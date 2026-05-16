# Realis

A production-grade real-time multimodal voice AI system focused on **latency engineering, streaming orchestration, and resilient AI pipelines**.

## Overview

Realis is a low-latency voice assistant that captures live audio, transcribes speech in real time, reasons over the input using LLMs, synthesizes natural voice responses, and streams them back to users.

The system is engineered to demonstrate:

* Real-time streaming architecture
* End-to-end latency decomposition
* Performance benchmarking
* Graceful degradation
* Timeout-aware recovery
* Production-grade observability

---

## Features

* Real-time microphone audio streaming
* Automatic Speech Recognition (ASR)
* LLM reasoning layer
* Natural voice synthesis
* WebSocket-based orchestration
* Latency dashboard
* Timeout handling & fallback routing
* Resilience-focused pipeline design

---

## Tech Stack

### Speech Recognition

* Deepgram / Whisper

### Reasoning Layer

* OpenAI / Claude

### Speech Synthesis

* ElevenLabs / Cartesia

### Backend

* Python
* FastAPI
* WebSockets
* AsyncIO

### Monitoring

* Custom latency instrumentation

---

## Architecture

```text
Microphone Input
   ↓
WebSocket Audio Stream
   ↓
ASR Engine
   ↓
Transcript Stabilization
   ↓
LLM Reasoning Layer
   ↓
Structured Response Generation
   ↓
TTS Synthesis
   ↓
Audio Playback
   ↓
Latency Monitoring Dashboard
```

---

## Latency Budget

The system decomposes end-to-end latency across each component:

* ASR Latency
* LLM Inference Latency
* TTS Synthesis Latency
* Network Transport Overhead
* Playback Initialization

Example:

```text
ASR: 280ms
LLM: 650ms
TTS: 320ms
Network: 60ms
Total: 1.31s
```

---

## Resilience Engineering

Realis implements graceful degradation strategies:

### ASR Failure

Prompt user to repeat input.

### LLM Timeout

Fallback to lightweight reasoning model.

### TTS Failure

Return text response instantly.

### Network Congestion

Switch to reduced streaming mode.

---

## Example Workflow

**User:**

> What's on my schedule tomorrow?

**Pipeline:**

1. Capture live speech
2. Stream audio via WebSockets
3. Transcribe using ASR
4. Process query through LLM
5. Generate spoken response
6. Stream synthesized reply
7. Log latency metrics

---

## Repository Structure

```text
src/
 ├── asr/
 ├── orchestration/
 ├── llm/
 ├── tts/
 ├── monitoring/
 ├── websocket/
 └── frontend/

tests/
configs/
```

---

## Project Goals

Realis demonstrates advanced AI systems engineering through:

* Real-time multimodal orchestration
* Performance optimization
* Low-latency inference pipelines
* Fault tolerance and recovery
* Production reliability patterns

---

## Why This Project?

Realis showcases practical systems engineering for modern AI applications beyond simple chatbot development.

It highlights expertise in:

* Streaming AI systems
* Latency-sensitive architecture
* Multimodal inference pipelines
* Reliability engineering

---

## Author

**Charu Jagguka**

Building production-grade AI systems focused on performance, resilience, and intelligent real-time interaction.
