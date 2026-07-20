# Aditya Rao

AI Engineer — Voice AI Systems · LLM Applications · Production Backends

[Portfolio](https://resume-web-liard.vercel.app/) · [GitHub](https://github.com/MAdityaRao) · [Email](mailto:madityarao5@gmail.com)

---

## About

I build production AI applications while completing a B.Sc. in Data Analytics at Nitte (Deemed to be University), Karnataka. The degree gives me the statistical and ML foundation; the engineering skill comes from shipping systems that handle real callers, real bookings, and real data under real constraints.

Most of my work centers on one recurring problem: making voice AI agents behave reliably when multiple things happen at once — a guest booking a room while another checks out through a dashboard, an outbound dialer placing hundreds of calls without duplicating work across restarts, a library assistant that has to answer correctly instead of confidently. These aren't prompting problems — they're systems problems that happen to have an LLM in the loop.

I led backend development for an AI voice receptionist platform during my internship at Torq Designs (Nitte AIC), owning the database layer, booking logic, and production reliability of the voice agent, not just the demo path.

> Reliability is a feature, not a phase you get to later.

## What I Build

| Area | In Practice |
|---|---|
| Production Voice AI | LiveKit-based agents with STT/TTS pipelines, tool calling, sub-second turnaround — deployed as standalone services, not demo scripts |
| LLM Applications | Grounded, tool-using systems designed to fail safely — refusing or escalating rather than hallucinating |
| Backend APIs | FastAPI/Python services with async DB access, structured error handling, observability from day one |
| Database Architecture | Schema and transaction logic that stays correct under concurrent access, not just a single test call |
| AI Calling Systems | SIP-integrated outbound calling with retry logic, follow-up scheduling, structured outcome logging |
| Automation | Orchestration layers that poll, dispatch, and recover from failure with no manual intervention |
| ML Systems | Time-series forecasting (LSTM/GRU/RNN) — data cleaning through to a served prediction endpoint |

## Featured Projects

### AI Hotel Voice Receptionist — 24/7 autonomous booking agent

Handles real hotel operations — availability, bookings, cancellations, policy Q&A — entirely through a phone-style voice interface, writing to the same database the front desk uses in real time.

**The hard problem:** concurrency. Two callers requesting the same room can't both win. Every booking write goes through a single atomic SQL CTE — lock the room row, verify availability, insert, update — all in one transaction, so the database serializes racing callers instead of the application guessing.

**Also solved:** spoken phone-number normalization ("double nine," "triple five") into a canonical digit string before it ever touches the database.

`LiveKit Agents` `GPT-4o-mini` `Sarvam STT/TTS` `FastAPI` `PostgreSQL` `asyncpg` `OpenTelemetry`

Lesson: race conditions in voice systems don't show up in solo testing — I only found this one by deliberately load-testing with concurrent simulated callers.

Repo: https://github.com/MAdityaRao/torq_web_agent

### Hotel Management Dashboard — zero-sync-layer operational view

A Next.js dashboard sharing its database directly with the voice receptionist — a voice booking appears on staff screens instantly, because there's no sync layer to begin with.

Audited an inherited codebase and fixed 23 production issues: ghost bookings from non-atomic writes, incorrect occupancy from miscounted cancellations, and checkout logic that failed to release rooms.

`Next.js` `TypeScript` `TailwindCSS` `Drizzle ORM` `Neon PostgreSQL` `Recharts` `ISR`

Repo: https://github.com/MAdityaRao/torq_web_agent

### AI Outbound Sales Dialer — fully automated calling pipeline

An orchestrator polls pending contacts, launches a LiveKit agent per call, dials through a Plivo SIP trunk, runs the conversation, and writes a structured outcome back — no manual dispatch step.

Each call is an independently deployable agent instance, so one crashed call can't take down the orchestrator. Retry/max-attempt state lives in PostgreSQL, not memory, so an orchestrator restart never causes duplicate dials.

Capabilities: mid-call English/Hindi language switching, objection-handling flows, automated follow-up scheduling, structured outcome recording (`answered` / `no_answer` / `voicemail` / `follow_up` / `converted` / `failed`).

`Python` `LiveKit SIP` `Plivo` `GPT-4o-mini` `Sarvam STT/TTS` `PostgreSQL` `asyncpg`

### NITK Library Voice Assistant — Ritu (voice) + Aria (chat)

Deployed on a physical kiosk at the NITK Central Library entrance. Recommends academic databases by subject, answers policy/hours/fines/thesis questions, and directs users to physical sections — through both a voice interface and a text interface sharing the same backend logic.

Tool access is scoped tightly to structured library data — the agent either retrieves a real answer or says it doesn't know, rather than answering confidently from general knowledge. VAD tuned specifically for a noisy public kiosk environment.

`LiveKit Agents` `DeepSeek Chat` `Sarvam STT/TTS` `Silero VAD` `Next.js`

Agent contract: `AGENT_NAME = "nitk-library-agent"` — dispatch name embedded directly in the LiveKit token, so browser-connect and agent-invite happen as one atomic step.

### Insurance Voice Assistant — IRDAI-grounded policy servicing

Handles policy lookup, claim status, waiting-period explanations, cashless claim guidance, and NCB queries — with proactive escalation: denied claims and regulatory disputes route to a human by default, not on request.

Deliberately tuned to a calmer, more measured conversational tone than the hotel/sales agents — a support context where getting things wrong has real consequences.

`LiveKit Agents` `GPT-4o-mini` `Deepgram STT` `Deepgram Aura TTS` `PostgreSQL`

Repo: https://github.com/MAdityaRao/Mvp_ins

### Resume-Grounded Interview Agent — sub-500ms voice interviewer

Answers strictly from a candidate's resume and target JD, with hallucination prevention as the central design constraint, not an afterthought. Accepts a live JD over a data channel and adapts mid-conversation.

Hit sub-500ms round-trip latency using Cartesia for both STT and TTS, and kept grounding lightweight — direct retrieval over the resume/JD text instead of a heavier RAG pipeline.

`LiveKit Agents` `GPT-4o-mini` `Cartesia STT/TTS` `Docker` `Next.js`

Repo: https://github.com/MAdityaRao/Resume_agent · Live demo: https://resume-web-liard.vercel.app/

### Commodity Price Forecasting — LSTM vs GRU vs SimpleRNN

Time-series forecasting for Karnataka commodity markets — coffee (Arabica/Robusta, IEEE paper with faculty co-author) and arecanut (Adike/Patora grades, Karkala and Udupi APMC). Identical preprocessing, splits, and scaling across all three architectures for a fair comparison; results served through a lightweight Flask/FastAPI prediction endpoint instead of staying notebook-bound.

`TensorFlow` `Keras` `Pandas` `Scikit-learn` `FastAPI` `Flask`

Finding: on small, regionally-specific datasets, GRU's lower parameter count mattered more than architectural sophistication — model choice should follow the data's size and noise profile, not current popularity.

LSTM repo: https://github.com/MAdityaRao/arecanut_lstm · RNN repo: https://github.com/MAdityaRao/arecanut_rnn

## Architecture Philosophy

Every voice AI system I build follows the same shape:

```
Frontend -> LiveKit Token API -> Independent Voice Agent -> Shared PostgreSQL <- Dashboard
```

- The agent is never embedded in the frontend. It's its own deployable process — I can patch a prompt or roll back a bad change without touching the frontend at all.
- The database is the single source of truth. No sync job, no webhook relay, no eventual-consistency window. If it's in the DB, every consumer sees it immediately.
- Correctness lives at the database layer. Atomic CTEs hold regardless of how many agent instances are running.
- Failure is isolated. A crash in one conversation can't take down the token API or other in-flight calls.

This same shape — frontend, token API, independent agent, shared database, dashboard — powers the hotel receptionist, the outbound dialer, the insurance assistant, and the library kiosk. The domain logic changes; the shape underneath doesn't.

## Tech Stack

**Voice AI:** LiveKit Agents, Sarvam STT/TTS, Deepgram, Cartesia, Silero VAD

**Backend:** Python, FastAPI, Flask, asyncpg

**Frontend:** Next.js, React, TypeScript, TailwindCSS

**Machine Learning:** TensorFlow, Keras, scikit-learn, Pandas

**Data & Infra:** PostgreSQL, Neon, Docker, Vercel, AWS, Git

## Public Repositories

| Repo | What it is |
|---|---|
| [`resume_web`](https://github.com/MAdityaRao/resume_web) | Portfolio site — Next.js + live voice agent "Sharanya" |
| [`torq_web_agent`](https://github.com/MAdityaRao/torq_web_agent) | Torq hotel voice receptionist and management dashboard |
| [`Mvp_ins`](https://github.com/MAdityaRao/Mvp_ins) | AI insurance voice agent platform ("Arria") |
| [`Resume_agent`](https://github.com/MAdityaRao/Resume_agent) | Resume-grounded voice interview agent |
| [`arecanut_lstm`](https://github.com/MAdityaRao/arecanut_lstm) | Arecanut price forecasting — LSTM model |
| [`arecanut_rnn`](https://github.com/MAdityaRao/arecanut_rnn) | Arecanut price forecasting — SimpleRNN comparison |
| [`nurse_note_ai`](https://github.com/MAdityaRao/nurse_note_ai) | Nursing/clinical note AI tooling |
| [`pdubelman_temple`](https://github.com/MAdityaRao/pdubelman_temple) | Temple website build |
| [`mar-lang`](https://github.com/MAdityaRao/mar-lang) / [`marlang_web`](https://github.com/MAdityaRao/marlang_web) | Language project and web frontend |
| [`farm_finance`](https://github.com/MAdityaRao/farm_finance) | Agricultural finance project |
| [`java_dsa`](https://github.com/MAdityaRao/java_dsa) | Data structures and algorithms practice, Java |

## GitHub Metrics

![Aditya's GitHub metrics — isometric contribution calendar, activity, languages, and topics](https://raw.githubusercontent.com/MAdityaRao/MAdityaRao/main/github-metrics.svg)

Refreshed daily via [lowlighter/metrics](https://github.com/lowlighter/metrics).

## Engineering Principles

- **Clean code over clever code.** A slightly longer function that's obvious to debug at 2am beats a compact one I have to re-derive every time.
- **Observability from the start.** OpenTelemetry tracing ships with the feature, not after the first incident.
- **Transactions as a design tool.** I default to atomic writes for shared state because I don't trust myself to spot a race condition by eyeballing code.
- **Shared database over sync layers.** Every extra sync mechanism is another place for state to drift.
- **Ship simple, refine under real load** — rather than pre-optimizing for failure modes that may never occur.
- **Avoid over-engineering.** Independent instances, atomic transactions, and tracing exist because I've watched the alternative break in production, not because they're impressive.

## Contact

Working on voice AI that needs to hold up under real call volume, LLM applications where hallucination isn't acceptable, backend systems with concurrent writes that must stay correct, or AI calling infrastructure — reach out.

Email: madityarao5@gmail.com · Portfolio: https://resume-web-liard.vercel.app/ · GitHub: https://github.com/MAdityaRao