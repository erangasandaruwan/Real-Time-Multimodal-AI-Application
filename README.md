# Project 5 – Real-Time Multimodal AI Application

## 1. Application Overview

A **real-time multimodal AI application** designed to demonstrate the ability to build systems that operate under:

- Continuous streaming input
- Tight latency requirements
- Real-time event processing
- External service dependencies
- Partial failures and timeouts
- Unpredictable or noisy input
- Production-grade resilience requirements

Unlike traditional batch-processing systems, a real-time AI system must react immediately while maintaining reliability, observability, and a good user experience.

The main objective of this project is not simply to connect an AI model to an application. The objective is to demonstrate the engineering required to make an AI system work reliably when data is continuously arriving and users expect immediate responses.

---

# 2. Suggested Project Track

Three possible implementations can be used for this project.

## Option 1 – Real-Time Voice Assistant

A voice assistant receives live audio from the user, converts the audio to text, sends the text to a language model, and streams the generated response back as speech.

### High-Level Flow

<img width="1086" height="1448" alt="image" src="https://github.com/user-attachments/assets/0b3d22f8-bf16-4cd1-9b73-2e3cbe071c25" />


This is the recommended option because it combines:

- Streaming audio
- Speech recognition
- LLM reasoning
- Speech synthesis
- Real-time networking
- Latency optimization
- Failure handling
- End-to-end observability

It provides a strong demonstration of real-time AI engineering.

---

## Option 2 – Real-Time Computer Vision Pipeline

A live webcam feed is continuously processed by an object-detection model. The detected objects or events are sent to a language model for contextual reasoning or explanation.

### Example Architecture

<img width="1086" height="1448" alt="image" src="https://github.com/user-attachments/assets/2c1d73ae-124f-4970-8eb9-74436fa75839" />


Example use cases include:

- Workplace safety monitoring
- Retail analytics
- Smart security systems
- Traffic monitoring
- Manufacturing inspection
- Visual AI assistants

---

## Option 3 – Real-Time Streaming Log Analyzer

A real-time log analyzer continuously consumes application or infrastructure logs, identifies unusual behavior, and uses an LLM to generate human-readable explanations.

### Example Architecture

<img width="1086" height="1448" alt="image" src="https://github.com/user-attachments/assets/f0e6c37a-e2c6-40bb-8a92-54825fd5ca0b" />


Example outputs might include:

- Root-cause summaries
- Error clustering
- Failure explanations
- Incident prioritization
- Recommended investigation steps

---

# 3. Recommended Implementation – Voice Assistant

The voice-assistant track provides the strongest demonstration of a real-time multimodal AI system.

A typical production architecture could be:

<img width="1182" height="1330" alt="image" src="https://github.com/user-attachments/assets/6fe24902-11ce-433a-baa5-0dce53dc2dd3" />


---

# 4. Suggested Technology Stack

## Frontend

Possible technologies:

- React
- Angular
- JavaScript / TypeScript
- Web Audio API
- WebSocket API
- MediaRecorder API

The frontend captures microphone audio and streams audio chunks to the backend.

---

## Backend

Recommended options:

- ASP.NET Core
- FastAPI
- Node.js

The backend is responsible for:

- WebSocket management
- Streaming event orchestration
- ASR integration
- LLM integration
- TTS integration
- Session management
- Latency tracking
- Retry handling
- Timeout handling
- Telemetry

---

## Speech Recognition

Possible Automatic Speech Recognition providers include:

- Deepgram
- OpenAI Whisper
- Azure AI Speech
- Google Speech-to-Text

The speech-recognition component should ideally support streaming transcription.

---

## Language Model

Possible reasoning models include:

- OpenAI models
- Azure OpenAI
- Claude
- Gemini
- Local LLM through Ollama

The LLM should support token streaming so that downstream processing can start before the entire response has been generated.

---

## Text-to-Speech

Possible text-to-speech providers include:

- ElevenLabs
- Cartesia
- Azure AI Speech
- OpenAI text-to-speech

The TTS engine should support low-latency streaming audio.

---

## Real-Time Communication

Recommended technologies:

- WebSockets
- Server-Sent Events where appropriate
- gRPC streaming for service-to-service communication

WebSockets are particularly suitable because the application requires bidirectional communication.

---

# 5. Event-Driven Architecture

A real-time system should use clearly structured events rather than passing unstructured messages between components.

Example events:

```json
{
  "eventType": "audio.chunk",
  "sessionId": "session-123",
  "timestamp": "2026-09-26T10:00:00Z",
  "sequence": 101,
  "payload": {
    "audioFormat": "pcm16",
    "sampleRate": 16000
  }
}
```

Other event types might include:

```text
audio.started
audio.chunk
audio.completed

asr.partial
asr.final
asr.failed

llm.started
llm.first_token
llm.token
llm.completed
llm.failed

tts.started
tts.first_byte
tts.audio_chunk
tts.completed
tts.failed

request.completed
request.failed
```

Having consistent event contracts makes the system easier to:

- Debug
- Replay
- Test
- Trace
- Monitor
- Extend

---

# 6. Phase 1 – Build the Streaming Pipeline

The first phase focuses only on getting the end-to-end streaming pipeline working.

Optimization is not the initial priority.

The target pipeline is:




## Phase 1 Objectives

The application should be able to:

1. Capture microphone audio.
2. Break the audio into streaming chunks.
3. Send the chunks to the backend.
4. Stream audio to the speech-recognition service.
5. Receive partial and final transcripts.
6. Send the final transcript to the LLM.
7. Stream LLM tokens.
8. Send generated text to the TTS service.
9. Receive synthesized audio.
10. Stream audio back to the user.

---

## Phase 1 Success Criteria

The first phase is complete when:

- Audio travels successfully through the entire pipeline.
- ASR produces a valid transcription.
- The LLM receives the transcription.
- The LLM response is streamed.
- TTS produces speech.
- The user hears the generated response.
- Events are logged with a unique session/request ID.

Even without optimization, achieving an end-to-end streaming AI pipeline is already a significant engineering milestone.

---

# 7. Phase 2 – Latency Engineering

The second phase focuses on measuring and optimizing latency.

For real-time systems, total response time alone is not enough.

The system should decompose latency into individual components.

---

# 8. Latency Budget

A voice assistant can track the following measurements.

| Metric | Description |
|---|---|
| Audio Capture Latency | Time required to capture and buffer user audio |
| Network Upload Latency | Time required to send audio to the backend |
| ASR Latency | Time required for speech recognition |
| ASR Finalization Latency | Time between end of speech and final transcript |
| LLM Request Overhead | Time required to send the request to the LLM |
| LLM Time to First Token | Time from LLM request until the first generated token |
| LLM Generation Time | Total response-generation time |
| TTS Time to First Byte | Time from TTS request until the first audio byte |
| TTS Generation Time | Total speech-generation time |
| Network Return Latency | Time required to return audio to the client |
| Client Playback Delay | Time before audio playback begins |
| Total End-to-End Latency | User stops speaking → assistant begins speaking |

---

# 9. Example Latency Breakdown

Example:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/91722c81-e304-4a59-9bf4-d49397a89b80" />


Total:

```text
220 ms
+ 30 ms
+ 390 ms
+ 310 ms
+ 180 ms
--------
1,130 ms
```

**Total perceived response latency: 1.13 seconds**

---

# 10. Latency Visualization

Build a dashboard that shows the latency breakdown for every request.

Example:

```text
Request ID: req-2026-00045

ASR                ███████         220 ms
Backend            █                30 ms
LLM TTFT           ████████████    390 ms
TTS TTFB           █████████       310 ms
Network            █████           180 ms

Total                                 1.13 s
```

Useful dashboard metrics include:

- P50 latency
- P90 latency
- P95 latency
- P99 latency
- ASR latency
- LLM TTFT
- TTS TTFB
- Total response latency
- Timeout rate
- Failure rate
- Reconnection rate

---

# 11. Key Performance Metrics

The system should track metrics such as:

```text
request_count
request_success_count
request_failure_count

asr_latency_ms
llm_ttft_ms
llm_total_latency_ms
tts_ttfb_ms
tts_total_latency_ms

end_to_end_latency_ms

websocket_connections
websocket_disconnects
websocket_reconnects

asr_timeout_count
llm_timeout_count
tts_timeout_count
```

Recommended aggregated measurements:

```text
P50
P90
P95
P99
Average
Minimum
Maximum
```

---

# 12. Observability Architecture

A production-style implementation should use distributed tracing.

Example:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/8f6dfc06-a6e5-4c2a-a1c8-28ac4212b34b" />


Tracing should capture each stage.

Example trace:




Recommended observability tools include:

- OpenTelemetry
- Jaeger
- Datadog
- Azure Monitor
- Application Insights
- Prometheus
- Grafana

---

# 13. Phase 3 – Resilience Engineering

The third phase focuses on failure scenarios.

A production real-time system cannot assume that every external dependency will always respond successfully.

The application should define behavior for failures such as:

- ASR service unavailable
- ASR timeout
- LLM timeout
- TTS timeout
- WebSocket disconnect
- Network interruption
- Invalid audio
- Partial transcription
- Malformed LLM response
- TTS generation failure
- Rate limiting
- Provider outage

---

# 14. Graceful Degradation

Instead of allowing the system to hang indefinitely, it should respond predictably.

Example strategies include:

### ASR Failure

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/d78f75d5-4420-4fe8-a48c-6b7dd8225701" />


Possible response:

```text
"I couldn't process the audio. Please try again."
```

---

### LLM Timeout

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/4dfa922a-6c06-4b37-96f7-0a2a4a8c0506" />


Possible fallback:

```text
"I'm having trouble generating a full response right now."
```

---

### TTS Failure

If text generation succeeded but speech synthesis failed:

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/4056cc58-208b-4002-9428-54446218caf5" />


The user still receives useful information instead of a complete failure.

---

# 15. Timeout Handling

Every external dependency should have a defined timeout.

Example configuration:

```yaml
timeouts:
  asr: 5s
  llm_first_token: 3s
  llm_total: 15s
  tts_first_byte: 3s
  tts_total: 10s
```

No network or AI dependency should be allowed to block indefinitely.

---

# 16. Retry Strategy

Retries should use controlled backoff rather than immediate repeated calls.

Example:

```text
Attempt 1
   │
   ▼
Failure
   │
   ▼
Wait 200 ms
   │
   ▼
Attempt 2
   │
   ▼
Failure
   │
   ▼
Wait 500 ms
   │
   ▼
Attempt 3
```

Retries should generally be limited to avoid increasing latency unnecessarily.

---

# 17. Circuit Breaker

Repeated downstream failures can be handled using a circuit breaker.

Example:

```text
Requests
   │
   ▼
ASR Provider
   │
   ├── Healthy → Continue
   │
   └── Repeated Failures
             │
             ▼
       Open Circuit
             │
             ▼
       Use Fallback
```

This prevents repeatedly calling a dependency that is already known to be unavailable.

---

# 18. Fallback Providers

A stronger implementation can support provider failover.

Example:

```text
Speech Recognition
       │
       ├── Primary: Deepgram
       │
       └── Fallback: Whisper
```

```text
Language Model
       │
       ├── Primary: Azure OpenAI
       │
       └── Fallback: Local Ollama Model
```

```text
Text-to-Speech
       │
       ├── Primary: ElevenLabs
       │
       └── Fallback: Azure Speech
```

This is optional but demonstrates advanced production engineering.

---

# 19. Replay Mode

A replay system is extremely useful for debugging streaming applications.

Instead of requiring live microphone input every time, the application should be able to replay previously captured sessions.

Example:

```text
Recorded Session
      │
      ▼
Replay Engine
      │
      ▼
Original Audio Chunks
      │
      ▼
ASR
      │
      ▼
LLM
      │
      ▼
TTS
      │
      ▼
Compare Results
```

Replay mode helps developers reproduce:

- Recognition failures
- Latency spikes
- LLM errors
- Timeout scenarios
- Provider issues
- Race conditions

---

# 20. Replay Event Format

Example replay record:

```json
{
  "sessionId": "session-001",
  "sequence": 18,
  "timestampOffsetMs": 940,
  "eventType": "audio.chunk",
  "payload": {
    "audioFile": "chunks/chunk-0018.wav"
  }
}
```

The replay engine can reproduce the original timing between events.

---

# 21. Session Recording

For development and testing, a session record can contain:

```text
session/
│
├── metadata.json
├── events.jsonl
├── input.wav
├── transcript.txt
├── llm-response.txt
├── output.wav
└── metrics.json
```

Example metadata:

```json
{
  "sessionId": "session-001",
  "startedAt": "2026-09-26T10:00:00Z",
  "asrProvider": "Deepgram",
  "llmProvider": "Azure OpenAI",
  "ttsProvider": "ElevenLabs"
}
```

---

# 22. Testing Strategy

Testing should cover much more than the happy path.

## Unit Testing

Test:

- Event serialization
- Timeout policies
- Retry policies
- Provider adapters
- Latency calculations
- Session state
- Audio metadata parsing

---

## Integration Testing

Validate:

```text
Audio
 ↓
ASR
 ↓
LLM
 ↓
TTS
```

Each dependency should also have mock implementations for automated testing.

---

## Failure Injection Testing

Simulate scenarios such as:

```text
ASR latency = 10 seconds
LLM returns HTTP 500
TTS service unavailable
WebSocket disconnects
Audio stream stops unexpectedly
Malformed event arrives
```

Then verify that the system degrades gracefully.

---

# 23. Load Testing

The application should also be tested under concurrent sessions.

Example load profiles:

```text
10 concurrent users
50 concurrent users
100 concurrent users
500 concurrent users
```

Measure:

- End-to-end latency
- CPU usage
- Memory usage
- WebSocket connections
- External API utilization
- Error rate
- Queue depth
- Connection failures

Tools may include:

- k6
- JMeter
- Locust

---

# 24. Suggested Production Architecture

```text
                         ┌──────────────────┐
                         │      User        │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │ React / Angular  │
                         │ Web Audio API    │
                         └────────┬─────────┘
                                  │
                              WebSocket
                                  │
                                  ▼
                      ┌──────────────────────┐
                      │ Real-Time AI Gateway │
                      │ ASP.NET Core/FastAPI │
                      └──────────┬───────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
              ▼                  ▼                  ▼
         ASR Service         LLM Service        TTS Service
         Deepgram /          Azure OpenAI /     ElevenLabs /
         Whisper             OpenAI             Cartesia
              │                  │                  │
              └──────────────────┼──────────────────┘
                                 │
                                 ▼
                       ┌─────────────────────┐
                       │ OpenTelemetry       │
                       │ Metrics / Tracing   │
                       └──────────┬──────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
           Jaeger              Grafana             Datadog
```

---

# 25. Kubernetes Deployment

A more advanced deployment could use Kubernetes.

```text
Kubernetes / AKS
 │
 ├── Frontend
 │
 ├── Real-Time AI Gateway
 │
 ├── Session Service
 │
 ├── Replay Service
 │
 ├── Metrics Service
 │
 ├── OpenTelemetry Collector
 │
 └── Redis
```

Redis can be used for:

- Session state
- Short-lived conversation context
- Distributed coordination
- Connection metadata
- Rate limiting

---

# 26. CI/CD Pipeline

A production-style CI/CD pipeline could look like:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/77c11a74-31f4-4a36-a437-75b750b2f69b" />

---

# 27. Latency Regression Testing

Because latency is critical, the CI pipeline can compare the current implementation against a baseline.

Example thresholds:

```text
ASR P95 latency         < 500 ms
LLM TTFT P95            < 700 ms
TTS TTFB P95            < 500 ms
End-to-end P95          < 2.0 sec
Failure rate            < 1%
```

If performance regresses beyond an agreed threshold, the pipeline can fail.

---

# 28. Recommended Dashboard

A monitoring dashboard could contain:

## System Health

```text
Active Sessions
Requests / Second
Failure Rate
WebSocket Connections
CPU
Memory
```

## Latency

```text
End-to-End P50
End-to-End P95
End-to-End P99

ASR P95
LLM TTFT P95
TTS TTFB P95
```

## AI Provider Metrics

```text
ASR success rate
LLM success rate
TTS success rate
Tokens per request
Cost per request
```

## Error Analysis

```text
ASR timeout
LLM timeout
TTS timeout
Provider errors
WebSocket disconnects
Retry count
Fallback count
```

---

# 29. Advanced Features

Once the core project is working, several advanced capabilities can be added.

## Voice Activity Detection

Automatically determine when the user starts and stops speaking.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/6ece3973-30ed-4074-91f6-ab2c5d2eef4f" />


This can significantly reduce perceived latency.

---

## Streaming LLM-to-TTS

Instead of waiting for the full LLM response:

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/34a852fe-9ea3-4e13-b076-73c42682cd5d" />


The first sentence can begin speech synthesis while the LLM is still generating later sentences.

This reduces perceived response time.

---

## User Interruption / Barge-In

A real voice assistant should allow the user to interrupt the assistant.

Example:

<img width="1208" height="1302" alt="image" src="https://github.com/user-attachments/assets/0f062b93-877a-4510-89b3-593a9ae7d64c" />


This is an advanced but highly valuable real-time interaction feature.

---

# 30. Project Deliverables

A strong portfolio project should include the following.

## Source Code

```text
/src
  /frontend
  /gateway
  /providers
  /observability
  /replay
  /tests
```

---

## Architecture Diagram

Document:

```text
Frontend
↓
WebSocket Gateway
↓
ASR
↓
LLM
↓
TTS
↓
Streaming Response
```

---

## Latency Dashboard

Demonstrate:

```text
ASR Latency
LLM TTFT
TTS TTFB
End-to-End Latency
```

---

## Failure Handling

Document scenarios such as:

```text
ASR outage
LLM timeout
TTS failure
WebSocket disconnect
```

and explain the recovery strategy.

---

## Replay Tool

Provide a command such as:

```bash
python replay.py session-001
```

or:

```bash
dotnet run -- replay session-001
```

that replays a previously recorded interaction.

---

## Technical Report

The final report should describe:

- Architecture
- Technology choices
- Streaming protocol
- Event contracts
- Latency measurements
- Performance bottlenecks
- Failure scenarios
- Retry policies
- Timeout policies
- Observability
- Replay strategy
- Load-test results
- Lessons learned

---

# 31. Recommended Repository Structure

```text
realtime-multimodal-ai/
│
├── frontend/
│   ├── src/
│   └── package.json
│
├── gateway/
│   ├── websocket/
│   ├── sessions/
│   ├── events/
│   └── telemetry/
│
├── providers/
│   ├── asr/
│   ├── llm/
│   └── tts/
│
├── replay/
│   ├── recorder/
│   ├── player/
│   └── sessions/
│
├── observability/
│   ├── otel/
│   ├── prometheus/
│   └── grafana/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── resilience/
│   └── performance/
│
├── deploy/
│   ├── docker/
│   └── kubernetes/
│
├── docs/
│   ├── architecture.md
│   ├── latency.md
│   ├── resilience.md
│   └── replay.md
│
├── docker-compose.yml
└── README.md
```

---

# 32. What This Project Demonstrates

This project demonstrates far more than the ability to call AI APIs.

It demonstrates knowledge of:

- Real-time system design
- Streaming architectures
- Multimodal AI
- Automatic speech recognition
- Large language models
- Text-to-speech synthesis
- WebSockets
- Event-driven design
- Distributed tracing
- Performance engineering
- Latency budgeting
- Failure handling
- Retry policies
- Timeout management
- Circuit breakers
- Graceful degradation
- Replay debugging
- Load testing
- Observability
- Production deployment

---

# 33. Interview Value

A strong explanation during an interview could be:

> I built a real-time voice AI assistant using a streaming architecture. Instead of measuring only total response time, I decomposed latency into ASR latency, LLM time-to-first-token, TTS time-to-first-byte, network overhead, and client playback delay. I instrumented every stage with distributed tracing and built dashboards showing P50, P95, and P99 latency.
>
> I also designed the application for failure scenarios. Each external AI provider has timeout and retry policies, and the system degrades gracefully if ASR, the LLM, or TTS becomes unavailable. I implemented session replay so recorded interactions can be reproduced during debugging and latency-regression testing.

This explanation shows a production engineering mindset rather than only AI-model integration.

---

# 34. Final Project Goal

The goal of this project is not simply:

```text
Audio → AI → Speech
```

The real goal is to demonstrate the ability to build:

<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/46b8d6b3-cb23-4599-9373-b64228db80a9" />


The project therefore combines **AI engineering, backend engineering, distributed systems, performance engineering, observability, resilience, and production operations** into one portfolio project.

---

# 35. Suggested Project Title

**Real-Time Multimodal Voice AI Assistant with Streaming, Latency Engineering, Observability, and Resilience**

Alternative repository name:

```text
realtime-multimodal-ai-assistant
```

Short description:

> A production-oriented real-time voice AI assistant implementing streaming ASR, LLM reasoning, streaming TTS, WebSocket orchestration, latency decomposition, distributed tracing, graceful degradation, replay debugging, and performance monitoring.
