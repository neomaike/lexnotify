# LexNotify

> AI legal-intelligence platform that monitors 48 Brazilian courts in real time — reads case documents, transcribes PT-BR audio, and generates branded legal filings for law firms. In production with recurring revenue.

**Stack:** TypeScript · Next.js · Node.js · Supabase · OpenAI · Whisper  
**Status:** In production · Paying customers

---

## The problem

Brazilian law firms managing hundreds of active cases face a brutal operational bottleneck: monitoring court deadlines manually across dozens of different court systems, each with its own portal and notification format. A missed deadline at the wrong stage means a case lost by default — with potential liability for the firm.

Existing tools are either too generic (CRM software with no legal intelligence), too expensive (enterprise LegalTech priced for large firms), or simply don't cover the breadth of Brazilian courts.

---

## What I built

**Court monitoring**
- Scrapes and parses updates from 48 Brazilian court portals on a configurable schedule
- Extracts case events, deadlines, and procedural milestones with structured parsing
- Maps each event to the correct deadline rule under Brazilian procedural law (CPC)

**Document intelligence**
- Reads attached process documents (PDF, scanned images) using OCR + LLM extraction
- Identifies document type, parties, dates, and legally relevant facts automatically
- Summarizes long case histories into attorney-readable briefings

**Audio transcription**
- Transcribes PT-BR court hearing audio (MP3/WAV) using a fine-tuned Whisper pipeline
- Produces timestamped transcripts with speaker identification
- Extracts key statements and judicial decisions from hearing recordings

**Filing generation**
- Generates branded legal filings (petitions, responses, notifications) from structured case data
- Supports firm-specific templates with custom formatting and header/footer
- Output ready for direct submission — no reformatting needed

**Alert system**
- WhatsApp + email alerts for upcoming deadlines, new case events, and generated filings
- Configurable urgency levels (7 days / 3 days / same day)
- Per-attorney routing — each lawyer receives only their own caseload

---

## Architecture

```
Court portals (48)
      │
      ▼
Scraping layer (Node.js workers, per-court adapters)
      │
      ▼
Event parser → Deadline calculator (CPC rules)
      │
      ▼
Supabase (cases, events, deadlines, documents)
      │
      ├── Document pipeline (PDF → OCR → GPT-4 extraction)
      │
      ├── Audio pipeline (MP3/WAV → Whisper → transcript)
      │
      └── Filing generator (template engine → branded PDF)
              │
              ▼
       Alert dispatcher (WhatsApp API + email)
              │
              ▼
       Next.js dashboard (attorneys, deadlines, documents)
```

**Key design decisions:**
- Per-court adapter pattern: each of the 48 courts has its own parser module, isolated from the core pipeline — adding a new court doesn't touch existing logic
- Deadline rules as data: CPC deadline calculations are expressed as configurable rules, not hardcoded — allows non-dev updates when procedural rules change
- Async document processing: documents and audio are processed in background workers; the attorney sees a "processing" state in the UI and gets notified on completion

---

## Stack details

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14, TypeScript, Tailwind CSS |
| Backend API | Node.js, Hono |
| Database | Supabase (PostgreSQL + Storage) |
| LLM | OpenAI GPT-4o (extraction, generation) |
| Transcription | OpenAI Whisper (PT-BR fine-tuned) |
| Alerts | WhatsApp Business API, Resend |
| Infra | AWS EC2, Docker, GitHub Actions CI/CD |

---

## Status

- Deployed and serving paying law firm clients
- 48 court integrations live
- WhatsApp alert integration: in Meta approval queue

---

*Built by [Maike Henrique](https://github.com/neomaike) — [linkedin.com/in/neomaike](https://linkedin.com/in/neomaike) — [lexnotify.com](https://lexnotify.com)*
