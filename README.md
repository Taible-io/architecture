# Taible-io — Architecture

Diagrams are [D2](https://d2lang.com) source, versioned in git. CI renders them to SVG on every push — the `.d2` is the source of truth, never hand-export.

## The system

A restaurant **voice agent**. A diner scans a QR at the table, talks to the agent in the browser over WebRTC, and a **self-hosted AMD GPU (ROCm)** pipeline does speech→LLM→speech. The LLM calls our MCP tools to read the menu and place orders.

**Canonical diagram:** [`diagrams/container/voice-agent.d2`](./diagrams/container/voice-agent.d2)

```
QR → PWA (WebRTC) → Pipecat/LiveKit (CPU)
      → AMD GPU/ROCm: Whisper STT → vLLM → Kokoro TTS
      → MCP tools → Postgres + POS
```

## Stack

| Stage | Tech | Host |
|---|---|---|
| STT | whisper.cpp (HIP) | **AMD GPU · ROCm** |
| LLM | vLLM — Llama 3.1 8B / Qwen 2.5 7B | **AMD GPU · ROCm** |
| TTS | Kokoro (PyTorch/ROCm) | **AMD GPU · ROCm** |
| Orchestration | Pipecat / LiveKit Agents | CPU |
| Tools | MCP server (`get_menu`, `place_order`, …) | small VPS / Fly.io |
| Data | Postgres / Supabase + POS/kitchen | managed |

Fits in **24 GB VRAM** (e.g. Radeon 7900 XTX). **AMD/ROCm on AMD cloud is a hard requirement** — no CUDA.

## Edit a diagram

1. Change a `.d2` under `diagrams/`.
2. Open a PR — CI checks it renders; merge to `main` writes the SVG to `rendered/`.

Preview locally:
```bash
curl -fsSL https://d2lang.com/install.sh | sh -s --
d2 diagrams/container/voice-agent.d2 out.svg
```

Decisions behind this setup: [`adr/`](./adr). New to Taible? Start at [playbok_startup](https://github.com/Taible-io/playbok_startup).
