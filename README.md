# Inbox Triage Agent

An AI agent built with n8n that reads incoming Gmail messages and decides on its own what to do with each one, instead of just classifying them.

## What it does

Most email automations sort messages into folders. This one goes a step further: it reads an email, reasons about what it actually needs, and picks one of four concrete actions:

- **REPLY** — drafts a response for simple questions it can answer confidently
- **SCHEDULE** — creates a tentative calendar event for meeting requests
- **ESCALATE** — flags anything unclear, high-stakes, or complaint-shaped for a human
- **TASK** — logs it as an action item for later instead of acting immediately

Every decision comes with a one-sentence reason, so the logic isn't a black box.

## Workflow overview

```
Gmail Trigger
      ↓
Gemini (decision + reasoning, returned as JSON)
      ↓
Parse Decision (Code node, validates and cleans the model's output)
      ↓
Route By Action (Switch: REPLY / SCHEDULE / ESCALATE / TASK)
      ↓
   ┌──────────────┬──────────────────┬─────────────┬──────────────┐
Draft Reply   Create Event      Escalate to Me   Log Task
(Gmail)       (Google Calendar) (Gmail)          (Google Sheets)
```

## Why it's safe to run unattended

- Replies are saved as **drafts**, never sent automatically — a human reviews before anything goes out
- Calendar events are created as **tentative**, not confirmed
- If the model's output fails to parse, the workflow **defaults to ESCALATE** rather than guessing
- Every action includes the model's stated reasoning, so decisions are auditable after the fact

## Tech stack

- **n8n** — workflow orchestration (low-code)
- **Google Gemini API** — reads the email and returns a structured decision
- **Gmail, Google Calendar, Google Sheets** — the tools the agent actually acts on, via OAuth2

## Setup

1. Import `workflow.json` into n8n (Import from File, or paste directly onto the canvas)
2. Connect your own Gmail, Google Calendar, and Google Sheets credentials (stripped from the export for security)
3. Add your Gemini API key in the **Gemini Reasoning** node
4. Point the **Log Task** node at your own Google Sheet (columns: Task, Source Email, From, Reasoning, Created)

## Screenshots

See `images/` for the workflow canvas and an example decision trace.

---

**Author:** Jay Khakhar
M.Sc. Artificial Intelligence (Germany)
