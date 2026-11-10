# Methodology

## Objective
Produce a decision-ready shortlist of LLM endpoints for an agentic gateway (LiteLLM proxy) serving multi-tenant AI-agent traffic.

## Probe design
- Each model: repeated identical prompt attempts across the window
- Metrics per attempt: latency (s), status (Success / error_type), tokens
- Aggregates: success rate %, average latency
- Classification: Stable >= 95% success; Limited 20–94%; Unstable ~60% (reported); Dead 0%

## Providers covered
OpenRouter · Groq · Cerebras · Gemini · NVIDIA NIM

## Tooling
- `embedding_benchmark.py` — embedding endpoints
- `exhaustive_benchmark.py` / `exhaustive_benchmark_p2.py` — chat completions sweep
- LiteLLM proxy as the routing layer under test

## Reproduction notes
1. Set provider API keys in environment
2. Run `python exhaustive_benchmark.py` with the target model list
3. Results emitted to results.csv / results.json

## Known limitations
- One region, one time window (June–July 2026)
- Latency sensitive to prompt length; fixes prompt used
- Rate-limit throttling can skew slow readings; attempts with client-side timeout failures were flagged, not silently dropped
- Provider endpoint lifecycle: models marked Dead may return later; rerun periodically
