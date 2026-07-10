<div align="center">

# Aditya Rao

### AI Engineer — Voice AI Systems, LLM Applications, Production Backends

I design and ship real-time voice AI agents and the backend systems that keep them consistent under concurrent load. My work lives in production, not in notebooks.

[![Portfolio](https://img.shields.io/badge/Portfolio-resume--web--liard.vercel.app-000000?style=flat-square)](https://resume-web-liard.vercel.app/)
[![GitHub](https://img.shields.io/badge/GitHub-MAdityaRao-181717?style=flat-square&logo=github)](https://github.com/MAdityaRao)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://linkedin.com/in/YOUR-LINKEDIN-HANDLE)
[![Email](https://img.shields.io/badge/Email-madityara5%40gmail.com-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:madityara5@gmail.com)

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![LiveKit](https://img.shields.io/badge/LiveKit-FF3C3C?style=flat-square)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=flat-square&logo=openai&logoColor=white)
![DeepSeek](https://img.shields.io/badge/DeepSeek-4D6BFE?style=flat-square)
![TensorFlow](https://img.shields.io/badge/TensorFlow-FF6F00?style=flat-square&logo=tensorflow&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white)

</div>

---

## Table of Contents

- [About Me](#about-me)
- [What I Build](#what-i-build)
- [Featured Projects](#featured-projects)
  - [AI Hotel Voice Receptionist](#ai-hotel-voice-receptionist)
  - [Hotel Management Dashboard](#hotel-management-dashboard)
  - [AI Outbound Sales Dialer](#ai-outbound-sales-dialer)
  - [NITK Library Voice Assistant (Ritu)](#nitk-library-voice-assistant-ritu)
  - [Insurance Voice Assistant](#insurance-voice-assistant)
  - [Resume-Grounded Interview Agent](#resume-grounded-interview-agent)
  - [Commodity Price Forecasting](#commodity-price-forecasting)
- [Architecture Philosophy](#architecture-philosophy)
- [Technical Skills](#technical-skills)
- [Engineering Principles](#engineering-principles)
- [Open Source](#open-source)
- [Currently Learning](#currently-learning)
- [Let's Build Something](#lets-build-something)

---

## About Me

I build production AI applications while completing a B.Sc. in Data Analytics at Nitte (Deemed to be University), Karnataka. The degree gives me the statistical and ML foundation; the actual engineering skill comes from shipping systems that handle real callers, real bookings, and real data under real constraints.

Most of my work centers on one recurring problem: making voice AI agents behave reliably when multiple things happen at once. A guest calls to book a room while another guest is checking out through a dashboard. An outbound dialer needs to place hundreds of calls without duplicating work across restarts. A library assistant has to answer correctly instead of confidently. These aren't LLM-prompting problems — they're systems problems that happen to have an LLM in the loop.

I led backend development for an AI voice receptionist platform during an internship at Torq Designs (Nitte AIC), where I was responsible for the database layer, the booking logic, and the reliability of the voice agent in production — not just the demo path.

I care about systems that stay correct when things go wrong: crashed processes, concurrent writes, dropped calls, malformed STT output. Most of the projects below exist because an earlier, simpler version of them broke in exactly one of these ways.

---

## What I Build

| Area | What that means in practice |
|---|---|
| **Production Voice AI** | LiveKit-based agents with STT/TTS pipelines, tool calling, and sub-second turnaround, deployed as standalone services rather than demo scripts |
| **LLM Applications** | Grounded, tool-using LLM systems designed to fail safely — refusing or escalating rather than hallucinating |
| **Backend APIs** | FastAPI/Python services with async database access, structured error handling, and observability built in from day one |
| **Database Architecture** | Schema design and transaction logic that stays correct under concurrent access, not just under a single test call |
| **AI Calling Systems** | SIP-integrated outbound calling with retry logic, follow-up scheduling, and structured outcome logging |
| **Automation** | Orchestration layers that poll, dispatch, and recover from failure without manual intervention |
| **ML Systems** | Time-series forecasting pipelines (LSTM/GRU/RNN) taken from data cleaning through to a served prediction endpoint |

---

## Featured Projects

### AI Hotel Voice Receptionist

An AI voice receptionist that handles real hotel operations — availability lookups, bookings, cancellations, policy questions — entirely through a phone-style voice interface, with the same reliability guarantees I'd want from a hand-written booking API.

**Why it exists**

Hotel front desks lose bookings during peak call volume, and third-party call-answering services don't have write access to live inventory. This agent talks to the same database the front desk uses, in real time, so nothing is out of sync.

**Engineering challenges solved**

The hardest problem wasn't the conversation — it was concurrency. Two callers requesting the same room type in the same window can't both succeed. I solved this by making every booking write go through a single atomic SQL CTE: lock the target room row, verify availability, insert the reservation, and update room state in one transaction. If two callers race for the same room, the database serializes them instead of the application code trying to — which means the guarantee holds even if I add more agent instances later.

The second problem was voice-specific: guests give phone numbers by speaking digits, and STT output for numbers is inconsistent ("double nine," "nine nine," "triple five"). I built a normalization layer that converts spoken number patterns into a canonical digit string before it ever reaches the database, so lookups against existing reservations don't silently fail on formatting differences.

**Key features**

- 24/7 conversational handling of availability, booking, cancellation, and lookup
- Hotel policy Q&A grounded in structured policy data, not free-form generation
- Human escalation path for requests outside the agent's scope
- Spoken phone-number normalization before persistence
- Atomic SQL CTE transactions preventing double bookings under concurrent callers
- End-to-end OpenTelemetry tracing across the agent, tool calls, and database layer

**Architecture highlights**

The agent runs as an independently deployable service, not as a function inside the frontend. It talks to PostgreSQL over `asyncpg` using connection pooling, and every tool call the LLM makes is traced with OpenTelemetry — so when a booking fails, I can see exactly which span (STT transcription, tool execution, or database write) caused it, instead of guessing from logs.

**Implementation detail worth calling out**

The booking CTE does lock → verify → insert → update in a single round trip specifically to avoid a check-then-act race condition — the classic bug where two processes both check availability, both see "available," and both insert. A single atomic statement closes that window entirely.

**Technologies**

`LiveKit Agents` · `GPT-4o-mini` · `Sarvam STT/TTS` · `FastAPI` · `PostgreSQL` · `asyncpg` · `OpenTelemetry`

**Lessons learned**

Race conditions in voice systems are easy to miss in testing because you rarely call your own agent twice at once. I only found this one by deliberately load-testing with concurrent simulated callers — since then, concurrency testing is a standard step before I call any booking flow "done."

**Links**

- GitHub: `[repo link placeholder]`
- Live demo: `[demo link placeholder]`

`![Hotel Voice Receptionist architecture](./screenshots/hotel-receptionist.png)`

---

### Hotel Management Dashboard

A Next.js dashboard that shares its database directly with the voice receptionist above — a booking made by voice appears on staff screens with no sync delay, because there's no sync layer to begin with.

**Why it exists**

Dashboards that poll a separate booking API introduce lag and a second source of truth. Reading and writing against the same PostgreSQL instance as the voice agent removes that entire failure class.

**Engineering challenges solved**

I inherited this codebase mid-project and ran a full audit, fixing 23 production issues. The most damaging were: ghost bookings (rows left in an inconsistent state by earlier non-atomic writes — resolved once the receptionist moved to CTE-based transactions), incorrect occupancy calculations caused by counting cancelled reservations, and checkout logic that didn't correctly release rooms back into the available pool.

**Key features**

- Real-time booking visibility with zero synchronization layer
- Corrected occupancy and revenue reporting
- Checkout flow that reliably releases room inventory
- Query optimization to remove redundant round trips per page load

**Architecture highlights**

Drizzle ORM over Neon PostgreSQL, with the same schema and constraints the voice agent writes against. Because both systems share one source of truth, correctness fixes made at the database layer (like the atomic booking CTE) automatically apply to what the dashboard displays — there's no dashboard-side reconciliation logic to keep in sync.

**Technologies**

`Next.js` · `TypeScript` · `TailwindCSS` · `Drizzle ORM` · `Neon PostgreSQL` · `Recharts` · `ISR`

**Lessons learned**

A shared database is a stronger consistency guarantee than any amount of webhook or polling-based syncing between two separate systems — but it only works if every writer respects the same transactional discipline. Auditing this dashboard is what pushed me to formalize the CTE pattern in the receptionist in the first place.

**Links**

- GitHub: `[repo link placeholder]`
- Live demo: `[demo link placeholder]`

`![Hotel Dashboard screenshot](./screenshots/hotel-dashboard.png)`

---

### AI Outbound Sales Dialer

A fully automated outbound calling system: an orchestrator polls for pending contacts, launches a LiveKit agent per call, places the call through a Plivo SIP trunk, runs the conversation, and writes a structured outcome back to the database — with no manual dispatch step.

**Why it exists**

Manually dialing leads doesn't scale, and most "AI dialer" tools stop at placing the call — they don't handle objections, follow-ups, or retries in a way that's queryable afterward. I wanted every call to end in a structured, analyzable record, not a transcript someone has to read.

**Engineering challenges solved**

Each call needed to be an independently deployable agent instance rather than a shared long-running process, so that one crashed call couldn't take down the orchestrator or other in-flight calls. I also implemented retry and max-attempt logic directly against the database state, so a restart of the orchestrator doesn't cause duplicate calls to the same contact — the source of truth for "has this contact been called" lives in PostgreSQL, not in memory.

**Key features**

- Orchestrator that continuously polls for pending contacts and dispatches calls
- LiveKit room creation and SIP call placement via Plivo, per call
- English/Hindi language switching mid-conversation based on caller response
- Objection-handling conversation flows
- Automated follow-up scheduling written back to the database
- Retry logic with a max-attempt ceiling to avoid harassing unresponsive contacts
- Structured outcome recording (interested, callback requested, not interested, no answer)

**Architecture highlights**

Each outbound call spins up its own LiveKit agent process rather than routing through one shared agent, which keeps failures isolated — a bug in one call's conversation state can't corrupt another's. Call outcomes are written through `asyncpg` as structured rows, which is what makes the follow-up scheduler possible: it queries "which contacts need a callback today" as a plain SQL query rather than parsing transcripts.

**Technologies**

`Python` · `LiveKit SIP` · `Plivo` · `GPT-4o-mini` · `Sarvam STT/TTS` · `PostgreSQL` · `asyncpg`

**Lessons learned**

Language switching mid-call is harder to get right than language detection at call start — the agent has to notice a code-switch in the caller's response and commit to it for the rest of the conversation, or the experience feels broken. I ended up tracking detected language as explicit conversation state rather than re-inferring it turn by turn.

**Links**

- GitHub: `[repo link placeholder]`
- Live demo: `[demo link placeholder]`

`![Outbound Dialer flow](./screenshots/outbound-dialer.png)`

---

### NITK Library Voice Assistant (Ritu)

A conversational AI assistant deployed on a physical kiosk at a university library entrance, answering questions about academic databases, borrowing rules, fines, floor layout, thesis submission, and plagiarism checking — with both a voice interface (Ritu) and a text interface (Aria) on the same Next.js frontend.

**Why it exists**

Front-desk staff answer the same handful of question categories dozens of times a day. A kiosk that handles the repetitive cases correctly frees staff for the ones that actually need a human.

**Engineering challenges solved**

The core risk with a public-facing kiosk isn't rudeness — it's confident wrong answers about things like fine amounts or submission deadlines. I scoped the agent's tool access tightly to structured library data rather than letting it answer from general knowledge, so it either retrieves a real answer or says it doesn't know. I also had to tune turn-taking and voice activity detection carefully, since a public space has background noise that a quiet office deployment wouldn't.

**Key features**

- Dual interface: Ritu (voice) and Aria (text), sharing the same backend logic
- Structured, grounded answers on library policy rather than free-form generation
- Silero VAD tuned for a noisy, public physical environment
- Deployed on kiosk hardware, not just accessible from a browser tab

**Architecture highlights**

`DeepSeek Chat` as the reasoning model, with `Sarvam` handling STT/TTS for Indian-language support and `Silero VAD` handling turn detection. Voice and text share the same underlying agent logic so behavior is consistent regardless of which interface a student uses.

**Technologies**

`LiveKit Agents` · `DeepSeek Chat` · `Sarvam STT/TTS` · `Silero VAD` · `Next.js`

**Lessons learned**

Deploying to a physical space surfaces problems a desk-based test never will — ambient noise, interruption patterns, multiple people talking near the mic. Tuning VAD sensitivity took more iteration than tuning the LLM prompt did.

**Links**

- GitHub: `[repo link placeholder]`
- Live demo: `[demo link placeholder]`

`![Ritu kiosk interface](./screenshots/ritu-kiosk.png)`

---

### Insurance Voice Assistant

A voice assistant for insurance policy servicing and customer support — policy lookup, claim status, waiting-period explanations, cashless claim guidance, and No Claim Bonus queries, built around IRDAI regulatory context.

**Why it exists**

Insurance support calls are high-anxiety and heavily regulated. Getting a waiting period or a claim status wrong isn't a minor UX issue — it has real consequences for the caller. This meant the agent needed conservative behavior baked in, not just accurate information most of the time.

**Engineering challenges solved**

I designed the escalation logic to trigger proactively rather than reactively: certain topics (denied claims, regulatory disputes) route to a human by default rather than waiting for the caller to ask for one. The agent also needed a noticeably different conversational tone than the hotel or sales agents — more measured, more willing to say "let me connect you with someone" instead of pushing to resolve everything itself.

**Key features**

- Policy lookup and claim status retrieval against structured records
- IRDAI regulation guidance grounded in reference data
- Waiting period and free-look period explanations
- Cashless claim process walkthroughs
- No Claim Bonus (NCB) queries
- Proactive human escalation for sensitive or disputed cases
- Deliberately calmer conversational tone tuned for a support context

**Architecture highlights**

`Deepgram STT` and `Deepgram Aura TTS` for the voice pipeline, `GPT-4o-mini` for reasoning, and a PostgreSQL-backed policy store the agent queries rather than reasons about from memory.

**Technologies**

`LiveKit Agents` · `GPT-4o-mini` · `Deepgram STT` · `Deepgram Aura TTS` · `PostgreSQL`

**Lessons learned**

Tone tuning matters as much as factual grounding in a support context — the same correct answer delivered too casually erodes trust with a caller who's anxious about a claim.

**Links**

- GitHub: `[repo link placeholder]`
- Live demo: `[demo link placeholder]`

`![Insurance Assistant screenshot](./screenshots/insurance-assistant.png)`

---

### Resume-Grounded Interview Agent

A real-time voice interview system that answers questions strictly from a candidate's resume and a target job description, with hallucination prevention as the central design constraint rather than an afterthought.

**Why it exists**

Generic interview-practice tools answer from general knowledge, which makes them useless for practicing questions specific to your actual background. This agent is deliberately constrained to only speak to what's in the uploaded resume and job description.

**Engineering challenges solved**

The main constraint was latency: a voice interview that pauses for two seconds before responding doesn't feel like a conversation. I got the agent down to sub-500ms response latency by using `Cartesia` for both STT and TTS (chosen specifically for round-trip speed) and keeping the grounding step lightweight — retrieval against the resume/JD text rather than a heavier RAG pipeline.

**Key features**

- Voice-first, real-time interview interaction
- Responses grounded strictly in resume and job description content
- Explicit refusal behavior when asked something outside that scope
- Sub-500ms response latency
- Containerized deployment for consistent behavior across environments

**Architecture highlights**

Docker-packaged for deployment consistency, with `LiveKit Agents` handling the real-time audio pipeline and `GPT-4o-mini` constrained via prompt and retrieval scope to the candidate's actual documents.

**Technologies**

`LiveKit Agents` · `GPT-4o-mini` · `Cartesia STT` · `Cartesia TTS` · `Docker`

**Lessons learned**

Latency budget forces architectural discipline — every extra step in the pipeline (a heavier retrieval call, an extra LLM round trip) shows up directly as dead air in the conversation, which makes this project a good forcing function for keeping the pipeline lean.

**Links**

- Live demo: [madityarao.github.io/Resume](https://madityarao.github.io/Resume/)
- GitHub: [github.com/MAdityaRao](https://github.com/MAdityaRao)

`![Resume Interview Agent screenshot](./screenshots/resume-interview.png)`

---

### Commodity Price Forecasting

Research-focused time-series forecasting for Karnataka commodity markets — coffee (Arabica and Robusta) and arecanut (Adike and Patora grades, Karkala and Udupi markets) — comparing LSTM, GRU, and SimpleRNN architectures, co-authored as IEEE-format research papers with a faculty collaborator.

**Why it exists**

Regional commodity price data is noisy and thin compared to the datasets most forecasting tutorials assume. The interesting engineering question wasn't "which model wins" in the abstract — it was which architecture actually generalizes on this specific, limited, regionally-sourced data.

**Engineering challenges solved**

Getting a fair comparison meant controlling everything except the recurrent architecture itself: identical preprocessing, identical train/validation splits, identical scaling, so that differences in reported performance reflect the model choice rather than pipeline drift between experiments. I also had to handle missing and irregular market-day data, since prices aren't reported on days markets don't trade.

**Key features**

- Consistent preprocessing and feature scaling pipeline across all three architectures
- LSTM vs. GRU vs. SimpleRNN comparison on both coffee and arecanut datasets
- Performance evaluation across grade- and market-specific subsets
- Prediction served through a lightweight API rather than left as a notebook result

**Architecture highlights**

The pipeline runs from raw scraped/collected market data through cleaning, scaling, and windowing, into parallel model training runs, with results and the final trained model exposed through a small FastAPI/Flask prediction endpoint rather than staying notebook-bound.

**Technologies**

`TensorFlow` · `Keras` · `Pandas` · `Scikit-learn` · `FastAPI` · `Flask`

**Lessons learned**

On small, regionally-specific datasets, GRU's lower parameter count relative to LSTM turned out to matter more than architectural sophistication — a useful reminder that model choice should follow from the data size and noise profile, not from whichever architecture is currently popular.

**Links**

- GitHub: `[repo link placeholder]`

`![Forecasting results chart](./screenshots/commodity-forecasting.png)`

---

## Architecture Philosophy

Every voice AI system I build follows the same shape:

```text
Frontend
    │
    ▼
LiveKit Token API
    │
    ▼
Independent Voice Agent
    │
    ▼
Shared PostgreSQL Database
    ▲
    │
Dashboard
```

**Why this scales:**

- **The agent is never embedded in the frontend.** It runs as its own deployable process, connected via a LiveKit token endpoint. This means I can redeploy the agent — fix a prompt, patch a tool, roll back a bad change — without touching or redeploying the frontend at all.
- **The database is the single source of truth.** Both the voice agent and the dashboard read and write against the same PostgreSQL instance. There's no sync job, no webhook relay, no eventual-consistency window between "the agent booked a room" and "the dashboard shows it." If it's in the database, every consumer sees it immediately.
- **Correctness lives at the database layer, not the application layer.** Concurrency guarantees (like the atomic booking CTE in the hotel receptionist) are enforced by the database transaction itself, which means they hold regardless of how many agent instances are running or how the application code evolves around them.
- **Failure is isolated.** Because each agent instance is independently deployable, a crash in one conversation doesn't take down the token API, the dashboard, or other in-flight agent instances.

This same architecture — frontend, token API, independent agent, shared database, dashboard — is what powers the hotel receptionist, the outbound dialer, the insurance assistant, and the university kiosk. The domain logic changes; the shape underneath doesn't.

---

## Technical Skills

### Voice AI

| Skill | Tools |
|---|---|
| Real-time agent frameworks | LiveKit Agents |
| Speech-to-text / text-to-speech | Sarvam, Deepgram, Cartesia |
| Turn detection | Silero VAD |
| Conversation design | Tool calling, multi-turn state management, prompt engineering |

### Backend

| Skill | Tools |
|---|---|
| API frameworks | FastAPI, Flask |
| Async database access | asyncpg |
| Language | Python |
| Reliability patterns | Atomic transactions, retry/backoff logic, structured error handling |
| Observability | OpenTelemetry |

### Frontend

| Skill | Tools |
|---|---|
| Framework | Next.js, React |
| Language | TypeScript |
| Styling | TailwindCSS |
| Data layer | Drizzle ORM |

### Machine Learning

| Skill | Tools |
|---|---|
| Deep learning frameworks | TensorFlow, Keras |
| Sequence models | LSTM, GRU, SimpleRNN |
| Classical ML | Scikit-learn |
| Applied pipelines | OCR pipelines, time-series forecasting |

### Cloud & DevOps

| Skill | Tools |
|---|---|
| Containerization | Docker |
| Version control | Git, GitHub |
| Deployment | Vercel |
| Tracing | OpenTelemetry |

### Databases

| Skill | Tools |
|---|---|
| Relational databases | PostgreSQL (Neon-hosted) |
| Access patterns | asyncpg (async), Drizzle ORM (typed) |
| Consistency techniques | Atomic CTEs, row-level locking, shared-schema architecture |

---

## Engineering Principles

> Reliability is a feature, not a phase you get to later.

**Clean code over clever code.** I'd rather write a slightly longer function that's obvious to debug at 2 a.m. than a compact one that requires re-deriving the logic every time I touch it.

**Observability from the start.** OpenTelemetry tracing goes into the agent and API layer before the feature ships, not after the first production incident. If I can't see why a call failed, I can't fix it — I can only guess.

**Transactions as a design tool, not an afterthought.** Concurrency bugs are the hardest class of bug to catch in manual testing because they require two things happening at once. I default to atomic, transactional writes for anything involving shared state, specifically because I don't trust myself to catch a race condition by eyeballing the code.

**Shared database over synchronization layers.** Every extra sync mechanism between two systems is another place for state to drift. Where it's architecturally sound, I prefer one database both systems read and write against directly.

**Simple architecture, shipped fast, refined under real load.** I'd rather ship the straightforward version and harden it against real production issues (like the 23 bugs I fixed in the hotel dashboard) than spend weeks pre-optimizing for failure modes that may never occur.

**Avoiding over-engineering.** Independent agent instances, atomic transactions, and tracing aren't there because they're impressive — they're there because I've watched the alternative break in production. I try not to add complexity beyond what a given system's actual failure modes justify.

---

## Open Source

My project code lives at [github.com/MAdityaRao](https://github.com/MAdityaRao). Most of my public repositories are extracted, cleaned versions of production systems I've built for clients or research collaborators — I try to keep sensitive credentials and client-specific data out, while keeping the actual architecture and logic intact so the code is genuinely representative of how the system works.

I'm generally more interested in maintaining a small number of well-documented, working systems than accumulating a large number of half-finished ones.

---

## Currently Learning

- Retrieval-augmented generation patterns for grounding voice agents in larger, less structured document sets
- Multi-agent orchestration for handoffs between specialized agents mid-conversation
- Deeper OpenTelemetry-based evaluation of LLM tool-calling accuracy in production
- Structured evaluation pipelines for voice agents (beyond manual QA)

---

## Let's Build Something

If you're working on:

- Voice AI agents that need to hold up under real call volume
- LLM applications where hallucination is not an acceptable failure mode
- Backend systems where concurrent writes have to stay correct
- AI calling infrastructure — outbound, inbound, or both
- Dashboards that need to reflect live system state, not a cached snapshot

I'd like to hear about it.

📧 **[madityara5@gmail.com](mailto:madityara5@gmail.com)**
🌐 **Portfolio:** [resume-web-liard.vercel.app](https://resume-web-liard.vercel.app/)
