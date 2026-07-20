<div align="center">
<!-- Animated header with custom gradient -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:17130F,40:2A1F17,70:3D2A1E,100:D97757&height=260&section=header&text=Aditya%20Rao&fontSize=70&fontColor=D97757&animation=fadeIn&fontAlignY=32&desc=Systems%20Engineer%20%E2%80%94%20Voice%20AI%20%C2%B7%20Production%20Backends%20%C2%B7%20LLM%20Infrastructure&descAlignY=56&descSize=16&descColor=A8A29E"/>
<br/>
<!-- Custom typing animation with longer phrases -->
<a href="https://resume-web-liard.vercel.app/">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=18&duration=3500&pause=800&color=D97757&center=true&vCenter=true&multiline=true&repeat=true&width=800&height=70&lines=Voice+agents+that+hold+up+under+real+call+volume...;...and+the+systems+that+keep+them+honest+when+things+go+wrong." alt="Typing SVG" />
</a>
<br/><br/>
<!-- Contact badges -->
https://resume-web-liard.vercel.app/
https://github.com/MAdityaRao
mailto:madityarao5@gmail.com
</div>
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
What I Actually Do
I ship voice AI systems that handle real traffic — not demos that break the moment two people call at once.
Currently finishing a B.Sc. in Data Analytics at Nitte University, Karnataka. The degree covers the theory. The actual skill comes from production systems that have handled real callers, real bookings, and real money.
My recurring problem: making voice agents behave when the world doesn't cooperate. Two guests booking the same room simultaneously. An outbound dialer restarting mid-campaign and redialing everyone. A library kiosk in a noisy hallway where the agent needs to hear the question, not the echo.
These aren't LLM prompting problems. They're systems problems that happen to have an LLM in the loop.
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad2" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad2)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Production Systems
<div align="center">
<table>
<tr>
<td width="50%" valign="top">
AI Hotel Voice Receptionist
24/7 autonomous booking agent
Handles real hotel operations — availability, bookings, cancellations, policy Q&A — entirely through a phone-style voice interface, writing to the same database the front desk uses in real time.
The hard problem: concurrency. Two callers requesting the same room can't both win. Every booking write goes through a single atomic SQL CTE — lock the room row, verify availability, insert, update — all in one transaction, so the database serializes racing callers instead of the application guessing.
Also solved: spoken phone-number normalization ("double nine," "triple five") into a canonical digit string before it ever touches the database.
LiveKit Agents GPT-4o-mini Sarvam STT/TTS FastAPI PostgreSQL asyncpg OpenTelemetry
Lesson: race conditions in voice systems don't show up in solo testing — I only found this one by deliberately load-testing with concurrent simulated callers.
Repo
</td>
<td width="50%" valign="top">
Hotel Management Dashboard
Zero-sync-layer operational view
A Next.js dashboard sharing its database directly with the voice receptionist — a voice booking appears on staff screens instantly, because there's no sync layer to begin with.
Audited an inherited codebase and fixed 23 production issues: ghost bookings from non-atomic writes, incorrect occupancy from miscounted cancellations, and checkout logic that failed to release rooms.
Next.js TypeScript TailwindCSS Drizzle ORM Neon PostgreSQL Recharts ISR
Repo
</td>
</tr>
<tr>
<td width="50%" valign="top">
AI Outbound Sales Dialer
Fully automated calling pipeline
An orchestrator polls pending contacts, launches a LiveKit agent per call, dials through a Plivo SIP trunk, runs the conversation, and writes a structured outcome back — no manual dispatch step.
Each call is an independently deployable agent instance, so one crashed call can't take down the orchestrator. Retry/max-attempt state lives in PostgreSQL, not memory, so an orchestrator restart never causes duplicate dials.
Capabilities: mid-call English/Hindi language switching, objection-handling flows, automated follow-up scheduling, structured outcome recording (answered / no_answer / voicemail / follow_up / converted / failed).
Python LiveKit SIP Plivo GPT-4o-mini Sarvam STT/TTS PostgreSQL asyncpg
</td>
<td width="50%" valign="top">
NITK Library Voice Assistant
Ritu (voice) + Aria (chat)
Deployed on a physical kiosk at the NITK Central Library entrance. Recommends academic databases by subject, answers policy/hours/fines/thesis questions, and directs users to physical sections — through both a voice interface and a text interface sharing the same backend logic.
Tool access is scoped tightly to structured library data — the agent either retrieves a real answer or says it doesn't know, rather than answering confidently from general knowledge. VAD tuned specifically for a noisy public kiosk environment.
LiveKit Agents DeepSeek Chat Sarvam STT/TTS Silero VAD Next.js
Agent contract: AGENT_NAME = "nitk-library-agent" — dispatch name embedded directly in the LiveKit token, so browser-connect and agent-invite happen as one atomic step.
</td>
</tr>
<tr>
<td width="50%" valign="top">
Insurance Voice Assistant
IRDAI-grounded policy servicing
Handles policy lookup, claim status, waiting-period explanations, cashless claim guidance, and NCB queries — with proactive escalation: denied claims and regulatory disputes route to a human by default, not on request.
Deliberately tuned to a calmer, more measured conversational tone than the hotel/sales agents — a support context where getting things wrong has real consequences.
LiveKit Agents GPT-4o-mini Deepgram STT Deepgram Aura TTS PostgreSQL
Repo
</td>
<td width="50%" valign="top">
Resume-Grounded Interview Agent
Sub-500ms voice interviewer
Answers strictly from a candidate's resume + target JD, with hallucination prevention as the central design constraint, not an afterthought. Accepts a live JD over a data channel and adapts mid-conversation.
Hit sub-500ms round-trip latency using Cartesia for both STT and TTS, and kept grounding lightweight — direct retrieval over the resume/JD text instead of a heavier RAG pipeline.
LiveKit Agents GPT-4o-mini Cartesia STT/TTS Docker Next.js
Repo · Live demo
</td>
</tr>
</table>
</div>
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad3" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad3)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Architecture I Ship
Every voice AI system I build follows the same shape:
plain
Frontend  →  LiveKit Token API  →  Independent Voice Agent  →  Shared PostgreSQL  ←  Dashboard
The agent is never embedded in the frontend. It's its own deployable process — I can patch a prompt or roll back a bad change without touching the frontend at all.
The database is the single source of truth. No sync job, no webhook relay, no eventual-consistency window. If it's in the DB, every consumer sees it immediately.
Correctness lives at the database layer. Atomic CTEs hold regardless of how many agent instances are running.
Failure is isolated. A crash in one conversation can't take down the token API or other in-flight calls.
This same shape — frontend, token API, independent agent, shared database, dashboard — powers the hotel receptionist, the outbound dialer, the insurance assistant, and the library kiosk. The domain logic changes; the shape underneath doesn't.
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad4" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad4)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Stack
<div align="center">
Voice AI
 LiveKit 
 
 Sarvam 
 
 Deepgram 
 
 Cartesia 
 
 Silero 
Backend
 Python 
 
 FastAPI 
 
 Flask 
 
 asyncpg 
Frontend
 Next.js 
 
 React 
 
 TypeScript 
 
 TailwindCSS 
Machine Learning
 TensorFlow 
 
 Keras 
 
 scikit--learn 
 
 Pandas 
Data & Infra
 PostgreSQL 
 
 Neon 
 
 Docker 
 
 Vercel 
 
 AWS 
 
 Git 
</div>
<br/>
<div align="center">
<img src="https://skillicons.dev/icons?i=python,fastapi,flask,postgresql,nextjs,react,typescript,tailwind,tensorflow,docker,aws,vercel,git,github&theme=dark" />
</div>
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad5" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad5)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Repositories
<div align="center">
Table
Repo	What it is
https://github.com/MAdityaRao/resume_web	This portfolio site — Next.js + live voice agent "Sharanya"
https://github.com/MAdityaRao/torq_web_agent	Torq hotel voice receptionist + management dashboard
https://github.com/MAdityaRao/Mvp_ins	AI Insurance Voice Agent Platform ("Arria")
https://github.com/MAdityaRao/Resume_agent	Resume-grounded voice interview agent
https://github.com/MAdityaRao/arecanut_lstm	Arecanut price forecasting — LSTM model
https://github.com/MAdityaRao/arecanut_rnn	Arecanut price forecasting — SimpleRNN comparison
https://github.com/MAdityaRao/nurse_note_ai	Nursing/clinical note AI tooling
https://github.com/MAdityaRao/pdubelman_temple	Temple website build
https://github.com/MAdityaRao/mar-lang / https://github.com/MAdityaRao/marlang_web	Language project + web frontend
https://github.com/MAdityaRao/farm_finance	Agricultural finance project
https://github.com/MAdityaRao/java_dsa	Data structures & algorithms practice, Java
</div>
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad6" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad6)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Principles
Clean code over clever code. A slightly longer function that's obvious to debug at 2am beats a compact one I have to re-derive every time.
Observability from the start. OpenTelemetry tracing ships with the feature, not after the first incident.
Transactions as a design tool. I default to atomic writes for shared state because I don't trust myself to spot a race condition by eyeballing code.
Shared database over sync layers. Every extra sync mechanism is another place for state to drift.
Ship simple, refine under real load — rather than pre-optimizing for failure modes that may never occur.
Avoid over-engineering. Independent instances, atomic transactions, and tracing exist because I've watched the alternative break in production — not because they're impressive.
<br/>
<!-- Custom animated SVG divider -->
<div align="center">
<svg width="100%" height="30" viewBox="0 0 1200 30" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="lineGrad7" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#17130F;stop-opacity:0" />
      <stop offset="20%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="80%" style="stop-color:#D97757;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#17130F;stop-opacity:0" />
    </linearGradient>
  </defs>
  <line x1="0" y1="15" x2="1200" y2="15" stroke="url(#lineGrad7)" stroke-width="2" />
  <circle cx="600" cy="15" r="4" fill="#D97757">
    <animate attributeName="r" values="4;6;4" dur="2s" repeatCount="indefinite" />
    <animate attributeName="opacity" values="1;0.5;1" dur="2s" repeatCount="indefinite" />
  </circle>
</svg>
</div>
<br/>
Let's Build
If you're working on voice AI that needs to hold up under real call volume, LLM applications where hallucination isn't acceptable, backend systems with concurrent writes that must stay correct, or AI calling infrastructure — I'd like to hear about it.
<div align="center">
mailto:madityarao5@gmail.com
https://resume-web-liard.vercel.app/
https://github.com/MAdityaRao
<br/>
 Profile Views 
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:17130F,40:2A1F17,70:3D2A1E,100:D97757&height=100&section=footer"/>
</div>