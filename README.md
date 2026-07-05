<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=37&pause=1000&color=6366F1&center=true&vCenter=true&width=700&lines=Realis;Real-Time+Voice+Intelligence;Speak.+Think.+Respond." alt="Realis" />

<br/>

<table>
<tr>
<td align="center">

`AI Voice Assistant`

</td>
<td align="center">

`< 1.5s end-to-end`

</td>
<td align="center">

`Live & Deployed`

</td>
<td align="center">

`MIT License`

</td>
</tr>
</table>

<br/>

### [→ Launch the Live Demo](https://real-time-voice-assistant-one.vercel.app/login)

<br/>

`React 18` · `FastAPI 0.111` · `Whisper (OpenAI)` · `Groq Llama 3.3` · `Supabase` · `WebSockets` · `Tailwind v3` · `Vercel` · `Render`

</div>

<br>

---

## Contents

`Screenshots` · `Overview` · `Architecture` · `Latency Budget` · `Features` · `Tech Stack` · `Local Setup` · `Deployment` · `File Structure` · `What I Learned` · `Roadmap`

---

<br>

## Screenshots

<table>
<tr>
<td width="50%" align="center"><b>Dashboard — Voice Interface</b></td>
<td width="50%" align="center"><b>Analytics — Live Latency Breakdown</b></td>
</tr>
<tr>
<td><img src="./Screenshot1.png.png" width="100%" /></td>
<td><img src="./Screenshot2.png.png" width="100%" /></td>
</tr>
</table>

<br>

## Overview

**Realis** is a production-grade, real-time voice assistant that turns your speech into an intelligent AI response and speaks it back — in under **1.5 seconds**.

It's built to demonstrate mastery of streaming systems, latency engineering, and resilient AI pipelines, not to be a toy chatbot.

<div align="center">

| Speak | → | Transcribe | → | Reason | → | Synthesize | → | Hear |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 🎙️ | | Whisper ASR<br>`~340ms` | | Groq Llama 3.3<br>`~620ms` | | gTTS<br>`~280ms` | | 🔊 |

**Total round trip: ~1.24s**

</div>

<br>

> **Demo recording:** add a screen-capture GIF here once recorded — [Loom](https://loom.com) or [ScreenToGif](https://www.screentogif.com/) both work well for this.

<br>

## Architecture

Realis is built across **three engineering phases**, each addressing a real production concern.

```
                    ┌───────────────────────────────┐
                    │           Auth Layer            │
                    │  Supabase JWT · Protected Routes │
                    └────────────────┬─────────────────┘
                                     │ WebSocket
                                     ▼
        ┌────────────┐      ┌───────────────┐      ┌────────────┐
        │   ASR       │ ──▶  │     LLM       │ ──▶  │    TTS      │
        │  Whisper    │      │  Groq Llama   │      │   gTTS      │
        │  ~340ms     │      │   ~620ms      │      │  ~280ms     │
        └─────┬──────┘      └───────┬───────┘      └──────┬─────┘
              │                     │                      │
              └─────────────────────┼──────────────────────┘
                                    ▼
                        ┌──────────────────────┐
                        │   Latency Tracker      │
                        │  Supabase Postgres     │
                        │  Recharts Dashboard    │
                        └──────────────────────┘
```

<table>
<tr><td width="33%" valign="top">

**Phase 1 — Pipeline**

Raw audio streams over a WebSocket connection. Each stage emits a live status event on completion, so the UI updates progressively instead of waiting on the full round trip.

</td><td width="33%" valign="top">

**Phase 2 — Visualization**

Every request is fully instrumented — ASR latency, LLM time-to-first-token, and TTS first-byte are each measured, stored in Supabase, and charted on `/analytics`.

</td><td width="33%" valign="top">

**Phase 3 — Resilience**

Every component has a hard timeout. Failures degrade gracefully rather than hanging. A Replay Mode reruns recorded audio through the pipeline for debugging.

</td></tr>
</table>

<br>

## Latency Budget

> Measured on Groq free tier + local Whisper `base` model, mid-range laptop.

<div align="center">

| Component | Tech | Target | Typical |
|:---|:---|:---:|:---:|
| Speech Recognition | Whisper `base` (local) | < 500ms | **~340ms** |
| AI Reasoning | Groq · Llama 3.3 70B | < 800ms | **~620ms** |
| Voice Synthesis | gTTS | < 400ms | **~280ms** |
| WebSocket overhead | FastAPI + asyncio | < 50ms | **~20ms** |
| **Total** | **End-to-end** | **< 2s** | **~1.26s** |

</div>

All of this is live on the analytics dashboard — every request draws a new bar on the chart.

<br>

## Features

<table>
<tr><td width="50%" valign="top">

### Core Voice Pipeline
- Browser mic → Whisper ASR → Groq LLM → gTTS → playback
- Live stage-by-stage status via WebSocket
- Sub-1.5 second total response time

### Authentication
- Email/password signup & login via Supabase Auth
- JWT-protected routes — API calls and DB rows scoped per user
- Row-Level Security on Postgres

### Analytics Dashboard
- Stacked bar chart (last 20 requests) by ASR / LLM / TTS
- 4 live stat cards with health indicators
- Latest-request proportional latency bar
- Auto-refreshes every 3 seconds

</td><td width="50%" valign="top">

### Production Resilience

| Failure | Response |
|---|---|
| ASR timeout (>10s) | Text-input fallback |
| LLM timeout (>8s) | Graceful fallback text |
| TTS failure | Text-only mode — never silent |
| WebSocket drop | Auto-retry: 1s → 2s → 4s |
| React crash | ErrorBoundary fallback |

### Replay Mode
- Upload or select a prior recording
- Reruns through the full pipeline
- Side-by-side latency: original vs replay
- Tagged `is_replay: true` to keep metrics clean

### Connection Health
- Navbar status pill: Connected / Reconnecting / Disconnected
- Exponential backoff on reconnect

</td></tr>
</table>

<br>

## Tech Stack

<table>
<tr><td width="33%" valign="top">

**Frontend**

| Tool | Purpose |
|---|---|
| React 18 + Vite | UI + dev server |
| Tailwind CSS v3 | Styling |
| Recharts | Latency charts |
| React Router v6 | Routing |
| supabase-js | Auth + DB client |

</td><td width="33%" valign="top">

**Backend**

| Tool | Purpose |
|---|---|
| FastAPI | Async API + WebSocket |
| uvicorn | ASGI server |
| openai-whisper | Local STT |
| groq SDK | LLM inference |
| gTTS | Text-to-speech |
| supabase-py | Server-side DB writes |

</td><td width="33%" valign="top">

**Infrastructure** *(100% free)*

| Service | Role |
|---|---|
| Supabase | Postgres + Auth + RLS |
| Vercel | Frontend hosting |
| Render | Backend hosting |
| GitHub Actions | CI on push |

</td></tr>
</table>

<br>

## Local Setup

**Prerequisites:** Node.js 18+, Python 3.11+, a free [Groq API key](https://console.groq.com), a free [Supabase project](https://supabase.com)

<br>

**1 · Clone**
```bash
git clone https://github.com/YOUR_USERNAME/realis.git
cd realis
```

**2 · Set up the Supabase database** — in SQL Editor, run:
```sql
CREATE TABLE session_metrics (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users(id) ON DELETE CASCADE,
  asr_latency_ms integer,
  llm_latency_ms integer,
  tts_latency_ms integer,
  total_latency_ms integer,
  transcript text,
  response text,
  is_replay boolean DEFAULT false,
  created_at timestamptz DEFAULT now()
);

ALTER TABLE session_metrics ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can only see their own metrics"
  ON session_metrics FOR ALL USING (auth.uid() = user_id);
```

**3 · Configure environment variables**
```bash
cp .env.example backend/.env
cp .env.example frontend/.env.local
```

```env
# backend/.env
GROQ_API_KEY=gsk_...
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
ALLOWED_ORIGINS=http://localhost:5173

# frontend/.env.local
VITE_SUPABASE_URL=https://xxxx.supabase.co
VITE_SUPABASE_ANON_KEY=eyJ...
VITE_WS_URL=ws://localhost:8000
VITE_API_URL=http://localhost:8000
```

> `SUPABASE_SERVICE_KEY` is the **service_role** key — never expose it in the frontend.

**4 · Start the backend**
```bash
cd backend
python -m venv venv
source venv/bin/activate     # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```

First run downloads the Whisper `base` model (~140MB, ~2 min). Subsequent starts are instant.

```
⏳ Loading Whisper model at startup...
✅ Whisper 'base' model loaded. Server ready.
INFO:     Uvicorn running on http://0.0.0.0:8000
```

**5 · Start the frontend**
```bash
cd frontend
npm install
npm run dev
```

Open **http://localhost:5173** → sign up → start talking.

<br>

## Deployment

<table>
<tr><td width="50%" valign="top">

### Frontend → Vercel *(free)*
1. Push this repo to GitHub
2. [vercel.com](https://vercel.com) → Import project → root directory `frontend`
3. Add env vars:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
   - `VITE_WS_URL` → Render URL, `wss://...`
   - `VITE_API_URL` → Render URL, `https://...`
4. Deploy

</td><td width="50%" valign="top">

### Backend → Render *(free)*
1. [render.com](https://render.com) → New Web Service → connect GitHub
2. Root directory: `backend`
3. Build: `pip install -r requirements.txt`
4. Start: `uvicorn main:app --host 0.0.0.0 --port $PORT`
5. Env vars: `GROQ_API_KEY`, `SUPABASE_URL`, `SUPABASE_SERVICE_KEY`, `ALLOWED_ORIGINS`
6. Deploy

> Free tier sleeps after 15 min idle — first request may take ~30s. Upgrade to Starter ($7/mo) to remove cold starts.

</td></tr>
</table>

<br>

## File Structure

```
realis/
├── .env.example
├── .github/workflows/ci.yml
├── README.md
├── backend/
│   ├── main.py                      FastAPI app, CORS, lifespan
│   ├── requirements.txt
│   ├── render.yaml
│   ├── routers/
│   │   ├── websocket.py             /ws/{user_id} — full pipeline
│   │   └── auth.py                  POST /api/replay
│   ├── services/
│   │   ├── asr_service.py           Whisper + 10s timeout
│   │   ├── llm_service.py           Groq Llama 3.3 + 8s timeout
│   │   └── tts_service.py           gTTS + 5s timeout + fallback
│   ├── models/schemas.py            Pydantic request/response models
│   └── utils/supabase_client.py     DB singleton + save_session_metrics
└── frontend/
    ├── vercel.json                  SPA rewrite + security headers
    ├── src/
    │   ├── config.js                All URLs from env
    │   ├── lib/supabase.js          Supabase browser client
    │   ├── context/
    │   │   ├── AuthContext.jsx      session, signIn, signUp, signOut
    │   │   └── WebSocketContext.jsx WS status for Navbar pill
    │   ├── hooks/useWebSocket.js    WS lifecycle + exponential backoff
    │   ├── components/
    │   │   ├── Auth/                LoginForm, SignupForm
    │   │   ├── Layout/               Navbar, ProtectedRoute
    │   │   ├── VoiceAssistant/       MicButton, Transcript, Response, ReplayMode
    │   │   ├── Dashboard/            LatencyDashboard (Recharts)
    │   │   └── ErrorBoundary.jsx    React crash fallback
    │   └── pages/
    │       ├── LoginPage.jsx
    │       ├── SignupPage.jsx
    │       ├── DashboardPage.jsx     Main voice UI
    │       └── AnalyticsPage.jsx     /analytics route
    └── screenshots/
        ├── dashboard.png
        └── analytics.png
```

<br>

## What I Learned

**Streaming systems are non-trivial.** Getting binary audio to flow over a WebSocket, through three sequential AI services, and back as audio in under 1.5 seconds required careful async orchestration with `asyncio`. The happy path is easy — handling timeouts, partial failures, and reconnections is where the real engineering happens.

**Latency budgets change how you build.** Instrumenting each stage individually — ASR, LLM time-to-first-token, TTS first-byte — forced clarity on *where* time was actually going. That's the difference between "it feels slow" and "LLM inference is 680ms; streaming tokens could cut perceived latency by 40%."

**Graceful degradation is a design discipline.** Planning fallback behavior before writing the happy path produced a more robust system. The text fallback when TTS fails, the text-input fallback when ASR times out, and Replay Mode for debugging all came from asking: what breaks, and what should happen when it does?

<br>

## Roadmap

- [ ] **Streaming TTS** — swap gTTS for [Cartesia](https://cartesia.ai) or [ElevenLabs](https://elevenlabs.io) for real-time audio streaming (~200ms perceived latency reduction)
- [ ] **Better ASR** — integrate [Deepgram Nova-2](https://deepgram.com) for cloud ASR with word-level timestamps
- [ ] **Conversation memory** — rolling context window so the assistant remembers prior turns
- [ ] **Mobile support** — optimize MediaRecorder settings for iOS Safari
- [ ] **Multilingual** — expose Whisper's 99-language support in the UI
- [ ] **Cost dashboard** — track estimated API cost per session alongside latency

<br>

## License

MIT © 2025 — built as a final-year CSE portfolio project.

---

<div align="center">

**If this project helped you, a ⭐ on GitHub goes a long way.**

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=14&pause=2000&color=6366F1&center=true&vCenter=true&width=400&lines=Built+with+FastAPI+%2B+React+%2B+Whisper;100%25+free+to+run+%26+deploy;Made+for+learning%2C+built+for+production" alt="footer" />

</div>
Built to demonstrate mastery of **streaming systems, latency engineering, and resilient AI pipelines** — not just a toy chatbot.

```
You speak  →  [Whisper ASR]  →  [Groq LLaMA 3.3]  →  [gTTS]  →  You hear
              ~340ms              ~620ms               ~280ms       Total ~1.24s
```

---

## 🎬 Demo

> *Add a screen recording GIF here once you record a demo.*
> Use [Loom](https://loom.com) or [ScreenToGif](https://www.screentogif.com/) to record, then drag the GIF into this README on GitHub.

```
[ 🎥 Replace this block with your demo GIF ]
```

---

## 🗂️ Table of Contents

- [📸 Screenshots](#-screenshots)
- [✨ What is VoxMind?](#-what-is-voxmind)
- [🏗️ Architecture](#️-architecture)
- [⚡ Latency Budget](#-latency-budget)
- [🌟 Features](#-features)
- [🛠️ Tech Stack](#️-tech-stack)
- [🚀 Local Setup](#-local-setup)
- [☁️ Deployment](#️-deployment)
- [📁 File Structure](#-file-structure)
- [🧠 What I Learned](#-what-i-learned)
- [🔮 Future Improvements](#-future-improvements)

---

## 🏗️ Architecture

VoxMind is built in **three engineering phases**, each representing a real production concern:

```
┌─────────────────────────────────────────────────────────────┐
│                    🔐 Auth Layer                             │
│         Supabase JWT · Login / Signup · Protected Routes    │
└───────────────────────┬─────────────────────────────────────┘
                        │ WebSocket
                        ▼
┌─────────┐      ┌─────────────┐      ┌───────────┐
│  🎤 ASR  │ ───▶ │  🧠  LLM    │ ───▶ │  🔊 TTS   │
│ Whisper  │      │ Groq Llama  │      │   gTTS    │
│ ~340ms   │      │   ~620ms    │      │  ~280ms   │
└─────────┘      └─────────────┘      └───────────┘
     │                  │                   │
     └──────────────────┴───────────────────┘
                        │
              ┌─────────▼──────────┐
              │ 📊 Latency Tracker  │
              │  Supabase Postgres  │
              │  Recharts Dashboard │
              └────────────────────┘
```

### Phase 1 — End-to-End Pipeline 🔗
Raw audio bytes stream over a **WebSocket** connection. Each stage emits a live status event as it completes, so the frontend updates progressively rather than waiting for everything at once.

### Phase 2 — Latency Budget Visualization 📊
Every request is fully instrumented. **ASR latency**, **LLM time-to-first-token**, and **TTS first-byte** are measured individually, stored in Supabase, and rendered as a **stacked bar chart** on the `/analytics` page. Recruiters can see real performance data, not just claims.

### Phase 3 — Resilience & Failure Handling 🛡️
Each component has a **hard timeout**. If any stage fails, the system degrades gracefully instead of hanging silently. A **Replay Mode** lets you re-run recorded audio through the pipeline for debugging — exactly how real production systems work.

---

## ⚡ Latency Budget

> Measured on Groq free tier + local Whisper base model on a mid-range laptop.

| Component | Tech | Target | Typical |
|-----------|------|--------|---------|
| 🎤 Speech Recognition | Whisper `base` (local) | < 500ms | ~340ms |
| 🧠 AI Reasoning | Groq · Llama 3.3 70B | < 800ms | ~620ms |
| 🔊 Voice Synthesis | gTTS | < 400ms | ~280ms |
| 🌐 WebSocket overhead | FastAPI + asyncio | < 50ms | ~20ms |
| **⏱️ Total** | **End-to-end** | **< 2s** | **~1.26s** |

> 📈 All of this is **live on the analytics dashboard** — every request generates a new bar on the chart.

---

## 🌟 Features

### 🎙️ Core Voice Pipeline
- Browser mic → Whisper ASR → Groq LLM → gTTS → audio playback
- Live stage-by-stage status updates via WebSocket
- Sub-1.5 second total response time

### 🔐 Authentication
- Email / password signup & login via Supabase Auth
- JWT-protected routes — all API calls and DB rows scoped to the user
- Row-Level Security on Postgres so users only see their own data

### 📊 Analytics Dashboard
- Stacked bar chart (last 20 requests) broken down by ASR / LLM / TTS
- 4 live stat cards with green/amber health indicators
- Latest-request proportional latency bar
- Auto-refreshes every 3 seconds

### 🛡️ Production Resilience
| Failure Scenario | Response |
|-----------------|----------|
| ASR timeout (>10s) | Text input fallback appears automatically |
| LLM timeout (>8s) | Graceful fallback text shown |
| TTS failure | Text-only mode — never silent |
| WebSocket drop | Auto-retry: 1s → 2s → 4s |
| React crash | ErrorBoundary with friendly message |

### 🔁 Replay Mode
- Upload or select a prior recording
- Re-runs through the full pipeline
- Side-by-side latency comparison: original vs replay
- Tagged in DB as `is_replay: true` so metrics stay clean

### 🌐 Connection Health
- Live Navbar pill: 🟢 Connected / 🟡 Reconnecting / 🔴 Disconnected
- Exponential backoff on reconnect

---

## 🛠️ Tech Stack

### Frontend
| Tool | Purpose |
|------|---------|
| React 18 + Vite | UI framework + fast dev server |
| Tailwind CSS v3 | Utility-first styling |
| Recharts | Latency visualisation charts |
| React Router v6 | Client-side routing |
| @supabase/supabase-js | Auth + DB client |

### Backend
| Tool | Purpose |
|------|---------|
| FastAPI | Async Python API + WebSocket server |
| uvicorn | ASGI server |
| openai-whisper | Local speech-to-text (no API cost) |
| groq SDK | LLM inference (Llama 3.3 70B) |
| gTTS | Text-to-speech synthesis |
| supabase-py | Server-side DB writes |

### Infrastructure (100% Free)
| Service | Role |
|---------|------|
| Supabase | Postgres DB + Auth + Row-Level Security |
| Vercel | Frontend hosting + CDN |
| Render | Backend hosting (FastAPI) |
| GitHub Actions | CI on every push |

---

## 🚀 Local Setup

### Prerequisites
- Node.js 18+
- Python 3.11+
- A Groq API key → [console.groq.com](https://console.groq.com) *(free)*
- A Supabase project → [supabase.com](https://supabase.com) *(free)*

### 1️⃣ Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/voxmind.git
cd voxmind
```

### 2️⃣ Set up the Supabase database
In your Supabase project → **SQL Editor**, run:
```sql
CREATE TABLE session_metrics (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users(id) ON DELETE CASCADE,
  asr_latency_ms integer,
  llm_latency_ms integer,
  tts_latency_ms integer,
  total_latency_ms integer,
  transcript text,
  response text,
  is_replay boolean DEFAULT false,
  created_at timestamptz DEFAULT now()
);

ALTER TABLE session_metrics ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can only see their own metrics"
  ON session_metrics FOR ALL USING (auth.uid() = user_id);
```

### 3️⃣ Configure environment variables
```bash
cp .env.example backend/.env
cp .env.example frontend/.env.local
# Open both files and fill in your keys
```

```env
# backend/.env
GROQ_API_KEY=gsk_...
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
ALLOWED_ORIGINS=http://localhost:5173

# frontend/.env.local
VITE_SUPABASE_URL=https://xxxx.supabase.co
VITE_SUPABASE_ANON_KEY=eyJ...
VITE_WS_URL=ws://localhost:8000
VITE_API_URL=http://localhost:8000
```

> ⚠️ `SUPABASE_SERVICE_KEY` is the **service_role** key — never expose it in the frontend.

### 4️⃣ Start the backend
```bash
cd backend
python -m venv venv
source venv/bin/activate     # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```

> 🕐 First run downloads Whisper `base` model (~140MB). Takes ~2 min. Subsequent starts are instant.

You should see:
```
⏳ Loading Whisper model at startup...
✅ Whisper 'base' model loaded. Server ready.
INFO:     Uvicorn running on http://0.0.0.0:8000
```

### 5️⃣ Start the frontend
```bash
# in a new terminal
cd frontend
npm install
npm run dev
```

Open **http://localhost:5173** → Sign up → start talking 🎙️

---

## ☁️ Deployment

### Frontend → Vercel (free)
1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → Import project → set **root directory** to `frontend`
3. Add these environment variables in Vercel dashboard:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
   - `VITE_WS_URL` → your Render backend URL (`wss://your-app.onrender.com`)
   - `VITE_API_URL` → your Render backend URL (`https://your-app.onrender.com`)
4. Deploy ✅

### Backend → Render (free)
1. Go to [render.com](https://render.com) → New Web Service → connect GitHub
2. Set **root directory** to `backend`
3. **Build command:** `pip install -r requirements.txt`
4. **Start command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`
5. Add env vars: `GROQ_API_KEY`, `SUPABASE_URL`, `SUPABASE_SERVICE_KEY`, `ALLOWED_ORIGINS` (your Vercel URL)
6. Deploy ✅

> 💡 Render free tier spins down after 15 min of inactivity. First request may take ~30s to wake up. Upgrade to Starter ($7/mo) to eliminate cold starts.

---

## 📁 File Structure

```
voxmind/
├── .env.example                     ← template for all env vars
├── .github/workflows/ci.yml         ← GitHub Actions CI
├── README.md
├── backend/
│   ├── main.py                      ← FastAPI app, CORS, lifespan
│   ├── requirements.txt
│   ├── render.yaml                  ← Render deploy config
│   ├── routers/
│   │   ├── websocket.py             ← /ws/{user_id} — full pipeline
│   │   └── auth.py                  ← POST /api/replay
│   ├── services/
│   │   ├── asr_service.py           ← Whisper + 10s hard timeout
│   │   ├── llm_service.py           ← Groq Llama 3.3 + 8s timeout
│   │   └── tts_service.py           ← gTTS + 5s timeout + fallback
│   ├── models/schemas.py            ← Pydantic request/response models
│   └── utils/supabase_client.py     ← DB singleton + save_session_metrics
└── frontend/
    ├── vercel.json                  ← SPA rewrite + security headers
    ├── src/
    │   ├── config.js                ← all URLs from env (no hardcoding)
    │   ├── lib/supabase.js          ← Supabase browser client init
    │   ├── context/
    │   │   ├── AuthContext.jsx      ← session, signIn, signUp, signOut
    │   │   └── WebSocketContext.jsx ← WS status for Navbar pill
    │   ├── hooks/useWebSocket.js    ← WS lifecycle + exponential backoff
    │   ├── components/
    │   │   ├── Auth/                ← LoginForm, SignupForm
    │   │   ├── Layout/              ← Navbar (status pill), ProtectedRoute
    │   │   ├── VoiceAssistant/      ← MicButton, Transcript, Response, ReplayMode
    │   │   ├── Dashboard/           ← LatencyDashboard (Recharts stacked chart)
    │   │   └── ErrorBoundary.jsx    ← React crash fallback
    │   └── pages/
    │       ├── LoginPage.jsx
    │       ├── SignupPage.jsx
    │       ├── DashboardPage.jsx    ← main voice UI
    │       └── AnalyticsPage.jsx    ← /analytics route
    └── screenshots/
        ├── dashboard.png
        └── analytics.png
```

---

## 🧠 What I Learned

- **Streaming systems are non-trivial.** Getting binary audio to flow over a WebSocket, through three sequential AI services, and back as audio in under 1.5 seconds required careful async orchestration with `asyncio`. The happy path is easy; handling timeouts, partial failures, and reconnections is where real engineering happens.

- **Latency budgets change how you build.** Instrumenting every stage individually — ASR, LLM time-to-first-token, TTS first-byte — forced me to understand *where* time was being spent. This is the difference between "it feels slow" and "our LLM inference is 680ms; if we switch to streaming tokens we can cut perceived latency by 40%."

- **Graceful degradation is a design discipline.** Planning fallback behavior *before* writing the happy path produced a more robust system. The audio-to-text fallback when TTS fails, the text-input fallback when ASR times out, and the replay mode for debugging all came from asking "what breaks, and what should happen when it does?"

---

## 🔮 Future Improvements

- [ ] 🎵 **Streaming TTS** — Switch from gTTS to [Cartesia](https://cartesia.ai) or [ElevenLabs](https://elevenlabs.io) for real-time audio streaming (reduces perceived TTS latency by ~200ms)
- [ ] 🎤 **Better ASR** — Integrate [Deepgram Nova-2](https://deepgram.com) for cloud ASR with word-level timestamps
- [ ] 🧠 **Conversation memory** — Add rolling context window so the assistant remembers previous turns in a session
- [ ] 📱 **Mobile support** — Optimize MediaRecorder settings for iOS Safari
- [ ] 🌍 **Multilingual** — Whisper supports 99 languages; expose language selection in UI
- [ ] 📉 **Cost dashboard** — Track estimated API cost per session alongside latency metrics

---

## 📄 License

MIT © 2025 — Built with 💜 as a final-year CSE portfolio project.

---

<div align="center">

**If this project helped you, consider giving it a ⭐ on GitHub!**

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=14&pause=2000&color=6366F1&center=true&vCenter=true&width=400&lines=Built+with+FastAPI+%2B+React+%2B+Whisper;100%25+free+to+run+%26+deploy;Made+for+learning%2C+built+for+production" alt="footer" />

</div>
