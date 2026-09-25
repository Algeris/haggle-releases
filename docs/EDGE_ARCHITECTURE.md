# Haggle Edge & Cloud Architecture

## Overview

Haggle delivers an ultra-low latency, real-time negotiation and voice coaching experience. To support high-fidelity audio transcription, responsive vision recognition, and autonomous workflow assistance, Haggle utilizes a distributed hybrid architecture:

1. **Global Edge Network (`edge.algeris.com`)**: Powered by Cloudflare's globally distributed Anycast network, terminating user connections in milliseconds close to the user.
2. **Asynchronous Agent Runtime (`haggle-oracle`)**: A high-performance execution cluster hosted on Oracle Cloud Infrastructure (OCI) designed for long-duration background missions and reasoning workflows without tying up edge request lifecycles.
3. **Legacy Control Plane (`api.algeris.com` & `stt.algeris.com`)**: Maintained with 100% uptime for billing, license verification, and emergency fallback.

```
+--------------------------------------------------------------------------+
|                           Haggle Desktop Client                          |
|         Real-time Voice Overlay | Stealth Mode | AI Copilot              |
+--------------------------------------------------------------------------+
                                    |
                    HTTPS / Secure WebSockets (WSS)
                                    v
+--------------------------------------------------------------------------+
|                 Haggle Global Edge Layer (edge.algeris.com)               |
|                                                                          |
|  * POST /v1/ocr       Workers AI Multimodal (Llama 4 Scout / Gemma 3)    |
|  * POST /v1/chat      Workers AI Streaming (Llama 3.3) -> Groq Fallback  |
|  * WS   /v1/stt/relay 4-Tier Nova-3 STT Ladder (Binding -> AI Gateway)   |
|  * POST /v1/missions  HMAC-SHA256 Signed Dispatch & KV Status Store      |
+--------------------------------------------------------------------------+
                                    |
                    Cloudflare VPC Tunnel (Encrypted QUIC)
                       No public open ports on host
                                    v
+--------------------------------------------------------------------------+
|                 Autonomous Agent Runtime (haggle-oracle)                 |
|                                                                          |
|  * Ingress Bridge: Express 4.19 (127.0.0.1:8090) + HMAC verification     |
|  * Persistent Queue: BullMQ on Redis 7 Alpine                            |
|  * Multi-Model Cascade: Claude -> Gemini -> Hugging Face -> Groq -> Sim  |
|  * Policy Enforcement: 3-tier safety boundaries (personal/unattended)   |
|  * Verified Callbacks: Signed status delivery back to Cloudflare Edge    |
+--------------------------------------------------------------------------+
```

---

## Key Platform Capabilities & Endpoints

### 1. Optical Character Recognition (`POST /v1/ocr`)
Haggle's edge tier processes screenshot and document frames directly on edge inference hardware:
- **Sub-second transcription** of onscreen dialogs, code editors, contracts, and negotiation terms.
- **Multimodal Models**: Powered primarily by `@cf/meta/llama-4-scout-17b-16e-instruct` with seamless fallback to `@cf/google/gemma-3-12b-it`.
- **In-Isolate Execution**: Memory-efficient 32KB chunked base64 processing within Cloudflare V8 isolates, enforcing a strict 10MB payload ceiling.
- **Privacy Preservation**: Image data is analyzed ephemerally and is never stored permanently or used for model training.

### 2. Low-Latency Streaming Chat (`POST /v1/chat`)
Chat and live coaching recommendations run through a high-availability fallback ladder:
- **Fast Tier (`fast_mode: true`)**: `@cf/meta/llama-3.1-8b-instruct-fast` for sub-150ms time-to-first-token (TTFT).
- **Quality Tier (`fast_mode: false`)**: `@cf/meta/llama-3.3-70b-instruct-fp8-fast` for complex reasoning and synthesis.
- **Automated Failover**: In the event of Workers AI rate limits or service interruptions, requests seamlessly fail over to Groq's high-throughput engine (`openai/gpt-oss-120b`) without dropping the client's Server-Sent Events (SSE) stream.

### 3. Real-Time Speech-to-Text (`WS /v1/stt/relay`)
Voice interactions are streamed continuously over secure WebSockets using 16kHz linear PCM audio through a bounded 4-tier STT ladder:

```
[Desktop Audio Stream]
        │
        ▼
[Rung 1A: Workers AI In-Isolate Binding] (@cf/deepgram/nova-3, zero external tokens)
        │ (if unavailable)
        ▼
[Rung 1B: AI Gateway Provider-Native] (deepgram/v1/listen via DEEPGRAM_API_KEY)
        │ (if gateway error)
        ▼
[Rung 2: Direct Deepgram WebSocket] (api.deepgram.com via DEEPGRAM_API_KEY)
        │ (if platform outage)
        ▼
[Rung 3: Standby OCI Relay] (stt.algeris.com:8080 via OCI VM)
```

- **Wire Compatibility**: The edge protocol adapter automatically transforms raw transcription events into Haggle's standard client wire format (`{status: 'connected', provider: 'deepgram'}`, `{text, is_final, confidence, words}`, `{type: 'utterance_end'}`), maintaining 100% backward compatibility with `HaggleProSTT.ts`.
- **Integrated VAD**: Smart endpointing distinguishes natural conversational pauses from turn completions.

### 4. Autonomous Agent Missions (`/v1/missions/*`)
For complex tasks that require background research, multi-source analysis, or long-running execution:
- **`POST /v1/missions/start`**: The edge verifies user authorization, issues an asynchronous mission dispatch signed with HMAC-SHA256, writes initial status (`queued`) to Cloudflare KV (`MISSIONS_KV` with 24-hour TTL), and forwards the request over the Cloudflare VPC tunnel to the OCI agent runtime.
- **Multi-Provider Fallback Cascade**: To ensure autonomous missions always complete, `haggle-oracle` implements a 5-tier execution cascade:
  1. **Anthropic Claude**: `claude-3-5-sonnet-20241022` for state-of-the-art tool orchestration.
  2. **Google Gemini**: Dynamic failover across `gemini-3.8-flash`, `gemini-3.5-flash`, `gemini-flash-lite-latest`, and `gemini-3.1-flash-lite` via `GEMINI_API_KEY`.
  3. **Hugging Face Router**: Fast instruction models (`meta-llama/Llama-3.1-8B-Instruct`) via `HUGGINGFACE_API_KEY` (gracefully catching quota/credit limits).
  4. **Groq**: High-speed completions (`openai/gpt-oss-120b`) via `GROQ_API_KEY`.
  5. **Safe Simulation Mode**: Autonomous policy simulation ensuring tasks always resolve gracefully.
- **`POST /v1/missions/callback`**: The agent runtime posts completion payloads back to the edge, signed with HMAC-SHA256, transitioning the status to `done` or `failed`.
- **`GET /v1/missions/:id`**: Returns current state and final results directly from edge KV cache.

---

## Security & Privacy Principles

- **Zero Inbound Attack Surface**: The OCI agent runtime VM exposes **no public listening ports** on port 8090. Inbound traffic is strictly restricted by host firewalls (`iptables`), and the Express bridge listens solely on `127.0.0.1:8090`. All edge communication is tunneled through outbound QUIC connections managed by `cloudflared`.
- **End-to-End Encryption**: All external client traffic is encrypted in transit using TLS 1.3 over Cloudflare's global edge.
- **Cryptographic Non-Repudiation**: Inter-service communication between Cloudflare Edge and the Oracle Bridge requires timing-safe HMAC-SHA256 signature verification (`X-Oracle-Signature` and `X-Oracle-Timestamp`).
- **Principle of Least Privilege**: Agent operations adhere to strict permission policies (`personal`, `unattended`, `strict`) governing tool interactions and system access.
- **Zero Plaintext Credentials**: All API keys and secrets are injected via secure keystores (Cloudflare Wrangler Secrets and encrypted server environment files) and are never exposed to clients or public repositories.
