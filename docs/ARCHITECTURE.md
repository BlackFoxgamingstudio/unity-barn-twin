# Architecture: Sovereign Unity Barn Twin

## Overview

**Package ID:** `PKG-029`  
**Domain:** Unity 3D Simulation & Digital Twins  
**Microservice Port:** `8809`  
**n8n Webhook Path:** `unity-barn-twin-trigger`  
**GitHub:** [BlackFoxgamingstudio/unity-barn-twin](https://github.com/BlackFoxgamingstudio/unity-barn-twin)

Unity-based digital twin engine for agricultural and commercial facility management. Streams real-time sensor data into Unity 3D scenes via WebSocket bridges.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign Unity Barn Twin     │
                     │       Port: 8809            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  UnityBridge     | SensorStreamAda | SceneStateMa  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `UnityBridge`
Handles all unitybridge operations. Exposes async methods callable from the core dispatcher.

### `SensorStreamAdapter`
Handles all sensorstreamadapter operations. Exposes async methods callable from the core dispatcher.

### `SceneStateManager`
Handles all scenestate operations. Exposes async methods callable from the core dispatcher.

### `TwinEventBus`
Handles all twineventbus operations. Exposes async methods callable from the core dispatcher.

### `DigitalTwinRecorder`
Handles all digitaltwinrecorder operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-unity-barn-twin", "port": 8809}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-unity-barn-twin:
  image: sovereign-unity-barn-twin:latest
  ports: ["8809:8809"]
  healthcheck:
    test: curl -f http://localhost:8809/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`unity`, `digital-twin`, `simulation`, `iot`
