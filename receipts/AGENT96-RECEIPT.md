# AGENT-96 RECEIPT: bd-local-rpc-ping

- **WRITTEN-AT**: 2026-10-17T23:41:19Z
- **SCRIPT**: `/home/mboyle/bin/bd-local-rpc-ping`
- **MODE**: `-rwxr-xr-x` (`chmod +x`)
- **SHA256**: `1f291283f95f4f30f3770555bdfa1a88e04c6caf3f96e4ae0b96f80cb1305e73`
- **LIVE-RC**: `0`

## Live Output

```text
# bd-local-rpc-ping (2026-10-17T23:41:18Z)
LABEL          ENDPOINT             STATUS                 MIN_MS     AVG_MS
----------------------------------------------------------------------------
llama-rpc-11   10.0.75.11:50052     DOWN(TimeoutError)  1006.007ms  1006.007ms
llama-rpc-12   10.0.75.12:50052     DOWN(TimeoutError)  1004.952ms  1004.952ms
llama-rpc-13   10.0.75.13:50052     DOWN(TimeoutError)  1004.659ms  1004.659ms
llama-rpc-14   10.0.75.14:50052     DOWN(TimeoutError)  1004.922ms  1004.922ms
port-8081      127.0.0.1:8081       OK                     0.201ms     1.366ms
port-8082      127.0.0.1:8082       OK                     0.312ms     1.248ms
port-4010      127.0.0.1:4010       OK                     0.350ms     0.523ms
port-4000      127.0.0.1:4000       OK                     0.167ms     0.391ms
```

## JSON Telemetry

```json
{
  "written_at": "2026-10-17T23:41:19Z",
  "endpoints": [
    {
      "label": "llama-rpc-11",
      "endpoint": "10.0.75.11:50052",
      "port": 50052,
      "status": "DOWN(TimeoutError)",
      "ok": false,
      "latency_ms": 1004.8,
      "avg_ms": 1004.8
    },
    {
      "label": "llama-rpc-12",
      "endpoint": "10.0.75.12:50052",
      "port": 50052,
      "status": "DOWN(TimeoutError)",
      "ok": false,
      "latency_ms": 1004.014,
      "avg_ms": 1004.014
    },
    {
      "label": "llama-rpc-13",
      "endpoint": "10.0.75.13:50052",
      "port": 50052,
      "status": "DOWN(TimeoutError)",
      "ok": false,
      "latency_ms": 1002.802,
      "avg_ms": 1002.802
    },
    {
      "label": "llama-rpc-14",
      "endpoint": "10.0.75.14:50052",
      "port": 50052,
      "status": "DOWN(TimeoutError)",
      "ok": false,
      "latency_ms": 1001.323,
      "avg_ms": 1001.323
    },
    {
      "label": "port-8081",
      "endpoint": "127.0.0.1:8081",
      "port": 8081,
      "status": "OK",
      "ok": true,
      "latency_ms": 0.504,
      "avg_ms": 0.62
    },
    {
      "label": "port-8082",
      "endpoint": "127.0.0.1:8082",
      "port": 8082,
      "status": "OK",
      "ok": true,
      "latency_ms": 0.278,
      "avg_ms": 0.524
    },
    {
      "label": "port-4010",
      "endpoint": "127.0.0.1:4010",
      "port": 4010,
      "status": "OK",
      "ok": true,
      "latency_ms": 0.348,
      "avg_ms": 0.56
    },
    {
      "label": "port-4000",
      "endpoint": "127.0.0.1:4000",
      "port": 4000,
      "status": "OK",
      "ok": true,
      "latency_ms": 0.335,
      "avg_ms": 0.425
    }
  ]
}
```
