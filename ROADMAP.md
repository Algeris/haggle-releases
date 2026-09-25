# Haggle Roadmap

*Last updated: September 24th 2026*

## Vision

Transform live conversational audio into real-time conversation intelligence, contextual recall, and strategic wit across meetings, pitches, interviews, dates, and debates.

---

## Live & Shipped Features (v0.1.2)

### 1. Call Center Built-in Mode (9th Persona)
**Status:** Shipped (v0.1.2)  
**Access:** All tiers (Standard & Pro)

Dedicated customer support and IT helpdesk mode:
- **Diagnostic Workflows**: Step-by-step troubleshooting, root-cause hypothesis generation, and verification checks.
- **Empathetic De-escalation**: Tailored customer validation phrases and calming scripts.
- **Structured Escalation Notes**: Formatted sections for `Customer Issue & Intent`, `Diagnostic Steps Attempted`, `Root Cause & Resolution`, and `Escalation & Follow-up Details`.

### 2. Hands-Free Auto Answer Engine (Beta)
**Status:** Shipped (v0.1.2)  
**Access:** Haggle Pro / Elite (and Trial)

Ambient background conversation evaluator:
- **Zero-Touch Assistance**: Detects actionable interviewer questions and triggers answers without requiring hotkeys.
- **Noise & Backchannel Suppression**: Actively suppresses casual conversational fillers (*"yeah"*, *"okay"*, *"sounds good"*, etc.) and short fragments (<3 words).
- **Confidence & Cooldown Gating**: Requires high STT confidence (≥0.6) and manages answer flight state to prevent prompt races.
- **Global Control**: Toggle with `CommandOrControl+Shift+A` or click the glowing top-pill status indicator.

### 3. Apple Speech On-Device STT (macOS 26+)
**Status:** Shipped (v0.1.2)  
**Access:** All tiers (Local & Free)

Native macOS speech recognition:
- **100% On-Device**: Built on Apple's modern Speech framework (`SpeechAnalyzer` / `SpeechTranscriber`).
- **Zero Latency & Cost**: Instant real-time transcription without cloud latency or third-party API keys.
- **Fail-Safe Parity**: Gracefully falls back on Windows/Linux without runtime errors.

### 4. NVIDIA NIM & Riva Streaming STT Provider
**Status:** Shipped (v0.1.2)  
**Access:** BYOK / Self-Hosted

High-throughput speech-to-text:
- **NVIDIA Cloud Functions**: Supports Parakeet-CTC 1.1B, Parakeet-TDT, and Canary-1B.
- **Self-Hosted Riva**: Configurable custom endpoints for on-prem enterprise deployments.
- **Smart Audio Framing**: VAD boundary detection, RMS silence energy gating, and automatic 16kHz resampling.

### 5. CJK Meeting PDF Export Engine
**Status:** Shipped (v0.1.2)  
**Access:** All tiers

High-fidelity document export:
- **CJK Ideographic Line-Breaking**: Native CSS word-break and strict line-breaking rules preventing broken Asian typography across Chinese, Japanese, and Korean text.
- **Offscreen PDF Rendering**: Crisp, print-ready A4 formatting directly from Electron.
- **One-Click Action**: Accessible directly in the Meeting Details dashboard alongside Markdown, JSON, and Text export.

### 6. Cloudflare Global Edge & Autonomous Agent Infrastructure
**Status:** Shipped & Cut Over (v0.1.2)  
**Access:** All tiers (`edge.algeris.com` + OCI Private Runtime)

Distributed low-latency intelligence network:
- **Zero-Egress Vision OCR (`/v1/ocr`)**: Native sub-second document and screen multimodal analysis powered by Workers AI Llama 4 Scout.
- **Streaming Chat with Automated Groq Failover (`/v1/chat`)**: Sub-150ms time-to-first-token running on Workers AI with zero-downtime failover to Groq (`openai/gpt-oss-120b`).
- **Dual-Path Speech-to-Text Relay (`/v1/stt/relay`)**: In-isolate Workers AI Deepgram Nova-3 WebSocket binding (zero external tokens) and AI Gateway provider-native relay, preserving client wire compatibility with standby OCI failover.
- **Autonomous Agent Runtime (`/v1/missions/*`)**: Outbound-only private VPC tunnel execution on Oracle VM with BullMQ persistent queue, 3-tier policy safety boundaries, and a 5-tier resilient model fallback cascade (Anthropic Claude $\rightarrow$ Google Gemini $\rightarrow$ Hugging Face $\rightarrow$ Groq $\rightarrow$ Safe Simulation).

---

## Live & Shipped Features (v0.1.1)

### 1. Expert Persona Modes Engine
**Status:** Shipped (v0.1.1)  
**Access:** Haggle Pro / Elite

Pre-configured expert modes tailored for any conversation:
- **Sales & Negotiation**: Real-time objection handling, value framing, pricing tactics.
- **System Architecture**: Technical depth, scalability patterns, distributed systems.
- **Executive & Strategy**: High-level synthesis, risk assessment, strategic alignment.
- **Custom Persona Prompting**: User-customizable prompt instructions.

### 2. Profile & Context Intelligence
**Status:** Shipped (v0.1.1)  
**Access:** Haggle Pro / Elite

- Local vector embeddings for user profile, resume, role expectations, and company context.
- Zero-latency context retrieval during live calls.

---

## Planned Features

### 1. Live System Design & Architecture Visualization
**Status:** In Development  
**Priority:** High

Visual diagram generation directly from real-time meeting technical discussions:
- Automatic architecture diagrams (microservices, event-driven pipelines, data models).
- Interactive Mermaid, SVG, and PNG export.
- Cross-session architectural memory.

### 2. Multi-Platform Phone Mirroring & Mobile Companion
**Status:** Planned  
**Priority:** Medium-High

- Real-time mobile meeting accompaniment and screen stream analysis.
- Instant haptic coaching signals.

### 3. Enterprise Team Intelligence & Governance
**Status:** Planned  
**Priority:** Medium

- Centralized team seat management and custom enterprise LLM endpoints.
- Role-based compliance logs and self-hosted relay options.

---

## Community & Feedback

We welcome feature requests and community feedback:
- **Website**: [https://haggle.algeris.com](https://haggle.algeris.com)
- **Feature Requests & Issues**: [https://github.com/Algeris/haggle-releases/issues](https://github.com/Algeris/haggle-releases/issues)
- **Releases & Downloads**: [https://github.com/Algeris/haggle-releases/releases](https://github.com/Algeris/haggle-releases/releases)

---

## Official Links

- **Terms of Service**: [https://haggle.algeris.com/terms](https://haggle.algeris.com/terms)
- **Privacy Policy**: [https://haggle.algeris.com/privacy](https://haggle.algeris.com/privacy)
- **Refund Policy**: [https://github.com/Algeris/haggle-releases/blob/main/refund.md](https://github.com/Algeris/haggle-releases/blob/main/refund.md)
