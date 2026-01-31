# Serper

Google search via Serper API with full page content extraction using trafilatura. Not just snippets — fetches and reads the actual web pages to extract clean full-text content.

[![GitHub](https://img.shields.io/badge/GitHub-openclaw--serper-blue)](https://github.com/nesdeq/openclaw-serper)
[![Version](https://img.shields.io/badge/version-3.0.0-green)](https://github.com/nesdeq/openclaw-serper)
[![License](https://img.shields.io/badge/license-MIT-blue)](https://github.com/nesdeq/openclaw-serper/blob/main/LICENSE)

---

## Features

- **Full Page Content** — Extracts clean readable text from every result using trafilatura
- **Two Search Modes** — All-time general search or time-sensitive news/recent results
- **Knowledge Graph** — Includes Google Knowledge Graph data when available
- **Locale Control** — Country and language targeting via `--gl` and `--hl` flags
- **Streaming Output** — Results stream as JSON lines, one per page, as each is scraped
- **Zero Heavy Dependencies** — Uses Python stdlib for HTTP; only requires trafilatura

---

## Quick Start

```bash
# 1. Clone into your OpenClaw skills directory
git clone https://github.com/nesdeq/openclaw-serper.git ~/.openclaw/skills/serper

# 2. Install trafilatura
pip install trafilatura

# 3. Add your API key (get one free at https://serper.dev — 2,500 queries)
echo 'SERPER_API_KEY="your-key"' >> ~/.openclaw/skills/serper/.env

# 4. Done. Search.
python3 ~/.openclaw/skills/serper/scripts/search.py -q "how does HTTPS work"
```

---

## Search Modes

### `default` — General search (all-time)

All-time Google web search, **5 results**, each enriched with full page content.

Use for: general questions, research, how-to, evergreen topics, product info, technical docs, comparisons, tutorials.

```bash
python3 scripts/search.py -q "how does HTTPS work"
python3 scripts/search.py -q "best mechanical keyboards 2026"
```

### `current` — News and recent info

Past-week Google web search (3 results) + Google News (3 results), each enriched with full page content. Results are deduplicated by URL.

Use for: news, current events, recent developments, breaking news, announcements.

```bash
python3 scripts/search.py -q "OpenAI latest announcements" --mode current
python3 scripts/search.py -q "tech layoffs this week" --mode current
```

### Mode Selection Guide

| Query signals | Mode |
|---------------|------|
| "how does X work", "what is X", "explain X" | `default` |
| Product research, comparisons, tutorials | `default` |
| Technical documentation, guides | `default` |
| Historical topics, evergreen content | `default` |
| "news", "latest", "today", "this week", "recent" | `current` |
| "what happened", "breaking", "announced", "released" | `current` |
| Current events, politics, sports scores, stock prices | `current` |

---

## Locale

**Default is global** — no country filter, English results.

Set `--gl` (country) and `--hl` (language) when the query is non-English or targets a specific region.

| Scenario | Flags |
|----------|-------|
| English query, no country target | *(omit --gl and --hl)* |
| German query or targeting DE/AT/CH | `--gl de --hl de` |
| French query or targeting France | `--gl fr --hl fr` |
| Any other language/country | `--gl XX --hl XX` (ISO codes) |

```bash
# German news
python3 scripts/search.py -q "Nachrichten aus Berlin" --mode current --gl de --hl de

# French product research
python3 scripts/search.py -q "meilleur smartphone 2026" --gl fr --hl fr
```

---

## Output Format

Clean JSON only, one object per line. Two types:

**First line — search metadata:**

```json
{
  "query": "how does HTTPS work",
  "mode": "default",
  "locale": {"gl": "world", "hl": "en"},
  "results": [
    {"title": "...", "url": "...", "source": "web"}
  ]
}
```

**Following lines — one per page with extracted content:**

```json
{"title": "Page Title", "url": "https://example.com", "source": "web", "content": "Full extracted page text..."}
{"title": "News Article", "url": "https://news.com", "source": "news", "date": "2 hours ago", "content": "Full article text..."}
```

### Result Fields

| Field | Description |
|-------|-------------|
| `title` | Page title |
| `url` | Source URL |
| `source` | `"web"`, `"news"`, or `"knowledge_graph"` |
| `content` | Full extracted page text (falls back to snippet if extraction fails) |
| `date` | Only present for news results |

---

## CLI Reference

| Flag | Description |
|------|-------------|
| `-q, --query` | Search query (required) |
| `-m, --mode` | `default` (all-time, 5 results) or `current` (past week + news, 3 each) |
| `--gl` | Country code (e.g. `de`, `us`, `fr`, `at`, `ch`). Default: `world` |
| `--hl` | Language code (e.g. `en`, `de`, `fr`). Default: `en` |

---

## FAQ & Troubleshooting

**Q: Do I need a paid Serper account?**
> No. Serper offers 2,500 free queries at [serper.dev](https://serper.dev).

**Q: Why is content empty or just a snippet for some results?**
> Some sites block scraping. When trafilatura can't extract content, the skill falls back to the search snippet.

**Q: Does this work on Windows?**
> The content extraction timeout uses `SIGALRM`, which is Linux/macOS only. The script will work on Windows but without per-page timeout protection.

**Error: "trafilatura is required but not installed"**
```bash
pip install trafilatura
```

**Error: "Missing Serper API key"**
```bash
echo 'SERPER_API_KEY="your-key"' >> ~/.openclaw/skills/serper/.env
```

**Error: "Invalid or expired API key" (401)**
> Generate a new key at [serper.dev](https://serper.dev).

**Error: "Rate limit exceeded" (429)**
> Wait and retry, or upgrade your Serper plan.

---

## License

MIT

---

## Links

- [Serper](https://serper.dev) — Google Search API (2,500 free queries)
- [GitHub](https://github.com/nesdeq/openclaw-serper) — Source code & issues
