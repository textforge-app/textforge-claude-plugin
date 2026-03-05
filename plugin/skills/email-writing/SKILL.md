---
name: email-writing
description: "Write effective, human-sounding emails via TextForge. Covers tone, structure, formatting, anti-patterns, and AI pattern detection."
---

# Email Writing Guide for TextForge

Use this skill whenever composing an email draft via TextForge. It covers writing principles, formatting rules, AI pattern detection, and a pre-send checklist. Every draft created with `mcp__textforge__create_draft` should follow these guidelines.

## Stop and Ask Before Drafting

If the inbound email contains open questions or requests for the sender's opinion, do NOT draft a reply until you have that input.

Common triggers:
- "I'd love to hear your thoughts on X"
- "What do you think about Y?"
- "Do you have any suggestions for Z?"

**Stop, surface the question to the user, and wait for their answer before composing.** Do not fill in generic responses. The whole point of the reply is to deliver the sender's actual perspective.

---

## Core Writing Principles

### 1. Be direct and confident

Don't hedge or apologize unnecessarily.

| Avoid | Use instead |
|-------|-------------|
| "If you don't mind my asking..." | "Can you share..." or just ask directly |
| "I was just wondering if perhaps..." | "Would you..." or "Can you..." |
| "even if it's just a quick note" | Remove - don't minimize the ask |
| "I wanted to circle back on..." | State the topic directly |

Write with confidence. Don't apologize for asking legitimate business questions.

### 2. Use specific, direct language

Call things what they are.

| Vague | Specific |
|-------|----------|
| "the non-renewal" | "your decision to cancel" or "canceling [Product]" |
| "your team" | "[Company Name] team" - use the actual company name |
| "We're always looking to improve" | Skip it - get to the point |
| "the project" | Name the actual project or product |

Specific language is clearer and more professional than generic phrases.

### 3. Frame questions with clear structure

Give recipients a mental framework for responding. Present a clear binary or structured choice rather than listing many possibilities.

**Bad - listing multiple possibilities:**
> "Was there something specific that didn't work? Features we were missing? Response times? Or was this more of a budget call?"

**Good - clear binary framework:**
> "Are you and the team still confident with [Product] to the point where you don't need support? Or were there issues with the plan itself?"

**Ask direct questions, don't imply them:**

| Passive | Direct |
|---------|--------|
| "You're welcome to join if you'd like" | "Would you like to join?" |
| "Feel free to let me know if that works" | "Does that time work for you?" |
| "We can discuss this further if needed" | "Should we schedule a call to discuss?" |

### 4. Write business-focused closings

Connect the ask to business value or the relationship.

| Weak | Strong |
|------|--------|
| "Your feedback would be really valuable" | "We'd love to learn what we could have done to keep your business" |
| "Let me know your thoughts" | "Please let me know - we'd love to learn from this" |
| "I'd appreciate any feedback" | "Your perspective would help us improve [specific thing]" |

### 5. Remove filler and get to the point

Cut unnecessary context and explanations.

**Over-explained:**
> "I wanted to reach out directly about the decision. I know the decision was communicated already, but I'm hoping to get your perspective as the technical lead on the project."

**Concise:**
> "I wanted to reach out about [Company]'s decision to cancel [Product]. I'm hoping to get your perspective."

Remove words that don't add meaning.

### 6. Address recipients directly

If someone is CC'd on the email, address them by name.

| Don't | Do |
|-------|----|
| "Please pass along our thanks to Tom" (when Tom is CC'd) | "Hi Sarah and Tom," (address both in greeting) |
| Ignore CC'd recipients | Speak to them directly in the body |

If they can read the email, talk to them directly.

---

## Technical Formatting Rules

### Line breaks and spacing
- **Use `<br>` for line breaks**, not `<p>` tags - `<p>` creates double-spacing in email clients
- Use `<br><br>` between paragraphs for natural spacing
- Set `bodyFormat: "Html"` unless the user explicitly wants Markdown

### No em dashes
- **Never use em dashes** - they are an immediate tell that an LLM wrote the email
- Use commas, periods, or rewrite the sentence instead
- Regular hyphens (-) for compound words are fine

### No email signatures
- Do NOT include signatures in draft bodies
- TextForge automatically appends the user's configured signature
- End with "Thanks," or "Best," and the sender's name

### Curly quotes
- Use straight quotes ("...") not curly/smart quotes
- Some email clients render curly quotes incorrectly
- Use `&amp;` for ampersands and `&#36;` for dollar signs in HTML bodies

---

## AI Pattern Detection - Mandatory Pre-Send Audit

**Before calling `mcp__textforge__create_draft`, audit the email body for these AI writing tells.** This is not optional. AI-sounding emails undermine trust with recipients.

This audit is derived from patterns documented in [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), adapted specifically for email.

### Patterns to catch and fix

**1. Significance inflation** - Puffing up importance with words like "pivotal", "testament", "vital role", "evolving landscape". Emails don't need grand framing. Just state the point.

**2. Copula avoidance** - Replacing "is" with "serves as", "stands as", "functions as", "represents". Use "is". It's fine.

**3. Negative parallelisms** - "It's not just about X, it's about Y." Just say what it's about.

**4. Rule of three** - Forcing ideas into groups of three ("reliable, scalable, and maintainable"). If you only need to say two things, say two things.

**5. Synonym cycling** - Varying words for no reason within the same email ("help" then "assist" then "support"). Pick one word and stick with it.

**6. Sycophantic openers** - "Great question!", "Absolutely!", "That's an excellent point." Remove. Just answer.

**7. Filler phrases** - "In order to" (use "to"), "due to the fact that" (use "because"), "at this point in time" (use "now"), "it is important to note that" (just state it).

**8. Excessive hedging** - "It could potentially possibly be argued that this might..." Just say what you mean.

**9. Generic conclusions** - "The future looks bright", "exciting times ahead", "we look forward to continuing this journey." End with a specific next step or just end.

**10. Chatbot artifacts** - "I hope this helps!", "Let me know if you'd like me to expand on any section", "Here is a summary of..." These are conversation artifacts, not email prose.

**11. Em dashes** - Already covered in formatting rules, but worth flagging again. Em dashes in emails are the single most recognizable LLM tell.

**12. Boldface overuse** - Don't bold every key term. Use bold sparingly for genuine emphasis, not for every acronym or product name.

### The audit process

After writing the email body but before creating the draft:

1. Re-read the body scanning for the 12 patterns above
2. Fix every instance found
3. Read the body out loud - does it sound like something a person would actually write?
4. If anything sounds "assembled" rather than "written", rewrite it

---

## Follow-Up Timing Framework

When scheduling follow-up emails or advising on timing:

| Situation | Follow-up in | Notes |
|-----------|-------------|-------|
| General check-in | 7 days | Standard cadence |
| Waiting on customer decision | 5-7 days | Give them time but stay engaged |
| Urgent / time-sensitive | 3-5 days | Renewals expiring, trials ending |
| Waiting on PO / payment | 7-14 days | Finance processes take time |
| They said "next week" | 7-10 days | Give them the week plus buffer |
| They said "end of month" | 1st of next month | Follow up when they said |
| Budget approval pending | 10-14 days | Corporate processes are slow |

**If unsure about timing, ask the user.** Don't guess.

---

## Anti-Pattern Reference

| Anti-pattern | Why it's bad | Fix |
|-------------|-------------|-----|
| "I hope this email finds you well" | Generic filler | Get directly to the point |
| "I've lost track of our conversation" | Unprofessional | Research thread history first using `search_threads_by_contact` or `get_thread` |
| Overly formal corporate language | Sounds like a template | Write like a person |
| Apologizing for emailing | Undermines confidence | Just state your purpose |
| Ending with just "Let me know" | Too vague | Specific call to action |
| "Don't hesitate to reach out" | Empty closing filler | End with "Thanks" and move on |
| Offering obvious options | Pads without information | Trust that the recipient is competent |
| "Please flag if you need more time" | Zero-value filler - states the obvious | Omit it. They know they can reach out. |

---

## Complete Email Template

```html
Hi [First Name],<br><br>

[Direct opening with clear context - one sentence]<br><br>

[Main question or request with clear framework - 1-2 sentences]<br><br>

[Business-focused closing that explains why you're asking]<br><br>

Thanks,<br>
[Your Name]
```

**Example:**
```html
Hi Tom,<br><br>

I wanted to reach out directly about Acme's decision to cancel their Pro plan. I know Lisa communicated the decision, but I'm hoping to get your perspective.<br><br>

Are you and the Acme team still happy with the product to the point where you don't need the Pro features? Or were there issues with the plan itself?<br><br>

Please let me know - we'd love to learn what we could have done to keep your business.<br><br>

Thanks,<br>
Sarah
```

---

## Pre-Send Checklist

Before calling `mcp__textforge__create_draft`:

- [ ] Subject line is specific and includes company/product name
- [ ] Greeting addresses all recipients by name (including CC'd)
- [ ] Opening is direct - no hedging or over-explaining
- [ ] Questions have clear structure - binary or framework, not a list of possibilities
- [ ] Closing is business-focused
- [ ] No em dashes anywhere
- [ ] Used `<br><br>` for paragraph spacing, not `<p>` tags
- [ ] No email signature in body
- [ ] Removed all filler words and phrases
- [ ] Used specific language - company names, product names, "cancel" not "non-renewal"
- [ ] **AI pattern audit completed** - scanned for all 12 patterns, fixed every instance
- [ ] Read the email out loud - sounds like a person wrote it

---

## Reply-All Guidelines

**Default to reply-all** unless there's a specific reason to narrow the conversation.

**Reply-all when:**
- Original email had multiple recipients
- CC'd parties need to stay in the loop
- All parties are relevant to the conversation

**Don't reply-all when:**
- Reaching out 1:1 for someone's specific perspective
- Following up privately after a group discussion
- Recipient explicitly asked to take it offline

---

## Subject Line Best Practices

Be specific and actionable:

| Generic | Specific |
|---------|----------|
| "Checking In" | "[Company] Pro Plan Renewal - April 2026" |
| "Quick Question" | "[Company] API Integration - Need Your Input" |
| "Following Up" | "[Company] License Decision - Your Feedback?" |

Subject lines should tell the recipient exactly what the email is about.

---

## Integration with TextForge Workflow

This skill works alongside the core `textforge` skill:

1. **Get context** - Use `search_threads_by_contact`, `search_messages`, or `get_thread` to understand the conversation
2. **Write the email** following the principles in this skill
3. **Run the AI pattern audit** (section above) on the body text
4. **Create the draft** via `mcp__textforge__create_draft`
5. **Tell the user** a draft is queued for their review - never say "sent"

Reference: [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) - CC BY-SA 4.0
