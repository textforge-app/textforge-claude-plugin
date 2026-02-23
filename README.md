# TextForge for Claude Code

<img src="https://textforge.net/images/og-image.png" width="300" alt="TextForge Logo">

Official Claude Code plugin for [TextForge](https://textforge.net) — safely automate your Gmail with AI.

---

## What This Does

TextForge gives Claude access to your Gmail with a mandatory human approval step on every email. Claude can read threads, search your inbox, and draft replies — but **nothing sends without your review**.

This plugin connects Claude Code to the TextForge MCP server so you can ask Claude to help with email directly in your coding sessions.

---

## Installation

### 1. Get Your TextForge API Token

1. Sign up at [textforge.net](https://textforge.net/login)
2. Generate a token at [textforge.net/settings/tokens](https://textforge.net/settings/tokens)
3. Set the environment variable in your shell config (`.bashrc`, `.zshrc`, etc.):
   ```bash
   export TEXTFORGE_API_KEY="your-token-here"
   ```

### 2. Add the Plugin Marketplace

```
/plugin marketplace add textforge-app/textforge-claude-plugin
```

### 3. Install the Plugin

```
/plugin install textforge@textforge
```

That's it. Claude Code will now have access to TextForge tools.

---

## Available Tools

Once installed, Claude has access to these MCP tools:

| Tool | What It Does |
|------|-------------|
| `mcp__textforge__create_draft` | Create an email draft for your approval |
| `mcp__textforge__list_threads` | List recent inbox threads |
| `mcp__textforge__get_thread` | Read a full email thread |
| `mcp__textforge__search_threads` | Search inbox with Gmail-style queries |
| `mcp__textforge__search_threads_by_contact` | Find threads by contact email |
| `mcp__textforge__sync_inbox` | Trigger an inbox sync |

---

## Example Usage

Once installed, try asking Claude:

> "Draft a follow-up email to the thread with sarah@example.com from last week"

> "Search my inbox for emails about the Q1 proposal and draft a status update reply"

> "List my 10 most recent inbox threads"

Every draft Claude creates queues in TextForge for your review. You approve it — or you don't. Nothing sends automatically.

---

## How Approval Works

1. Claude calls `create_draft` with subject, body, and recipients
2. You get notified (Slack, Discord, or webhook — configured in TextForge)
3. You review and edit the draft in the TextForge UI
4. You approve — it sends from your Gmail with your signature

---

## Pricing

| Plan | Price | Includes |
|------|-------|---------|
| **Solo** | $9.99/month | 20 drafts/day, 2 webhooks, 30-day inbox sync |
| **Pro** | $19.99/month | Unlimited drafts, 10 webhooks, full inbox history |

---

## Learn More

- **[TextForge](https://textforge.net)** — Home
- **[MCP Documentation](https://textforge.net/docs/mcp)** — Full tool reference and setup guides
- **[Use Cases](https://textforge.net/use-cases)** — Sales follow-ups, support replies, inbox zero, and more

---

*Your AI drafts it. You approve it. Then it sends.*
