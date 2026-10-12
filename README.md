# n8n-automation

A self-hosted [n8n](https://n8n.io) automation stack that tracks my job search end to end — it reads my own inbox, classifies each message into an application stage, and keeps a private Google Sheet up to date so I never have to update a tracker by hand.

> **Personal project, single user.** There is no hosted version, no sign-up, and no second account. The Google authorization exists only so the software can reach the owner's own mailbox and spreadsheets. The pages in this repo (`index.html`, `privacy.html`) are the homepage and privacy policy required for the Google OAuth consent screen, served via GitHub Pages.

## What it does

- **Ingestion & classification** — a Gmail-triggered workflow reads incoming mail and decides whether a message is a real application event, then labels its stage (Applied → Viewed → Assessment → Interview → Offer, plus Rejected / Withdrawn / Closed as terminal states).
- **Lifecycle tracking** — each application is tracked as an identity with an ordered event history, not just a status cell. Identity is resolved by thread, then ATS/requisition ID, then job URL, then an exact company + role match — company alone is never enough, and ambiguous mail becomes a review item rather than a wrong update.
- **Discovery** — a daily workflow pulls new postings from job-board APIs, scores them against my resume keywords, and writes ranked results back to the tracker.
- **Alerts** — meaningful events (interview invitations, assessments, offers) trigger a private Telegram notification.
- **Review-first follow-ups** — a follow-up drafter can prepare Gmail drafts for stale applications. It only ever drafts; it never sends.

## Architecture

```
Gmail ──▶ n8n (Docker) ──▶ Google Sheets (tracker)
                │
                ├──▶ local LLM (Ollama) for message classification
                ├──▶ job-board APIs for discovery
                └──▶ Telegram bot for alerts
```

| Component        | Role |
|------------------|------|
| **n8n**          | Workflow engine, self-hosted in Docker |
| **Ollama**       | Local LLM for classifying messages (runs alongside n8n; no cloud model calls) |
| **Google Sheets**| System of record — application rows and a full event log |
| **Telegram**     | One-way alerts to the owner |
| **ngrok**        | Stable public URL for OAuth callbacks and the MCP endpoint |

### Design principles

- **100% local and free for inference.** Classification runs on a local Ollama model — no paid or cloud model provider is in the critical path.
- **The Sheet is the source of truth.** The viewer and drafts are downstream; nothing bypasses the single authoritative writer.
- **Fail loud, fail specific.** Runs report the exact stage that failed and whether the ingestion checkpoint advanced, rather than failing silently.
- **No unattended submission.** The project tracks and assists applications; it does not auto-submit forms on an applicant's behalf.

## Repository contents

| File           | Purpose |
|----------------|---------|
| `index.html`   | Project homepage (OAuth consent branding) |
| `privacy.html` | Privacy policy (OAuth consent requirement) |
| `README.md`    | This file |

Workflow definitions and local configuration are kept in the running n8n instance and are **not** committed here, since they contain account-specific identifiers and credentials.

## Privacy

All data stays inside the owner's own Google account and the machine the software runs on. Nothing is sold, shared, or sent to a third party. See [`privacy.html`](privacy.html) for the full policy.

## Contact

Questions: [Divyeshgutta1409@gmail.com](mailto:Divyeshgutta1409@gmail.com)
