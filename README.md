# LLM Gateway Benchmarks — 191 Models, 5 Providers, Measured

**Who:** Dev4AiBots (Devraj Gadhvi) — AI Engineer
**When:** June–July 2026 · **What:** production model routing for an agentic gateway (LiteLLM proxy)

> I benchmarked **191 models** across **5 providers** (OpenRouter, Groq, Cerebras, Gemini, NVIDIA NIM) to answer one practical question:
> *Which models are fast, stable, and cheap enough to serve agent traffic — and which should be removed from the routing table?*

Every number below comes from **real API calls with measured latency**, not vendor marketing. Results were used to build the routing config for a multi-tenant AI-agent SaaS.

---

## Key findings

| Finding | Detail |
|---|---|
| Fastest stable models | Groq **qwen3.6-27b** (0.28s), Groq **Llama-4-scout-17b-16e** (0.32s), Groq **Llama-3.3-70b-versatile** (0.40s), OpenRouter **Llama-4-scout** (0.56s), OpenRouter **granite-4.1-8b** (0.64s), OpenRouter **qwen3.5-35b-a3b** (0.78s) |
| Best quality-per-latency | OpenRouter **Claude Opus 4.6** (1.92s), OpenRouter **DeepSeek R1-distill-Llama-70b** (1.29s), OpenRouter **qwen3-max-thinking** (1.23s) |
| Dead at test time | Bulk of NVIDIA NIM catalog (0% success), several free-tier aliases, gpt-audio, and older Gemini 1.5 endpoints |
| Success-rate threshold | Models with <100% success over the probe window were marked for failover or removal |

## Fastest stable models (top 10 by latency)

| Provider | Model | Avg latency | Success |
|---|---|---|---|
| Groq | qwen3.6-27b | 0.28s | 100% |
| Groq | llama-4-scout-17b-16e-instruct | 0.32s | 100% |
| Groq | llama-prompt-guard-2-22m | 0.36s | 100% |
| Groq | llama-3.3-70b-versatile | 0.40s | 100% |
| Groq | qwen3-32b | 0.44s | 100% |
| Groq | llama-prompt-guard-2-86m | 0.48s | 100% |
| Groq | gpt-oss-safeguard-20b | 0.50s | 100% |
| OpenRouter | llama-4-scout | 0.56s | 100% |
| OpenRouter | ibm-granite/granite-4.1-8b | 0.64s | 100% |
| OpenRouter | qwen/qwen3.5-35b-a3b | 0.78s | 100% |

## Why this matters for agents

Agent systems generate **multiple sequential model calls per task** (plan → act → verify). A 0.3s vs 2.2s model choice is the difference between a snappy chat and a walkie-talkie. Routing the *right* model per job (fast-tier for classification/guards, reasoning-tier for final answers) cut latency and spend simultaneously.

---

## Methodology (short)

- **Probe:** each model received repeated same-prompt requests; per-attempt latency, status, token counts recorded
- **Window:** June–July 2026; endpoints may change after this window
- **Classification:** Stable (≥95% success) / Limited / Unstable / Dead
- **Tooling:** LiteLLM proxy + custom benchmark scripts
- **Outputs:** aggregated table in data/summary.md; raw per-attempt logs kept private

## Repository layout

    llm-benchmarks/
    ├── README.md            # this file
    ├── METHODOLOGY.md       # probe design, limits, reproduction
    ├── data/
    │   └── summary.md       # aggregated results: 191 models
    └── LICENSE

## Honest limits

- Latency is one sample context; production variance depends on load, region, input size
- Provider availability changes weekly — rerun before trusting the table long-term
- This is an engineering benchmark, not a model quality leaderboard

## License

MIT (benchmark scripts + this content). Provider/model names remain property of their owners.
