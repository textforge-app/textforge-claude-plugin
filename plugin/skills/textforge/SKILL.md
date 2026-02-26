---
name: "textforge"
description: "Draft, search, and manage emails via TextForge — human approval required before anything sends."
---

# TextForge Email Skill

Use this skill when the user wants to draft emails, read inbox threads, search email, or manage email follow-ups via TextForge. Every email draft requires human approval before it sends — nothing goes out automatically.

## Workflow

1. **Get context** — search or read threads to understand the conversation before drafting
2. **Draft** — call `create_draft` with well-formatted HTML content
3. **Confirm** — tell the user a draft is queued in TextForge for their review
4. **Follow-up loop** — when recipients reply, the user will be notified via their configured webhook; draft the next response when asked

Never tell the user an email was "sent" — it was drafted and queued for their approval.

## Tool Reference

### `mcp__textforge__create_draft`

Create an email draft for human approval.

**Parameters:**
- `subject` (string, required): Email subject line
- `body` (string, required): Email body
- `bodyFormat` (string): `"Html"` or `"Markdown"` — default `"Html"`
- `toRecipients` (string, required): Comma-separated recipient emails
- `ccRecipients` (string, optional): Comma-separated CC emails
- `bccRecipients` (string, optional): Comma-separated BCC emails
- `threadId` (string, optional): Existing thread ID to reply into
- `scheduledFor` (string, optional): ISO 8601 datetime for scheduled send

Drafts are automatically submitted for approval upon creation.

### `mcp__textforge__list_threads`

List recent inbox threads.

**Parameters:**
- `limit` (number, optional): Max threads (default: 20)
- `label` (string, optional): Filter by label, e.g. `"INBOX"`, `"SENT"`

### `mcp__textforge__get_thread`

Read the full content of a specific email thread.

**Parameters:**
- `threadId` (string, required): Thread ID

### `mcp__textforge__search_threads`

Search the inbox with Gmail-style query syntax.

**Parameters:**
- `query` (string, required): Search query, e.g. `"from:alice@example.com after:2026-01-01"`
- `limit` (number, optional): Max results (default: 20)

### `mcp__textforge__search_threads_by_contact`

Find threads by a contact email address.

**Parameters:**
- `email` (string, required): Contact email to search for

### `mcp__textforge__sync_inbox`

Trigger an immediate inbox sync to fetch new messages.

**Parameters:**
- `fullSync` (boolean, optional): Force full history sync (default: false)

## Email Composition Rules

### Formatting
- **Use `<br>` for line breaks**, not `<p>` tags — `<p>` creates double-spacing in most email clients
- Use `<br><br>` between paragraphs
- Set `bodyFormat: "Html"` unless the user explicitly wants Markdown
- Do not add an email signature — TextForge appends the user's configured signature automatically

### Tone and Style
- **No em dashes (—)** — they're an immediate LLM tell; use a comma or restructure the sentence
- **Be direct** — no "if you don't mind my asking" hedging
- **Be specific** — use names, product names, concrete dates; avoid vague placeholders
- **Address all recipients** — if someone is CC'd, acknowledge them by name in the body
- Match the user's voice, not a generic professional tone

### Subject Lines
Be specific and actionable:
- ❌ "Checking In"
- ✅ "[Acme Corp] Q2 Renewal — Action Needed by April 15"

## Updating Drafts

Use `update_draft` (not delete + recreate) when revisions are needed. Drafts in `PendingApproval` status cannot be deleted — only updated.

## What to Tell the User

After creating a draft:
> "I've queued a draft in TextForge for your review. You'll be notified via your configured channel — approve or edit it there before it sends."

Never say "I sent an email" or "the email was sent."
