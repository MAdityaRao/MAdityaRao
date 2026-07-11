<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0f0f,100:1a1a1a&height=220&section=header&text=Aditya%20Rao&fontSize=60&fontColor=F5A623&animation=fadeIn&fontAlignY=38&desc=AI%20Engineer%20%E2%80%94%20Voice%20AI%20Systems%20%C2%B7%20LLM%20Applications%20%C2%B7%20Production%20Backends&descAlignY=58&descSize=18&descColor=CFCFCF"/>

<br/>

<a href="https://resume-web-liard.vercel.app/">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=500&size=22&duration=3000&pause=800&color=F5A623&center=true&vCenter=true&multiline=true&repeat=true&width=700&height=90&lines=I+design+and+ship+real-time+voice+AI+agents...;...and+the+backend+systems+that+keep+them+correct.;My+work+lives+in+production%2C+not+in+notebooks." alt="Typing SVG" />
</a>

<br/><br/>

[![Portfolio](https://img.shields.io/badge/Portfolio-resume--web--liard.vercel.app-0f0f0f?style=for-the-badge&logo=vercel&logoColor=F5A623&labelColor=000000)](https://resume-web-liard.vercel.app/)
[![GitHub](https://img.shields.io/badge/GitHub-MAdityaRao-0f0f0f?style=for-the-badge&logo=github&logoColor=F5A623&labelColor=000000)](https://github.com/MAdityaRao)
[![Email](https://img.shields.io/badge/Email-madityarao5%40gmail.com-0f0f0f?style=for-the-badge&logo=gmail&logoColor=F5A623&labelColor=000000)](mailto:madityarao5@gmail.com)

</div>

<br/>

## 👋 About Me

I build production AI applications while completing a **B.Sc. in Data Analytics** at Nitte (Deemed to be University), Karnataka. The degree gives me the statistical and ML foundation; the actual engineering skill comes from shipping systems that handle **real callers, real bookings, and real data** under real constraints.

Most of my work centers on one recurring problem: making voice AI agents behave reliably when multiple things happen at once — a guest booking a room while another checks out through a dashboard, an outbound dialer placing hundreds of calls without duplicating work across restarts, a library assistant that has to *answer correctly* instead of *confidently*. These aren't prompting problems — they're **systems problems that happen to have an LLM in the loop**.

I led backend development for an AI voice receptionist platform during my internship at **Torq Designs** (Nitte AIC), owning the database layer, booking logic, and production reliability of the voice agent — not just the demo path.

```text
> Reliability is a feature, not a phase you get to later.
```

<br/>

## 🧠 What I Build

<div align="center">

| Area | In Practice |
|---|---|
| 🎙️ **Production Voice AI** | LiveKit-based agents with STT/TTS pipelines, tool calling, sub-second turnaround — deployed as standalone services, not demo scripts |
| 🤖 **LLM Applications** | Grounded, tool-using systems designed to fail safely — refusing or escalating rather than hallucinating |
| ⚙️ **Backend APIs** | FastAPI/Python services with async DB access, structured error handling, observability from day one |
| 🗄️ **Database Architecture** | Schema + transaction logic that stays correct under **concurrent** access, not just a single test call |
| 📞 **AI Calling Systems** | SIP-integrated outbound calling with retry logic, follow-up scheduling, structured outcome logging |
| 🔁 **Automation** | Orchestration layers that poll, dispatch, and recover from failure with no manual intervention |
| 📈 **ML Systems** | Time-series forecasting (LSTM/GRU/RNN) — data cleaning through to a served prediction endpoint |

</div>

<br/>

## 🚀 Featured Projects

<details open>
<summary><b>🏨 AI Hotel Voice Receptionist</b> — 24/7 autonomous booking agent</summary>
<br/>

Handles real hotel operations — availability, bookings, cancellations, policy Q&A — entirely through a phone-style voice interface, writing to the **same database** the front desk uses in real time.

**The hard problem:** concurrency. Two callers requesting the same room can't both win. Every booking write goes through a single **atomic SQL CTE** — lock the room row, verify availability, insert, update — all in one transaction, so the database serializes racing callers instead of the application guessing.

**Also solved:** spoken phone-number normalization ("double nine," "triple five") into a canonical digit string before it ever touches the database.

`LiveKit Agents` `GPT-4o-mini` `Sarvam STT/TTS` `FastAPI` `PostgreSQL` `asyncpg` `OpenTelemetry`

> **Lesson:** race conditions in voice systems don't show up in solo testing — I only found this one by deliberately load-testing with concurrent simulated callers.

**[💻 Repo](https://github.com/MAdityaRao/torq_web_agent)**

</details>

<details>
<summary><b>📊 Hotel Management Dashboard</b> — zero-sync-layer operational view</summary>
<br/>

A Next.js dashboard sharing its database **directly** with the voice receptionist — a voice booking appears on staff screens instantly, because there's no sync layer to begin with.

Audited an inherited codebase and fixed **23 production issues**: ghost bookings from non-atomic writes, incorrect occupancy from miscounted cancellations, and checkout logic that failed to release rooms.

`Next.js` `TypeScript` `TailwindCSS` `Drizzle ORM` `Neon PostgreSQL` `Recharts` `ISR`

**[💻 Repo](https://github.com/MAdityaRao/torq_web_agent)**

</details>

<details>
<summary><b>📞 AI Outbound Sales Dialer</b> — fully automated calling pipeline</summary>
<br/>

An orchestrator polls pending contacts, launches a **LiveKit agent per call**, dials through a **Plivo SIP trunk**, runs the conversation, and writes a structured outcome back — no manual dispatch step.

Each call is an independently deployable agent instance, so one crashed call can't take down the orchestrator. Retry/max-attempt state lives in **PostgreSQL**, not memory, so an orchestrator restart never causes duplicate dials.

**Capabilities:** mid-call English/Hindi language switching, objection-handling flows, automated follow-up scheduling, structured outcome recording (`answered` / `no_answer` / `voicemail` / `follow_up` / `converted` / `failed`).

`Python` `LiveKit SIP` `Plivo` `GPT-4o-mini` `Sarvam STT/TTS` `PostgreSQL` `asyncpg`

</details>

<details>
<summary><b>📚 NITK Library Voice Assistant</b> — Ritu (voice) + Aria (chat)</summary>
<br/>

Deployed on a physical kiosk at the NITK Central Library entrance. Recommends academic databases by subject, answers policy/hours/fines/thesis questions, and directs users to physical sections — through **both a voice interface and a text interface** sharing the same backend logic.

Tool access is scoped tightly to structured library data — the agent either retrieves a real answer or says it doesn't know, rather than answering confidently from general knowledge. VAD tuned specifically for a noisy public kiosk environment.

`LiveKit Agents` `DeepSeek Chat` `Sarvam STT/TTS` `Silero VAD` `Next.js`

**Agent contract:** `AGENT_NAME = "nitk-library-agent"` — dispatch name embedded directly in the LiveKit token, so browser-connect and agent-invite happen as one atomic step.

</details>

<details>
<summary><b>🛡️ Insurance Voice Assistant</b> — IRDAI-grounded policy servicing</summary>
<br/>

Handles policy lookup, claim status, waiting-period explanations, cashless claim guidance, and NCB queries — with **proactive** escalation: denied claims and regulatory disputes route to a human by default, not on request.

Deliberately tuned to a calmer, more measured conversational tone than the hotel/sales agents — a support context where getting things wrong has real consequences.

`LiveKit Agents` `GPT-4o-mini` `Deepgram STT` `Deepgram Aura TTS` `PostgreSQL`

**[💻 Repo](https://github.com/MAdityaRao/Mvp_ins)**

</details>

<details>
<summary><b>🎤 Resume-Grounded Interview Agent</b> — sub-500ms voice interviewer</summary>
<br/>

Answers strictly from a candidate's resume + target JD, with hallucination prevention as the **central design constraint**, not an afterthought. Accepts a live JD over a data channel and adapts mid-conversation.

Hit sub-500ms round-trip latency using `Cartesia` for both STT and TTS, and kept grounding lightweight — direct retrieval over the resume/JD text instead of a heavier RAG pipeline.

`LiveKit Agents` `GPT-4o-mini` `Cartesia STT/TTS` `Docker` `Next.js`

**[💻 Repo](https://github.com/MAdityaRao/Resume_agent) · [🔗 Live demo](https://resume-web-liard.vercel.app/) · [📖 README](https://resume-web-liard.vercel.app/project/project_readme/resume_agent)**

</details>

<details>
<summary><b>📈 Commodity Price Forecasting</b> — LSTM vs GRU vs SimpleRNN</summary>
<br/>

Time-series forecasting for Karnataka commodity markets — **coffee** (Arabica/Robusta, IEEE paper w/ faculty co-author) and **arecanut** (Adike/Patora grades, Karkala & Udupi APMC). Identical preprocessing/splits/scaling across all three architectures for a fair comparison; results served through a lightweight Flask/FastAPI prediction endpoint instead of staying notebook-bound.

`TensorFlow` `Keras` `Pandas` `Scikit-learn` `FastAPI` `Flask`

> **Finding:** on small, regionally-specific datasets, GRU's lower parameter count mattered more than architectural sophistication — model choice should follow the data's size and noise profile, not current popularity.

**[💻 LSTM repo](https://github.com/MAdityaRao/arecanut_lstm) · [💻 RNN repo](https://github.com/MAdityaRao/arecanut_rnn) · [📖 README](https://resume-web-liard.vercel.app/project/project_readme/aracanut_lstm)**

</details>

<br/>

## 🏗️ Architecture Philosophy

Every voice AI system I build follows the same shape:

```text
Frontend  →  LiveKit Token API  →  Independent Voice Agent  →  Shared PostgreSQL  ←  Dashboard
```

- **The agent is never embedded in the frontend.** It's its own deployable process — I can patch a prompt or roll back a bad change without touching the frontend at all.
- **The database is the single source of truth.** No sync job, no webhook relay, no eventual-consistency window. If it's in the DB, every consumer sees it immediately.
- **Correctness lives at the database layer.** Atomic CTEs hold regardless of how many agent instances are running.
- **Failure is isolated.** A crash in one conversation can't take down the token API or other in-flight calls.

This same shape — frontend, token API, independent agent, shared database, dashboard — powers the hotel receptionist, the outbound dialer, the insurance assistant, and the library kiosk. The domain logic changes; the shape underneath doesn't.

<br/>

## 🛠️ Tech Stack

<div align="center">

**Voice AI**

![LiveKit](https://img.shields.io/badge/LiveKit_Agents-FF3C3C?style=flat-square) ![Sarvam](https://img.shields.io/badge/Sarvam_STT%2FTTS-6C5CE7?style=flat-square) ![Deepgram](https://img.shields.io/badge/Deepgram-13EF93?style=flat-square&logoColor=black) ![Cartesia](https://img.shields.io/badge/Cartesia-000000?style=flat-square) ![Silero](https://img.shields.io/badge/Silero_VAD-4A4A4A?style=flat-square)

**Backend**

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat-square&logo=flask&logoColor=white) ![asyncpg](https://img.shields.io/badge/asyncpg-336791?style=flat-square)

**Frontend**

![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js&logoColor=white) ![React](https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=react&logoColor=black) ![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white) ![TailwindCSS](https://img.shields.io/badge/TailwindCSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)

**Machine Learning**

![TensorFlow](https://img.shields.io/badge/TensorFlow-FF6F00?style=flat-square&logo=tensorflow&logoColor=white) ![Keras](https://img.shields.io/badge/Keras-D00000?style=flat-square&logo=keras&logoColor=white) ![scikit--learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat-square&logo=scikit-learn&logoColor=white) ![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat-square&logo=pandas&logoColor=white)

**Data & Infra**

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white) ![Neon](https://img.shields.io/badge/Neon-00E599?style=flat-square&logo=databricks&logoColor=black) ![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white) ![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white) ![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazonaws&logoColor=white) ![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)

</div>

<br/>

<div align="center">
<img src="https://skillicons.dev/icons?i=python,fastapi,flask,postgresql,nextjs,react,typescript,tailwind,tensorflow,docker,aws,vercel,git,github&theme=dark" />
</div>

<br/>

## 📌 Public Repositories

<div align="center">

| Repo | What it is |
|---|---|
| [`resume_web`](https://github.com/MAdityaRao/resume_web) | This portfolio site — Next.js + live voice agent "Sharanya" |
| [`torq_web_agent`](https://github.com/MAdityaRao/torq_web_agent) | Torq hotel voice receptionist + management dashboard |
| [`Mvp_ins`](https://github.com/MAdityaRao/Mvp_ins) | AI Insurance Voice Agent Platform ("Arria") |
| [`Resume_agent`](https://github.com/MAdityaRao/Resume_agent) | Resume-grounded voice interview agent |
| [`arecanut_lstm`](https://github.com/MAdityaRao/arecanut_lstm) | Arecanut price forecasting — LSTM model |
| [`arecanut_rnn`](https://github.com/MAdityaRao/arecanut_rnn) | Arecanut price forecasting — SimpleRNN comparison |
| [`nurse_note_ai`](https://github.com/MAdityaRao/nurse_note_ai) | Nursing/clinical note AI tooling |
| [`pdubelman_temple`](https://github.com/MAdityaRao/pdubelman_temple) | Temple website build |
| [`mar-lang`](https://github.com/MAdityaRao/mar-lang) / [`marlang_web`](https://github.com/MAdityaRao/marlang_web) | Language project + web frontend |
| [`farm_finance`](https://github.com/MAdityaRao/farm_finance) | Agricultural finance project |
| [`java_dsa`](https://github.com/MAdityaRao/java_dsa) | Data structures & algorithms practice, Java |

</div>

<br/>

## 📊 GitHub Metrics

<div align="center">

<img src="https://raw.githubusercontent.com/MAdityaRao/MAdityaRao/main/github-metrics.svg" alt="Aditya's GitHub metrics — animated isometric calendar, activity, languages, and topics" width="100%"/>

<sub>Live, auto-refreshing daily via <a href="https://github.com/lowlighter/metrics">lowlighter/metrics</a> · gold/white themed to match the portfolio accent</sub>

</div>

<br/>

## 📊 GitHub Stats

<div align="center">

<img height="165" src="https://github-readme-stats.vercel.app/api?username=MAdityaRao&show_icons=true&theme=dark&hide_border=true&bg_color=0D1117&title_color=F5A623&icon_color=F5A623&text_color=CFCFCF"/>
<img height="165" src="https://github-readme-stats.vercel.app/api/top-langs/?username=MAdityaRao&layout=compact&theme=dark&hide_border=true&bg_color=0D1117&title_color=F5A623&text_color=CFCFCF"/>

<br/>

<img src="https://github-readme-streak-stats.herokuapp.com/?user=MAdityaRao&theme=dark&hide_border=true&background=0D1117&ring=F5A623&fire=F5A623&currStreakLabel=F5A623"/>

<br/>

<img src="https://github-readme-activity-graph.vercel.app/graph?username=MAdityaRao&theme=react-dark&hide_border=true&bg_color=0D1117&color=F5A623&line=F5A623&point=CFCFCF"/>

</div>

<br/>

## 🧭 Engineering Principles

- **Clean code over clever code.** A slightly longer function that's obvious to debug at 2am beats a compact one I have to re-derive every time.
- **Observability from the start.** OpenTelemetry tracing ships with the feature, not after the first incident.
- **Transactions as a design tool.** I default to atomic writes for shared state because I don't trust myself to spot a race condition by eyeballing code.
- **Shared database over sync layers.** Every extra sync mechanism is another place for state to drift.
- **Ship simple, refine under real load** — rather than pre-optimizing for failure modes that may never occur.
- **Avoid over-engineering.** Independent instances, atomic transactions, and tracing exist because I've watched the alternative break in production — not because they're impressive.

<br/>

## 📫 Let's Build Something

If you're working on voice AI that needs to hold up under real call volume, LLM applications where hallucination isn't acceptable, backend systems with concurrent writes that must stay correct, or AI calling infrastructure — I'd like to hear about it.

<div align="center">

[![Email](https://img.shields.io/badge/madityarao5%40gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:madityarao5@gmail.com)
[![Portfolio](https://img.shields.io/badge/resume--web--liard.vercel.app-000000?style=for-the-badge&logo=vercel&logoColor=white)](https://resume-web-liard.vercel.app/)
[![GitHub](https://img.shields.io/badge/MAdityaRao-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/MAdityaRao)

<br/>

![Profile Views](https://komarev.com/ghpvc/?username=MAdityaRao&color=F5A623&style=flat-square&label=Profile+Views)

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0f0f,100:1a1a1a&height=100&section=footer"/>

</div>
