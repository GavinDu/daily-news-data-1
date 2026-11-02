# Daily News Data

Machine-readable market and technology news datasets collected from Reuters, TradingView News, and Unusual Whales when timestamp-verifiable public records are available.

## Repository layout

- `data/YYYY/MM/DD/market_news_contract_gpt_YYYYMMDDHHMMSS.json` — immutable timestamped collection snapshots, partitioned by UTC generation date.
- `schemas/news-event.schema.json` — JSON Schema for the dataset envelope and canonical event fields.
- `README.md` — storage contract and operating rules.

## Dataset contract

Each snapshot is a JSON object with:
- `metadata`: producer, UTC generation/window timestamps, completion status, target and actual counts, per-source status, validation results, and limitations.
- `events`: unique events with headline, source, canonical URL, publication timestamp, summary, entities/tickers, event type, sentiment, severity, reliability, fetch timestamp, and deduplication key.

Event types: `macro`, `policy`, `geopolitical`, `energy`, `earnings`, `credit`, `market_structure`, `tech`.

Deduplication key format: `source|YYYY-MM-DD|slug` (source normalized to lowercase; date is the UTC publication date).

## Collection and quality rules

1. Collect up to 100 unique, verified events per rolling 24-hour UTC window. This is a target, not a quota: never fabricate or pad records.
2. Retain only records with a verifiable publication timestamp inside the declared collection window.
3. Normalize timestamps to timezone-aware UTC ISO-8601 strings.
4. Keep canonical source URLs; deduplicate by key and URL.
5. Sentiment must be in [-1, 1]; severity and reliability must be in [0, 1].
6. Record source access gaps and validation limitations in metadata. Do not imply canonical Pydantic validation when only provisional structural validation was performed.
7. Snapshots are immutable. Create a new timestamped file for each run; do not overwrite prior snapshots.
8. Store compact JSON only. Do not commit raw HTML, credentials, API keys, or large raw captures.

## Current baseline snapshot

The initial snapshot is at [`data/2026/11/02/market_news_contract_gpt_20261102083516.json`](./data/2026/11/02/market_news_contract_gpt_20261102083516.json). It contains 14 timestamp-verified Reuters events for 2026-11-01 08:35:16 UTC through 2026-11-02 08:35:16 UTC. It is explicitly marked `partial_not_final`: the 100-event target was not met, TradingView yielded no additional separately timestamp-verified unique events, Unusual Whales did not expose in-window article rows, and canonical NewsEvent Pydantic validation remains unverified.

This initial snapshot is **not** the requested three-day historical backfill (2026-10-31 through 2026-11-02). No complete three-day result was available at initialization; it must be added as a separate snapshot only after collection and validation.

## Daily briefing

The companion briefing should select exactly five high-signal stories spanning AI/LLMs/agents, markets/macro, developer tools, and robotics/edge AI, with concise technical and investment implications. The briefing is a curated view, not a replacement for the underlying event archive.
