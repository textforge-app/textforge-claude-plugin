# TextForge for Claude Code

<img src="https://raw.githubusercontent.com/textforge-app/openclaw-textforge/master/assets/textforge-logo.png" width="300" alt="TextForge Logo">

Official Claude Code plugin for [TextForge](https://textforge.net) — safely automate your Gmail with AI.

---

## What This Does

TextForge gives Claude access to your Gmail with a mandatory human approval step on every email. Claude can read threads, search your inbox, draft replies, manage attachments, and track draft activity — but **nothing sends without your review**.

This plugin connects Claude Code to the TextForge MCP server so you can ask Claude to help with email directly in your coding sessions. It includes 22 MCP tools and bundled skills for writing effective, human-sounding emails.

---

## Installation

### Option A — OAuth (No Token Required)

The fastest way to connect. Claude Code handles authentication automatically:

```bash
claude mcp add textforge --transport http https://textforge.net/mcp
```

Claude Code will open your browser to sign in and grant access. If you're new to TextForge, you'll be walked through account creation and setup before the connection completes — the full onboarding takes 5-15 minutes for new users, under 60 seconds for existing users.

Once authorized, your connection appears in [Settings → API Tokens](https://textforge.net/tokens) as **"MCP - Claude Code"**. You can revoke it there at any time.

### Option B — API Key (Plugin Marketplace)

Good for CI/headless environments or if you prefer explicit token management:

1. Sign up at [textforge.net](https://textforge.net/login) and generate a token at [textforge.net/tokens](https://textforge.net/tokens)
2. Set the environment variable in your shell config (`.bashrc`, `.zshrc`, etc.):
   ```bash
   export TEXTFORGE_API_KEY="your-token-here"
   ```
3. Add the plugin marketplace:
   ```
   /plugin marketplace add textforge-app/textforge-claude-plugin
   ```
4. Install the plugin:
   ```
   /plugin install textforge@textforge
   ```

---

## Available Tools (22)

### Draft Management

| Tool | What It Does |
|------|-------------|
| `create_draft` | Create an email draft and submit for approval |
| `list_drafts` | List drafts with optional status/recipient filtering |
| `get_draft` | Get draft details including status and approval history |
| `update_draft` | Update an existing draft's content or recipients |
| `submit_draft` | Submit a draft for approval (auto-submitted on create) |
| `get_draft_activity` | View complete draft activity log |
| `reject_draft` | Reject a pending draft |
| `delete_draft` | Delete a draft (only non-pending statuses) |

### Thread Management

| Tool | What It Does |
|------|-------------|
| `list_threads` | List recent email threads |
| `list_engaged_threads` | List threads where you've sent messages |
| `get_thread` | Read full thread with messages |
| `get_thread_by_external_id` | Look up thread by provider's thread ID |
| `sync_thread` | Sync a specific thread for latest messages |
| `sync_inbox` | Trigger full inbox sync |
| `search_messages` | Search with Gmail-style query syntax |
| `search_threads_by_contact` | Find threads by contact email |
| `import_thread` | Import a thread from email provider |

### Attachment Management

| Tool | What It Does |
|------|-------------|
| `list_message_attachments` | List attachments on a received message |
| `get_attachment_download_url` | Get presigned download URL for an attachment |
| `list_draft_attachments` | List attachments on a draft |
| `get_draft_attachment_upload_url` | Get presigned upload URL to attach a file |
| `remove_draft_attachment` | Remove an attachment from a draft |

---

## Bundled Skills

This plugin includes two skills beyond the core TextForge connector:

### Email Writing Guide

Best practices for composing effective, human-sounding emails. Includes:
- 6 core writing principles (be direct, use specific language, frame questions clearly)
- Technical formatting rules (HTML line breaks, no em dashes, no signatures)
- A mandatory AI pattern audit — 12 common LLM writing tells to catch before sending
- Follow-up timing framework
- Anti-pattern reference table
- Pre-send checklist

### TextForge (Core)

Full 22-tool reference with parameter documentation, attachment workflow, and draft lifecycle management.

---

## Example Usage

Once installed, try asking Claude:

> "Draft a follow-up email to the thread with sarah@example.com from last week"

> "Search my inbox for emails about the Q1 proposal and draft a status update reply"

> "List my 10 most recent inbox threads"

> "Download the PDF from the latest email from accounting and attach it to my reply draft"

> "Show me all my pending drafts and their approval status"

> "What happened with the draft I created yesterday? Show me the activity log"

Every draft Claude creates queues in TextForge for your review. You approve it — or you don't. Nothing sends automatically.

---

## How Approval Works

1. Claude calls `create_draft` with subject, body, and recipients
2. You get notified (Slack, Discord, or webhook — configured in TextForge)
3. You review and edit the draft in the TextForge UI
4. You approve — it sends from your Gmail with your signature

---

## Pricing

Both plans include a 7-day free trial. No credit card required to start.

| Plan | Price | Includes |
|------|-------|---------|
| **Solo** | $9.99/month | 20 drafts/day, 2 webhooks, 30-day inbox sync |
| **Pro** | $19.99/month | Unlimited drafts, 10 webhooks, full inbox history |

---

## Cross-Platform

TextForge works with any MCP-compatible client. Claude Code and Claude Desktop connect via OAuth. Cursor, Windsurf, VS Code, Cline, Zed, and Copilot CLI connect via API key. See the [full compatibility table](https://textforge.net/docs/mcp#authentication).

Also available as an [OpenClaw skill](https://github.com/textforge-app/openclaw-textforge).

---

## Learn More

- **[TextForge](https://textforge.net)** — Home
- **[MCP Documentation](https://textforge.net/docs/mcp)** — Full tool reference and setup guides
- **[Use Cases](https://textforge.net/use-cases)** — Sales follow-ups, support replies, inbox zero, and more

---

*Your AI drafts it. You approve it. Then it sends.*
