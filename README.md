# Voynich Manuscript Claims Database

A structured knowledge base of research claims extracted from the [voynich.ninja](https://www.voynich.ninja) forum — 3,500+ threads and 69,000+ posts distilled into ~20,000 searchable, categorized claims.

## What's Here

| File | Description |
|------|-------------|
| `voynich_archive.db` | SQLite database with full data (download separately) |
| `data/claims.json` | All claims with linked theories and sources |
| `data/theories.json` | Theory taxonomy with categories |
| `data/folios.json` | Folio → claim count mapping |
| `data/threads.json` | Thread summaries |
| `scripts/` | Python pipeline used to build this |
| `web/` | Flask-based explorer application |

## Quick Start

### Browse the data

The simplest way — open the SQLite database with [DB Browser for SQLite](https://sqlitebrowser.org/) or any SQLite client.

### Run the web explorer

```bash
cd web
pip install flask
python app.py
# Open http://localhost:5000
```

### Use the JSON exports

```python
import json

with open('data/claims.json') as f:
    claims = json.load(f)

# Find claims about a specific folio
f116v_claims = [c for c in claims if 'f116v' in (c['folios'] or '')]

# Find established claims with linguistic evidence
established = [c for c in claims 
               if c['confidence'] == 'established' 
               and c['evidence_type'] == 'linguistic']
```

## Data Overview

### Claims (~20,000)

Each claim is a single assertion extracted from forum discussions:

```json
{
  "id": 12345,
  "assertion": "The word 'daiin' appears 887 times in the manuscript",
  "evidence_type": "statistical",
  "confidence": "established",
  "folios": "general",
  "sources": [{"thread_id": "1234", "post_id": "5678"}],
  "theories": [{"id": 42, "name": "Zipf's Law Analysis", "stance": "supports"}]
}
```

**Confidence levels:**
- `established` — widely accepted in the research community
- `disputed` — actively debated
- `speculative` — individual hypotheses

**Evidence types:**
- `statistical`, `visual`, `historical`, `linguistic`, `paleographic`, `codicological`, `speculative`

### Theories (~3,000)

Categorized hypotheses about the manuscript:

- **Language Identity** — what language underlies the text
- **Encoding Method** — cipher, shorthand, or encoding systems
- **Authorship** — who created the manuscript
- **Purpose** — intended function
- **Dating & Provenance** — when/where created
- **Content Interpretation** — what specific sections depict
- **Hoax & Meaninglessness** — theories that the text carries no meaning

### Folios (~350)

Standard folio references (e.g., `f1r`, `f116v`) normalized from various notations found in discussions.

## Updating the Data

The `scripts/` folder contains the full pipeline:

```bash
# 1. Scrape new forum content (requires login credentials)
python voynich_db.py update
python voynich_db.py scrape

# 2. Summarize new threads (requires Anthropic API key)
python voynich_db.py compile --cost-limit 5

# 3. Extract claims from threads
python extract_claims.py --cost-limit 5

# 4. Normalize and link
python normalize_folios.py --apply
python link_claims_to_posts.py --apply

# 5. Export
python export_claims.py --strict
python export_json.py
```

See `scripts/reentry.txt` for detailed workflow documentation.

## Schema

See [SCHEMA.md](SCHEMA.md) for complete database documentation.

## Limitations

- Claims were extracted via LLM summarization — some nuance may be lost
- ~5% of claims couldn't be linked to specific source posts
- "general" folio tag (~13k claims) means the claim isn't about specific pages
- Forum discussions through January 2026; not automatically updated

## License

The extracted claims and structure are provided freely for research purposes. Original forum content remains the property of its authors. The Voynich Manuscript itself is in the public domain.

## Acknowledgments

Built from discussions at [voynich.ninja](https://www.voynich.ninja). Thanks to the community of researchers who have contributed decades of analysis.