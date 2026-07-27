# orator-showcase
Daily public-speaking trainer with AI voice analysis — showcase
# Orator

**A daily public-speaking trainer that gives you an objective mirror of your own voice — pace, hesitations, vocal tension — instead of a subjective audience.**

Designed and architected solo, with a deliberate build-vs-scope discipline: ship the measurable core first, keep the ambitious long-term vision (simulated audience, scenario training) architecturally possible without a rewrite.

> This is a showcase repository for a product in active development. Source code is available privately on request — see [Access](#access) below.

---

## The problem

Most public-speaking apps jump straight to gamified simulation — virtual audiences, scored performances — before solving the more basic problem: most people have no accurate, repeatable feedback on how they actually sound. Orator starts there: record yourself, get concrete measurements (speaking rate, hesitation count, playback with annotations), and only build toward simulation once the measurement layer is solid.

## Product approach

A four-phase roadmap where every phase is scoped to ship independently, and the phase-1 architecture is deliberately built to absorb phase 4 (a full simulated-audience training mode) without a rewrite — the audio-analysis engine and the future scenario engine are decoupled from day one, not bolted together and split apart later.

## My role

Sole architect and developer: designed the three-tier system, made the call to isolate audio processing in its own service rather than force it into the business-logic stack, and wrote the resource guardrails that keep a compute-heavy service safe to run in production.

## Technical highlights

- **Right tool for the job, not one framework for everything** — the business logic (users, sessions, billing) stays in a conventional backend framework; audio signal processing (transcription, pause detection, pitch analysis) is delegated to a dedicated Python service, because that's where the mature libraries live. The two only ever communicate through a queue, never a blocking call.
- **Production guardrails for a compute-heavy service, written before the feature, not after an incident** — a hard cap on upload duration and file size, the transcription model loaded once as a singleton instead of per-request, a concurrency semaphore limiting simultaneous jobs per worker, per-job timeouts with guaranteed cleanup, and container-level CPU/RAM limits. This is the kind of operational thinking that usually only shows up after a service has fallen over once in production.
- **Data retention by policy, not by accident** — user audio is deleted automatically after a defined retention window unless the user explicitly opts to keep it, with temporary files guaranteed to be cleaned up via `try/finally` rather than relying on the happy path.
- **Async by construction** — mobile client uploads, backend queues the job, the analysis service processes independently and reports back via webhook/polling — so a slow transcription never blocks the app or ties up a web worker.

## Stack

Flutter (iOS + Android, single codebase) · Laravel 11 (business logic, auth, orchestration, queues via Horizon/Redis) · Python + FastAPI (audio analysis: Whisper transcription, voice-activity detection, signal processing) · MySQL/PostgreSQL · S3-compatible object storage

## Access

Full source and architecture docs available on request — happy to walk through the service-boundary decisions in an interview context.

---

*Part of a portfolio of 30+ shipped production projects — [see full CV / LinkedIn].*
