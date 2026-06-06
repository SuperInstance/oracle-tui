# Oracle TUI — Agentic Terminal Dashboard for the SuperInstance Fleet

> A real-time, terminal-based dashboard that shows fleet health, crate test status, Nebula reflex engine state, and system info — all driven by the SuperInstance fleet's edge network.

---

## Overview

Oracle TUI is an interactive terminal UI for the SuperInstance Construct fleet. It connects directly to **Nebula** — the fleet's Cloudflare Workers edge reflex engine — and provides a live overview of every subsystem:

- **Fleet Health** — disk, memory, CPU load, ARM64 host stats
- **Nebula Status** — reflex count, agent registrations, request volume, fast/slow path split
- **Crate Status** — test results from the ternary math layer (Eisenstein lattice, P48 vectors, etc.)
- **Reflex Viewer** — last 8 taught intent-reflex pairs with invocation counts
- **Interactive Query** — send natural-language intents to Nebula's reflex engine

---

## Quick Start

```bash
# Clone and run
git clone https://github.com/SuperInstance/oracle-tui.git
cd oracle-tui
node oracle-tui.js
```

**Requirements:** Node.js 18+, terminal with ≥80 columns, network access to the Nebula worker.

---

## Commands

Commands are entered at the `→` prompt after the dashboard renders:

| Command | Action |
|---------|--------|
| `health` | Query Nebula for full fleet health report |
| `crates` | Show crate test status from the math layer |
| `reflexes` | List the 5 most recent taught reflexes |
| `evolve` | Run a CraftMind Ranch evolution cycle (demo) |
| `disk` | Show disk usage bar chart |
| `exit` / `quit` | Exit the TUI |
| *(any other text)* | Queries Nebula as a natural-language intent |

Press **Ctrl+C** to exit at any time. The dashboard auto-refreshes on each query.

---

## Dashboard Sections

### System Info
- **Disk:** Bar chart showing usage percentage with total/used/free
- **Memory:** RAM usage (used / total, free)
- **Load:** 1m / 5m / 15m load averages
- **Arch:** ARM64 · 4 cores · 24GB RAM

### Nebula (Edge Reflex Engine)
- **Status:** HEALTHY / DOWN with live indicator dot
- **Reflexes:** Total stored reflexes and registered agents
- **Traffic:** Total requests, average response time (ms)
- **Fast/Slow Path:** Fast-path call count + percentage vs slow-path LLM fallbacks
- **LLM:** DeepInfra (DeepSeek V4 Flash) with BGE embeddings

### Crates (Math Layer)
- Shows per-crate test results from the ternary construct:
  - `eisenstein-quantize` — A₂ hexagonal lattice quantization (10/10 ✅)
  - `pythagorean48` — Zero-drift vector directions (7/7 ✅)
  - `deadband-snr` — Sparse signal filtering (10/10 ✅)
  - `ternary-spatial` — P48 + Eisenstein combined spatial queries (15/15 ✅)

### Recent Reflexes
Shows the last 8 taught reflexes with their intent text and invocation count. Filled dots indicate invoked reflexes, hollow dots indicate taught-but-unused.

---

## Architecture

```
┌─────────────────────────────────────────────┐
│           Oracle TUI (this repo)             │
│  ┌─────────┐  ┌──────────┐  ┌─────────────┐ │
│  │ System  │  │  Nebula  │  │    Query     │ │
│  │  Info   │  │  Status  │  │  Interface   │ │
│  └────┬────┘  └────┬─────┘  └──────┬───────┘ │
└───────┼─────────────┼───────────────┼──────────┘
        │             │               │
   local exec    HTTP/JSON        HTTP/JSON
        │             │               │
        ▼             ▼               ▼
   /proc/stat    fleet-murmur-    fleet-murmur-
   /proc/loadavg worker.workers.  worker.workers.
                 dev/api/health   dev/api/agent/
                                  message
```

The Nebula worker (`fleet-murmur-worker.casey-digennaro.workers.dev`) serves as the single reflex engine endpoint:
- `GET /api/health` — Fleet health check
- `GET /api/status` — Full status with reflex count, traffic, fast-path stats
- `GET /api/agent/reflexes` — All stored reflexes
- `POST /api/agent/message` — Send an intent, receive a reflex response

---

## Related Repos

| Repo | Purpose |
|------|---------|
| [fleet-status](https://github.com/SuperInstance/fleet-status) | Web-based fleet dashboard (gh-pages) |
| [hex-lattice-explorer](https://github.com/SuperInstance/hex-lattice-explorer) | Interactive A₂ Eisenstein lattice explorer |
| [construct-coordination](https://github.com/SuperInstance/construct-coordination) | Fleet coordination surface |
| [fleet-orchestrator](https://github.com/SuperInstance/fleet-orchestrator) | Stateless edge coordination hub |

---

## License

MIT — see [LICENSE](LICENSE) (parent fleet-status repo for the fleet-level license).
