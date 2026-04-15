# Elixir Daemon Migration Specification

## 1. Overview
Extract the `wombo-combo` daemon into a standalone Elixir/OTP application to leverage BEAM's supervision trees, lightweight concurrency, and fault tolerance. The TypeScript/Bun ecosystem will remain as the primary presentation layer (CLI and Ink-based TUI).

## 2. Core Architecture
- **Daemon (BEAM)**: Responsible for wave supervision, task scheduling, agent process management, and state durability.
- **Client (Bun)**: Stateless CLI tool that communicates with the daemon via HTTP/WebSocket.
- **IPC**: Communication via Unix Domain Sockets (preferred) or localhost HTTP.

## 3. Daemon Responsibilities (Elixir)
- **Supervision**: Each "Wave" is a supervisor; each "Agent" is a worker process.
- **Inactivity Management**: Self-terminate after 60s of idle time.
- **State Preservation**: ETS for in-memory hot state; SQLite (Ecto) for persistence.
- **File Watching**: Native file-system events to trigger wave re-scheduling when `.yml` files change.

## 4. Client Responsibilities (TypeScript)
- command parsing (Citty).
- TUI Rendering (Ink).
- Streaming daemon events via WebSocket to the dashboard.

## 5. API Contract (Draft)
- `POST /waves` -> Initialize a new wave.
- `GET /status` -> Global daemon/wave status.
- `WS /stream`  -> Real-time event log for TUI.
- `DELETE /daemon` -> Controlled shutdown.

## 6. Migration Steps
1. Define the Protobuf/JSON schema for IPC.
2. Implement the Elixir skeleton (Supervisor tree).
3. Create the `Woco.Client` TS adapter.
4. Port task scheduling logic from TS to Elixir.
5. Pivot TUI from local state reading to WS streaming.
