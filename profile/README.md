# TOLVYN

**The Financial Control Plane for AI Infrastructure.**

Meter, attribute, govern, and audit every AI API call across
OpenAI, Anthropic, and Google. One line of code. 5 minutes to start.

---

## What is TOLVYN?

TOLVYN is a drop-in reverse proxy and SDK layer that sits between your application and the AI providers you already use. When your code calls OpenAI or Anthropic through the TOLVYN SDK, the request passes through the TOLVYN server, which records every token, calculates the exact cost, attributes it to the team and service that made the call, and enforces the spending rules you set — all before the response reaches your application. The overhead is minimal; the visibility is complete.

TOLVYN is built for engineering teams that need to ship quickly without losing control of AI spend. It gives finance teams an auditable, tamper-evident ledger of every dollar spent on AI. It gives platform teams the ability to kill a runaway service in one command, set per-team hard-stop budgets, receive anomaly alerts, and watch every request stream live in a terminal — without changing anything in the underlying AI calls.

---

## Architecture

```
Your Application
      │
      │  Bearer: tlv_live_<key>
      ▼
┌─────────────────────────────────┐
│         TOLVYN Server           │   Go HTTP server
│                                 │   ─────────────
│  ① Authenticate API key         │   • AES-256-GCM encrypted provider keys
│  ② Enforce budget (hard/soft)   │   • Hash-chained ledger (SHA-256 + HMAC)
│  ③ Forward to AI Provider       │   • Per-tenant Row Level Security (RLS)
│  ④ Meter tokens + cost (async)  │   • Budget auto-reset by period
│  ⑤ Write ledger record          │   • SSE real-time tail stream
│  ⑥ Fire threshold alerts        │
└──────┬──────────────────────────┘
       │                                      ┌──────────────┐
       │   Original request + provider key    │  PostgreSQL  │
       ▼                                      │  tolvyn_v1   │
┌─────────────────┐                           │              │
│   AI Provider   │                           │ 10 tables    │
│  OpenAI         │                           │ RLS enabled  │
│  Anthropic      │                           └──────────────┘
│  Google         │
└─────────────────┘
       │
       │   Provider response (unchanged)
       ▼
Your Application
```

**Components:**

| Component              | Language    | Description                                           |
|------------------------|-------------|-------------------------------------------------------|
| `tolvyn-server`        | Go          | HTTP proxy + REST API backend                         |
| `tolvyn` (CLI)         | Go + Cobra  | Terminal interface for all operations                 |
| `tolvyn` (Python SDK)  | Python 3.9+ | Drop-in for `openai` and `anthropic` packages         |
| `tolvyn` (Node.js SDK) | TypeScript  | Drop-in for `openai` and `@anthropic-ai/sdk` packages |
| `tolvyn-go` (Go SDK)   | Go 1.21+    | Drop-in for the OpenAI, Anthropic and Google Go SDKs  |
| PostgreSQL             | SQL         | Primary datastore with row-level security             |

---

## Repositories

| Repo                                                       | Description                                                           |
|------------------------------------------------------------|-----------------------------------------------------------------------|
| tolvyn-server *(private)*                                  | Go HTTP proxy and REST API backend                                    |
| [tolvyn-python](https://github.com/tolvyn/tolvyn-python)   | Python SDK — drop-in for `openai` and `anthropic`                     |
| [tolvyn-nodejs](https://github.com/tolvyn/tolvyn-nodejs)   | Node.js/TypeScript SDK — drop-in for `openai` and `@anthropic-ai/sdk` |
| [tolvyn-go](https://github.com/tolvyn/tolvyn-go)           | Go SDK — drop-in for the OpenAI, Anthropic and Google Go SDKs          |
| [tolvyn-cli](https://github.com/tolvyn/tolvyn-cli)         | CLI: `tolvyn tail`, budgets, cost, keys, teams                        |
| [docs](https://github.com/tolvyn/docs)                     | GitBook documentation source                                          |
| [tolvyn-website](https://github.com/tolvyn/tolvyn-website) | Marketing website                                                     |

---

## Quick Start

**1. Sign up and get your TOLVYN API key**

```bash
tolvyn init
# or: curl -s https://api.tolvyn.io/v1/auth/signup ...
```

**2. Install the SDK**

```bash
pip install tolvyn        # Python
npm install tolvyn        # Node.js
```

**3. Add your provider key via CLI**

```bash
tolvyn providers add openai
# Enter your OpenAI API key when prompted
```

**4. Change one line of code**

```python
# Before
from openai import OpenAI
client = OpenAI()

# After
from tolvyn import OpenAI
client = OpenAI(
    tolvyn_api_key="tlv_live_...",
    team="backend",
    service="summariser",
)
```

**5. Watch it live**

```bash
tolvyn tail
# TIME     | TEAM/SERVICE          | MODEL            |   TOKENS |     COST | LATENCY
# ─────────┼───────────────────────┼──────────────────┼──────────┼──────────┼─────────
# 14:22:01 | backend/summariser    | gpt-4o           |    1,847 |  $0.0185 |    923ms
```

**Next: set a budget**

```bash
tolvyn budgets create --scope team --team backend --amount 500 --period monthly --mode soft
```

---

## Links

- Website: [tolvyn.io](https://tolvyn.io)
- Docs: [docs.tolvyn.io](https://docs.tolvyn.io)
- PyPI: [pypi.org/project/tolvyn](https://pypi.org/project/tolvyn)
- npm: [npmjs.com/package/tolvyn](https://npmjs.com/package/tolvyn)
- X: [@tolvyn](https://x.com/tolvyn)
