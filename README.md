# Hi, I'm Aditya 👋

AI/ML engineer building voice AI agents and backend systems that hold up with real users, not just in a demo. Based in Karnataka, India, finishing a B.Sc. in Data Analytics alongside client and research work.

Most of what's below came out of an internship at Torq Designs (Nitte AIC) where I led backend development on an entire AI voice product line — the agents themselves, plus the databases and dashboards that keep everything in sync — alongside a couple of independent research and voice AI projects.

📧 madityara5@gmail.com · 🌐 [portfolio](https://resume-web-liard.vercel.app/)

---

## Projects

### 🏨 Hotel Voice Receptionist
A 24/7 AI hotel receptionist built on LiveKit Agents — handles guest inquiries, checks room availability, and books rooms entirely through voice. Spoken phone numbers get normalized through a custom heuristic, and every booking runs through an atomic SQL CTE (lock → insert → update in one transaction), so two callers can never double-book the same room. Also handles cancellations, existing-booking lookups, hotel policy questions, and escalation to a human supervisor when needed. Instrumented end-to-end with OpenTelemetry for tracing.

`LiveKit Agents` `GPT-4o-mini` `Sarvam STT/TTS` `PostgreSQL + asyncpg` `FastAPI` `OpenTelemetry`

### 🖥️ Hotel Management Dashboard
The operational counterpart to the receptionist agent — a Next.js dashboard that reads and writes the *same* database the voice agent uses, so a booking made by voice shows up on staff screens without any sync step in between. I audited the full codebase and fixed 23 bugs, including ghost bookings from unfiltered queries, occupancy numbers that didn't match the bookings page, and checkouts that weren't actually freeing rooms.

`Next.js` `Drizzle ORM` `Neon Postgres` `Recharts` `ISR`

### 📞 Outbound Sales Dialer
A demo-grade outbound AI voice sales agent: an orchestrator reads `pending` contacts from Postgres, dispatches an agent into a fresh LiveKit room, and dials out over a Plivo SIP trunk. Once connected, the agent (GPT-4o-mini + Sarvam STT/TTS, tuned for Indian English) runs the pitch, handles three objection types (busy / not interested / interested), and switches to Hindi mid-call if the contact does. Every call ends with a structured outcome written back to the database, plus automatic follow-up scheduling with a max-attempts cutoff before a contact gets discarded.

`LiveKit SIP` `Plivo` `GPT-4o-mini` `Sarvam STT/TTS` `PostgreSQL (asyncpg)` `Python`

### 🎓 NITK Central Library Voice Kiosk — "Ritu"
A voice agent deployed on a robot at a university library entrance. Talks a student through finding the right academic databases for their subject, library hours, floor layout, borrowing rules, fines, and thesis/plagiarism-check services — backed by a static knowledge base so the demo stays predictable. Runs alongside a Next.js frontend with both a voice interface (Ritu) and a text chat interface (Aria).

`LiveKit Agents` `DeepSeek Chat` `Sarvam STT (saaras:v3) / TTS (bulbul:v3)` `Silero VAD` `Next.js`

### 🛡️ Insurance Voice Assistant — "Arria"
A voice-driven insurance assistant that retrieves policy details, checks claim status, and answers IRDAI regulatory questions (waiting periods, cashless claims, NCB, Section 45, free-look period, and more) — with an empathetic persona and escalation to a human when a customer is upset or asks for one directly. Spoken policy numbers get parsed from words to digits and normalized into the DB's format.

`LiveKit Agents` `GPT-4o-mini` `Deepgram STT (Nova-3) / TTS (Aura-2)` `PostgreSQL (Neon)`

### 🎤 Resume-Based Interview Agent
A real-time voice interviewer that answers strictly from a candidate's resume plus a job description sent over the data channel — sub-500ms latency, 30–45 word answers, professional tone, and it'll tell you honestly when the JD doesn't match the background instead of making something up.

`LiveKit Agents` `GPT-4o-mini` `Cartesia STT (Ink Whisper) / TTS (Sonic Turbo)` `Docker`
[Live demo](https://madityarao.github.io/Resume/) · [GitHub](https://github.com/MAdityaRao)

### 🌾 Commodity Price Forecasting (LSTM)
Time-series forecasting models for arecanut prices (Adike & Patora grades, Karkala/Udupi markets) and Karnataka coffee prices (Arabica/Robusta), comparing LSTM against GRU and SimpleRNN architectures. Data preprocessing with MinMaxScaler, evaluation via MAE and R², and a small FastAPI/Flask service serving next-day price predictions from the trained model. Co-authored as IEEE-format research papers with a faculty collaborator at Nitte.

`TensorFlow/Keras` `pandas` `scikit-learn` `FastAPI/Flask`

---

## The pattern behind the agent projects

The voice/hotel/outbound/library projects aren't one-off builds — they share a deliberate architecture I've refined across all of them: **the agent always runs as a separate, independently deployable process**, never folded into the frontend's serverless functions. The frontend's only job is to open a door (mint a LiveKit token with agent dispatch embedded), call the agent's brain directly for a single chat turn, or read/write shared database state that the agent also touches. That separation is what let me ship the hotel dashboard and the voice agent as fully independent deploys, and what makes the outbound dialer treat Postgres as a proper work queue instead of something bolted on after the fact.

---

## What I work with

**Voice AI** — LiveKit Agents, STT/TTS pipelines (Sarvam, Cartesia, Deepgram), tool-calling agents, multi-turn prompt design

**Backend** — Python, asyncpg, FastAPI, atomic SQL transactions, PostgreSQL (Neon)

**Frontend** — Next.js, TypeScript, Tailwind, Drizzle ORM

**ML** — LSTM/GRU/RNN time-series forecasting, OCR pipelines, scikit-learn

---

## How I work

I write full files instead of patchwork edits when building something new — easier to review, easier to trust. I don't reach for abstraction a project doesn't need yet, and I'd rather ship something running fast so we're debugging real behavior instead of arguing over specs.

If you're building something that needs to talk to people, or a backend that can't afford to lose data under load, feel free to reach out.
