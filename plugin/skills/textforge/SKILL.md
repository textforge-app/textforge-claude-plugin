---
name: "textforge"
description: "Draft, search, and manage emails via TextForge — human approval required before anything sends."
---

# TextForge Email Skill

Use this skill when the user wants to draft emails, read inbox threads, search email, or manage email follow-ups via TextForge. Every email draft requires human approval before it sends.

For email composition best practices, see the `email-writing` skill.

## Workflow

1. **Find context** — `search_threads_by_contact`, `search_messages`, or `get_thread`
2. **Import if needed** — `import_thread` to pull in threads not yet in TextForge
3. **Draft email** — `create_draft` with `bodyFormat: "Html"` (see `email-writing` skill for composition guidelines)
4. **Attach files** — `get_draft_attachment_upload_url` then upload via presigned URL
5. **User gets notified** — Slack, Discord, or webhook with draft preview
6. **User reviews/approves** — edit in TextForge UI, then send
7. **If edits needed** — use `update_draft`, never delete/recreate
8. **Check activity** — `get_draft_activity` to see full draft history

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
- `threadId` (string, optional): Reply to existing thread
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

### Thread Management (9 tools)

#### `mcp__textforge__list_threads`

List email threads with optional filtering.

- `limit` (number, optional): Max threads to return (default: 20)
- `label` (string, optional): Filter by label (e.g., `"INBOX"`, `"SENT"`)

#### `mcp__textforge__list_engaged_threads`

List threads where you have previously sent at least one message.

- `limit` (number, optional): Max threads (default: 20)

#### `mcp__textforge__get_thread`

Get full thread details including messages and threading headers for replies.

- `threadId` (string, required): The thread ID

#### `mcp__textforge__get_thread_by_external_id`

Look up a thread by the email provider's thread ID.

- `externalId` (string, required): Provider thread ID (e.g., Gmail thread ID)

#### `mcp__textforge__search_messages`

Search email using provider-delegated search syntax (e.g., Gmail search operators).

- `query` (string, required): Search query (e.g., `"from:alice@example.com after:2026-01-01"`)
- `limit` (number, optional): Max results (default: 20)

#### `mcp__textforge__search_threads_by_contact`

Find all threads involving a specific email address or domain.

- `email` (string, required): Contact email to search for

#### `mcp__textforge__sync_thread`

Sync a specific thread from the email provider to fetch new messages.

- `threadId` (string, required): The thread ID to sync

#### `mcp__textforge__sync_inbox`

Trigger a full inbox sync to discover new threads and messages.

- `fullSync` (boolean, optional): Force full history sync (default: false)

#### `mcp__textforge__import_thread`

Import a thread by provider thread ID into TextForge.

- `externalId` (string, required): Provider thread ID to import

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
