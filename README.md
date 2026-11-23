<p align="center">
  <img src="https://raw.githubusercontent.com/mdKamrul-h/mdKamrul-h/main/assets/banner.svg" alt="Kamrul Hasan — AI Agent Engineer" width="100%"/>
</p>

<p align="center">
  <a href="https://linkedin.com/in/mdkamrulh"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
  <a href="https://fabricxai.com"><img src="https://img.shields.io/badge/fabricXai-0f172a?style=for-the-badge&logoColor=white" alt="fabricXai"></a>
  <a href="https://github.com/mdKamrul-h/mdKamrul-h/raw/main/assets/Kamrul_Hasan_CV.pdf"><img src="https://img.shields.io/badge/Resume-22c55e?style=for-the-badge&logo=readdotcv&logoColor=white" alt="Resume"></a>
  <a href="mailto:kamrul.sociofi@gmail.com"><img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"></a>
</p>

## About

I build **AI agent systems for real industries** — an AI-powered ERP for garment manufacturing, an agents-as-a-service platform underneath it, and a growing line of products where AI drafts and a person decides: verified answers in Bangla, building-permit plan review, hospital records, hiring.

My rule for production AI: **the model proposes, the system disposes.** Agents in my products never write to a live database — they draft into an approval queue, their tool calls are scoped by Postgres row-level security, their loops are step-capped and cost-capped, and anything deterministic (pricing math, pass/fail logic) is computed in code, not by the model. That's the difference between a demo and something a factory can run its orders on.

The runway behind it: an EE degree (BUET), an M.S. thesis on self-supervised medical imaging, 9 deep-learning specializations — and a **GPT-2 trained from scratch in 2020**, back when language models were a niche hobby.

## Flagship builds

### 🏭 [fabricXai](https://fabricxai.com) — The Garment Operating System &nbsp; <sub>live at `app.fabricxai.com`</sub>

AI-native ERP for garment factories and buying houses — replacing the Excel-and-WhatsApp workflow for RFQs, quoting, production, quality, and compliance. Built solo, shipped in waves.

- **"MARBIM" copilot** across every module, on a strict **propose → approve → commit** loop: AI output lands in a `pending_changes` queue that a human approves — nothing is sent or changed without sign-off.
- **Streaming tool-use agent** (Vercel AI SDK): step-capped, with every tool query running through the caller's session so **row-level security scopes what the agent can see** in a multi-tenant DB.
- **Role-based model registry** — `reasoning: claude-sonnet-4-5`, `fast: gemini-2.5-flash`. Right model for the job, swappable in one line, never hardcoded.
- **AI drafts, code computes**: the quoting engine has the LLM propose cost components, then computes FOB pricing deterministically. The math is real; the copilot narrates it.
- **Real ERP foundations**: 25 SQL migrations, 17 carrying RLS policies for per-company isolation; trigram indexes so search stays fast at scale; email-confirmed auth; ~135 React components across 18+ module screens.

`Next.js 16` `TypeScript` `Supabase / Postgres RLS` `Vercel AI SDK` `Claude` `Gemini` `Zod`

### 🤖 SocioFi Agents — agents-as-a-service platform &nbsp; <sub>in build</sub>

The engine room: enterprises compose multi-agent architectures on a visual canvas, deploy them, and govern every action.

- **Hand-rolled agent runtime** on the Anthropic SDK's native `tool_use` — **no LangChain, no LangGraph** (banned patterns in the repo). A pure-functional loop with an injected client, iteration caps, and per-token cost accounting.
- **Three-tier model routing** — orchestrator / domain / lightweight (Opus / Sonnet / Haiku): cheap models do cheap work, expensive ones only when reasoning demands it.
- **Governance as an engine, not a checkbox**: a JSON-logic rule engine resolves each action to `auto_approve` / `auto_reject` / `gate` — and **fails safe to human review** on any error. Agents can be paused mid-loop for approval.
- **Sandbox before production**: every architecture test-runs in an ephemeral sandbox with hard cost ($5), time (120s), and token caps, against mocked integrations.
- **Platform plumbing done properly**: encrypted secrets vault, HMAC-signed webhooks with exponential retry + auto-disable, append-only audit logs, bcrypt-hashed `sk_` API keys, pgvector-backed skill library with version pinning, TS + Python SDKs.

`Python 3.11` `FastAPI` `Anthropic SDK` `Celery + Redis` `Postgres + pgvector` `Supabase Realtime` `React Flow` `Docker`

### ☁️ SocioFi Cloud — managed hosting console &nbsp; <sub>in build</sub>

Provider-agnostic hosting (AWS / DigitalOcean / Hetzner / Vercel) for people who don't administer their own infra. A typed `Protocol` abstraction over every provider — deploys with blue-green rollback, log streaming, ACME SSL, TimescaleDB metrics, RBAC, and append-only audit. Deliberately *not* AI-native: one small model (Haiku) summarizes incidents, nothing more.

`FastAPI` `SQLAlchemy` `TimescaleDB` `Stripe` `Cloudflare` `boto3 / hcloud`

## More builds

The same rule, applied in other domains: verified answers, permit review, healthcare, hiring, content, community.

### ✅ Torun (তরুণ) — Bangla-first verified answers for Bangladesh &nbsp; <sub>in build · pre-pilot</sub>

Free, sourced answers to public-service questions, with a trust label on every one. One Flutter codebase for Android, iOS and web, on a FastAPI backend.

- **Hybrid retrieval** over crawled official sources: pgvector (HNSW) + full-text search fused by rank, boosted by source tier and recency. Below a similarity floor the answer is `NO_SOURCE` — the model is never asked to guess.
- **Citations enforced in code**: every sentence must cite passage IDs; uncited sentences, and numbers or dates absent from the cited passages, are dropped before the user sees them.
- **Trust labels are rules, not prompts** — `VERIFIED` / `DISAGREE` / `HEALTH` / `OUTDATED` / `NO_SOURCE` are assigned by deterministic code. Emergency red flags are caught before any model runs; retrieved text passes a prompt-injection filter.
- **Bangla ingestion done properly**: legacy Bijoy-font conversion to Unicode, Tesseract OCR for scans, and human approval for OCR passages that carry dates or amounts.
- **Budgets and audits**: per-provider daily spend caps that downgrade the model, a cache that holds only verified answers, gold-set retrieval evals, and a monthly stratified audit sample.

`Flutter` `FastAPI` `Postgres + pgvector` `Celery + Redis` `OpenTelemetry` `Prometheus / Grafana`

### 📐 Inzhora — AI-assisted plan check for California residential permits &nbsp; <sub>in build</sub>

Reads a permit submittal, checks it against the 2022 California Building Standards Code (Title 24), and drafts plan-check comments that a licensed reviewer accepts, edits or rejects in a Bluebeam-style markup interface.

- **Deterministic rules run before any LLM check** — if a check can be done without a model, it is.
- **No fabricated citations**: every finding carries a source, a confidence score and a sheet location; a citation must resolve in the code knowledge base or the finding is marked as judgment.
- **The reviewer decides**: only accepted comments reach the correction letter, acceptance is blocked while a finding has an unverified item, and findings are immutable once decided.
- **Structured output only**: tool-use with a JSON schema for every model call, every call logged (model, prompt hash, tokens, latency), and an eval harness that scores AI findings against comment letters real plan checkers issued.
- **Contract-first workflow app**: an OpenAPI contract checked on every request and response, with the full Playwright suite passing against the real backend.

`Python 3.12` `Anthropic SDK` `FastAPI` `Postgres + pgvector` `PyMuPDF` `React + pdf.js` `Fastify` `Playwright`

### 📡 [Antarious Signal](https://signal.antarious.com) — cited research to content, for brands &nbsp; <sub>pilot live</sub>

Brand setup → cited research → editable strategy → content calendar and drafts → monitoring, in Bangla and English.

- **Research with receipts**: web-search-backed research returned as Zod-validated structured output with sources.
- **Drafts that can't invent facts**: inputs are treated as untrusted, and prices, statistics and testimonials may not be made up. Strategy is assembled from saved reports with no extra model call.
- **The owner approves; nothing auto-posts.** Approved versions are immutable. Research is rate-limited per workspace and service-wide.

`Next.js 16` `TypeScript` `Supabase / Postgres RLS` `Zod` `Vitest` `Docker`

### 🐾 [Parul](https://parul.pet) — pet adoption, rescue and community &nbsp; <sub>Google Play open testing · TestFlight</sub>

A full consumer product, and deliberately not an AI one: feed, circles, adoption, rescue, lost-and-found, foster / transport / blood-donor services, vet consults and direct messages.

- **Realtime backend**: Django REST + Channels over Redis for websockets, Celery for scheduled work, Postgres, Cloudflare R2 media, FCM push.
- **Shipped through the stores**: Flutter app with Google and Apple sign-in, CI release builds, and 2,400+ automated tests across app and backend.

`Flutter` `Django REST + Channels` `Postgres` `Celery + Redis` `Cloudflare R2` `Firebase`

### Also live

| Product | What it is | Where the AI stops |
| --- | --- | --- |
| 🌍 [Global Job](https://jobglobal.sociofitechnology.com) | Job portal for Bangladesh and abroad: describe the job you want, see in plain words why each one fits. | AI reads what the job seeker writes; fixed rules do the matching. The AI never ranks people. |
| 🏥 [Chartis HIS](https://chartis.sociofitechnology.com) | Bangla-first cloud hospital and clinic system: front desk, consultation, billing, lab, pharmacy, wards. <sub>early access</sub> | An AI scribe drafts the consultation note, labelled "AI draft — unverified". The doctor signs every one. The AI never diagnoses. |

## How I build

```text
model proposes → human approves → code commits     never let the LLM write prod data
right-size the model                               Haiku/Flash for volume, Opus/Sonnet for reasoning
deterministic core, generative shell               pricing, AQL verdicts, state machines live in code
cap everything                                     steps, tokens, cost, time — agents get budgets
tenant isolation below the agent                   RLS in Postgres, not trust in the prompt
```

## Currently going deeper

- **MCP** — typed, safe model access to real systems; building servers for my own platforms next
- **Small & local models** — pushing more of the tiering down to open-weight models
- **Evals** — graduating from smoke-test verification to scored eval suites

## Stack

<p>
  <img src="https://img.shields.io/badge/Claude_&_Anthropic_SDK-D97757?style=flat-square&logo=anthropic&logoColor=white">
  <img src="https://img.shields.io/badge/Vercel_AI_SDK-000000?style=flat-square&logo=vercel&logoColor=white">
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white">
  <img src="https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white">
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white">
  <img src="https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white">
  <img src="https://img.shields.io/badge/PostgreSQL_+_RLS-4169E1?style=flat-square&logo=postgresql&logoColor=white">
  <img src="https://img.shields.io/badge/pgvector-336791?style=flat-square">
  <img src="https://img.shields.io/badge/Supabase-3FCF8E?style=flat-square&logo=supabase&logoColor=white">
  <img src="https://img.shields.io/badge/Celery_+_Redis-DC382D?style=flat-square&logo=redis&logoColor=white">
  <img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white">
  <img src="https://img.shields.io/badge/Flutter-02569B?style=flat-square&logo=flutter&logoColor=white">
</p>

## GitHub

<p align="center">
  <img height="165" src="https://github-readme-stats.vercel.app/api?username=mdKamrul-h&show_icons=true&hide_border=true&count_private=true&theme=tokyonight" alt="stats" />
  <img height="165" src="https://github-readme-stats.vercel.app/api/top-langs/?username=mdKamrul-h&layout=compact&hide_border=true&theme=tokyonight&langs_count=8" alt="top langs" />
</p>

---

<p align="center">Open to <b>AI Agent Engineer · Applied AI · Founding Engineer</b> roles.</p>
<p align="center"><code>$ agents that ship — and are safe to leave running ▊</code></p>

