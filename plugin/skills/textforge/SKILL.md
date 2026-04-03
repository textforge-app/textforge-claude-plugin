---
name: "textforge"
description: "Draft, search, and manage emails via TextForge — human approval required before anything sends."
---

# TextForge Email Skill

Use this skill when the user wants to draft emails, read inbox threads, search email, or manage email follow-ups via TextForge. Every email draft requires human approval before it sends.

For email composition best practices, see the `email-writing` skill.

## ID Types

TextForge uses two types of thread identifiers:

| ID Type | Format | Example | Where It Appears |
|---------|--------|---------|-----------------|
| Internal ID (`id`) | GUID | `3fa85f64-5717-4562-b3fc-2c963f66afa6` | `list_threads`, `list_engaged_threads`, `search_threads_by_contact`, matched results in `search_messages` |
| Provider ID (`externalThreadId`) | Hex string | `19d2243fcda1917c` | `search_messages` (unmatched results), email headers |

- Use **internal IDs** with: `get_thread`, `sync_thread`, `create_draft` (threadId param)
- Use **provider IDs** with: `get_thread_by_external_id`, `import_thread`
- **Never pass a provider ID to `get_thread`** — it requires GUID format and will error

## Workflow

1. **Find context** — `search_messages`, `search_threads_by_contact`, `list_threads`, or `list_recent_messages`
   - `search_messages` returns two groups: **matched threads** (already in TextForge, have an `id` GUID) and **unmatched provider threads** (only have `externalThreadId`)
   - `list_recent_messages` returns individual messages filtered by date — useful for "what came in recently?"
2. **Import if needed** — For unmatched results from `search_messages`, use `import_thread` with the `externalThreadId` to pull the thread into TextForge. This returns the internal `id`.
3. **Read thread** — `get_thread` with the internal `id` (GUID format). Do NOT pass provider thread IDs to this tool — use `get_thread_by_external_id` for those.
4. **Draft email** — `create_draft` with `bodyFormat: "Html"` (see `email-writing` skill for composition guidelines)
5. **Attach files** — `get_draft_attachment_upload_url` then upload via presigned URL
6. **User gets notified** — Slack, Discord, or webhook with draft preview
7. **User reviews/approves** — edit in TextForge UI, then send
8. **If edits needed** — use `update_draft`, never delete/recreate
9. **Check activity** — `get_draft_activity` to see full draft history

Never tell the user an email was "sent" — it was drafted and queued for their approval.

## Quick Formatting Reference

- Use `<br>` for line breaks, not `<p>` tags
- Use `<br><br>` between paragraphs
- Set `bodyFormat: "Html"` unless the user explicitly wants Markdown
- No em dashes — they're an immediate LLM tell
- No email signatures — TextForge appends the user's configured signature

For the full writing guide, anti-pattern reference, and AI pattern audit, see the `email-writing` skill.

---

## Available Tools

### Draft Management (8 tools)

#### `mcp__textforge__create_draft`

Create an email draft for human approval.

- `subject` (string, required): Email subject line
- `body` (string, required): Email body
- `bodyFormat` (string): `"Html"` or `"Markdown"` (default: `"Html"`)
- `toRecipients` (string, required): Comma-separated recipient emails
- `ccRecipients` (string, optional): Comma-separated CC emails
- `bccRecipients` (string, optional): Comma-separated BCC emails
- `threadId` (string, optional): Internal TextForge thread ID (GUID) to reply to an existing thread
- `scheduledFor` (string, optional): ISO 8601 datetime for scheduled send

Drafts are automatically submitted for approval upon creation.

#### `mcp__textforge__list_drafts`

List email drafts with optional filtering by status or recipient.

- `status` (string, optional): Filter by status (e.g., `"PendingApproval"`, `"Approved"`, `"Sent"`)
- `recipient` (string, optional): Filter by recipient email
- `limit` (number, optional): Max results (default: 20)

#### `mcp__textforge__get_draft`

Get detailed information about a specific draft including body, recipients, and status.

- `draftId` (string, required): The draft ID

#### `mcp__textforge__update_draft`

Update an existing draft's content. Only works on editable drafts. **Always use this instead of deleting/recreating** — drafts in `PendingApproval` status cannot be deleted.

- `draftId` (string, required): The draft ID to update
- `subject` (string, optional): Updated subject
- `body` (string, optional): Updated body
- `bodyFormat` (string, optional): `"Html"` or `"Markdown"`
- `toRecipients` (string, optional): Updated recipients
- `ccRecipients` (string, optional): Updated CC
- `bccRecipients` (string, optional): Updated BCC

#### `mcp__textforge__submit_draft`

Submit a draft for human approval. Rarely needed since `create_draft` auto-submits.

- `draftId` (string, required): The draft ID

#### `mcp__textforge__get_draft_activity`

Get the complete activity history for a draft (created, edited, submitted, approved, rejected, sent).

- `draftId` (string, required): The draft ID

#### `mcp__textforge__reject_draft`

Reject a draft that is pending approval.

- `draftId` (string, required): The draft ID
- `reason` (string, optional): Rejection reason

#### `mcp__textforge__delete_draft`

Permanently delete a draft. Only works on non-sendable statuses.

- `draftId` (string, required): The draft ID

### Thread Management (10 tools)

#### `mcp__textforge__list_threads`

List email threads with optional filtering. Returns threads with `hasUnreadReply`, `lastInboundAt`, and `lastOutboundAt` fields.

- `participantEmail` (string, optional): Filter by participant email address
- `page` (number, optional): Page number, 1-based (default: 1)
- `pageSize` (number, optional): Items per page (default: 25, max: 100)

#### `mcp__textforge__list_engaged_threads`

List threads where you have previously sent at least one message, ordered by most recent activity. Includes `hasUnreadReply` (true when the latest message is inbound — the ball is in your court) plus `lastInboundAt` and `lastOutboundAt` timestamps. Use this to find conversations that need a response.

- `page` (number, optional): Page number, 1-based (default: 1)
- `pageSize` (number, optional): Items per page (default: 25, max: 100)

#### `mcp__textforge__get_thread`

Get full thread details including messages and threading headers for replies. Requires an internal TextForge thread ID (GUID format).

- `threadId` (string, required): Internal TextForge thread ID (GUID format, e.g., `"3fa85f64-5717-4562-b3fc-2c963f66afa6"`). Use the `id` field from `list_threads`, `search_threads_by_contact`, or matched `search_messages` results. Do NOT pass provider/Gmail thread IDs here — use `get_thread_by_external_id` for those.
- `maxMessages` (number, optional): Max messages to return (default: 5, set to 0 for all)
- `stripQuotedReplies` (boolean, optional): Strip quoted reply content (default: true)

#### `mcp__textforge__get_thread_by_external_id`

Look up a thread by the email provider's thread ID. Use this with provider thread IDs from `search_messages` results or email headers. If not found, use `import_thread` to import it first.

- `externalThreadId` (string, required): Provider thread ID (e.g., Gmail thread ID like `"19d2243fcda1917c"`)
- `maxMessages` (number, optional): Max messages to return (default: 5, set to 0 for all)
- `stripQuotedReplies` (boolean, optional): Strip quoted reply content (default: true)

#### `mcp__textforge__search_messages`

Search emails using Gmail-style query syntax. Returns two result groups:
- `threads` — already in TextForge, have `id` (GUID) and full metadata (subject, participants, timestamps) for use with `get_thread`
- `unmatchedProviderThreads` — not yet imported, have `externalThreadId` only — use `import_thread` first

- `query` (string, required): Search query (e.g., `"from:alice@example.com after:2026-01-01"`, `"subject:invoice"`, `"has:attachment"`)
- `maxResults` (number, optional): Max results (default: 100, max: 500)
- `pageToken` (string, optional): Page token for pagination (from previous response)

#### `mcp__textforge__search_threads_by_contact`

Find all threads involving a specific email address or domain. Returns threads with full metadata including `hasUnreadReply`.

- `email` (string, required): Contact email to search for (e.g., `"john@example.com"` or `"@example.com"` for domain)

#### `mcp__textforge__sync_thread`

Sync a specific thread from the email provider to fetch new messages. Requires an internal TextForge thread ID (GUID).

- `threadId` (string, required): Internal TextForge thread ID (GUID format)

#### `mcp__textforge__sync_inbox`

Trigger a full inbox sync to discover new threads and messages.

#### `mcp__textforge__import_thread`

Import a thread from the email provider by its external thread ID (e.g., from the `unmatchedProviderThreads` array in `search_messages` results). Returns the thread with its internal `id` (GUID) for use with `get_thread`, `create_draft`, etc. Idempotent: if already imported, syncs instead.

- `externalThreadId` (string, required): Provider thread ID to import

#### `mcp__textforge__list_recent_messages`

List recent email messages across all threads, filtered by date and optionally by direction. Returns individual messages (not threads) with thread context (`threadId`, `threadSubject`) so you can navigate to the full thread via `get_thread`. Use this to quickly find recent inbound emails without paging through threads.

- `since` (string, required): Return messages after this date/time (ISO 8601, e.g., `"2026-03-25T00:00:00Z"`)
- `direction` (string, optional): Filter by direction: `"inbound"`, `"outbound"`, or omit for all
- `limit` (number, optional): Max results (default: 50, max: 100)
- `newestFirst` (boolean, optional): Sort order (default: true)

### Attachment Management (5 tools)

#### `mcp__textforge__list_message_attachments`

List attachments for a received email message.

- `messageId` (string, required): The message ID

#### `mcp__textforge__list_draft_attachments`

List attachments currently attached to a draft.

- `draftId` (string, required): The draft ID

#### `mcp__textforge__get_attachment_download_url`

Get a presigned URL to download an attachment. URL expires in 10 minutes.

- `attachmentId` (string, required): The attachment ID

#### `mcp__textforge__get_draft_attachment_upload_url`

Get a presigned URL to upload an attachment to a draft.

- `draftId` (string, required): The draft ID
- `fileName` (string, required): Name of the file to upload
- `contentType` (string, required): MIME type (e.g., `"application/pdf"`)

#### `mcp__textforge__remove_draft_attachment`

Remove an attachment from a draft email.

- `draftId` (string, required): The draft ID
- `attachmentId` (string, required): The attachment ID

---

## Attachment Workflow

To attach a file to a draft:

1. Create the draft via `create_draft`
2. Get an upload URL via `get_draft_attachment_upload_url` with the draft ID, filename, and content type
3. Upload the file via HTTP PUT to the returned `uploadUrl`:
   ```bash
   curl -X PUT "<uploadUrl>" \
     -H "Content-Type: application/pdf" \
     --data-binary "@/path/to/file.pdf"
   ```
4. The upload URL is single-use and expires in 10 minutes

To access received attachments:

1. Use `list_message_attachments` on a message ID
2. Use `get_attachment_download_url` to get a presigned download URL

Common attachment types: PDF, images (PNG/JPG), documents (DOCX), spreadsheets (XLSX).

---

## What to Tell the User

After creating a draft:
> "I've queued a draft in TextForge for your review. You'll be notified via your configured channel — approve or edit it there before it sends."

Never say "I sent an email" or "the email was sent."

## Pricing

Both plans include a 7-day free trial. No credit card required to start.

| Plan | Price | Includes |
|------|-------|---------|
| **Solo** | $9.99/month | 20 drafts/day, 2 webhooks, 30-day inbox sync |
| **Pro** | $19.99/month | Unlimited drafts, 10 webhooks, full inbox history |
