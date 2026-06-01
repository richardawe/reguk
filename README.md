# UK Regulatory Monitor

An auto-updating intelligence dashboard for UK regulatory news. Built on the [SmartKit](https://github.com/richardawe/smartkit) framework — tracks enforcement actions, guidance, and rulemaking from the FCA, PRA, ICO, CMA, FRC, and HM Treasury. Updated every 6 hours via GitHub Actions, displayed on GitHub Pages.

## Data sources

| Regulator | Coverage |
|-----------|----------|
| **FCA** — Financial Conduct Authority | News, final notices, enforcement actions |
| **Bank of England / PRA** | Prudential regulation, financial stability, policy |
| **ICO** — Information Commissioner's Office | Data protection enforcement, GDPR fines |
| **CMA** — Competition and Markets Authority | Mergers, market investigations, antitrust |
| **HM Treasury** | Financial services legislation and policy |
| **FRC** — Financial Reporting Council | Audit, accounting, corporate governance |

## Quickstart

1. **Enable Pages:** Settings → Pages → Source → **GitHub Actions**
2. **Run once:** Actions → Update UK Regulatory Monitor → Run workflow
3. **Visit:** `https://richardawe.github.io/reguk/dashboard/`

Optional: add `OPENROUTER_API_KEY` as a repository secret to enable LLM-assisted field extraction (free tier available at [openrouter.ai](https://openrouter.ai/models?q=:free)). Without it, the pipeline uses deterministic keyword extraction — fully functional at $0.

## Customise

All configuration lives in `config/`:

- **`sources.yml`** — add or remove feed URLs (RSS, JSON, HTML)
- **`rules.yml`** — adjust keyword weights and the scoring threshold
- **`settings.yml`** — change the dashboard title or swap in a different LLM model

## Architecture

```
config/sources.yml
       │
       ▼
   [fetch]       RSS · JSON · HTML  (one bad source never stops the run)
       │
       ▼
  [extract]      Tier 0 — regex/keywords        no key required, always runs
       │          Tier 1 — OpenRouter free model  set OPENROUTER_API_KEY secret
       │
       ▼
   [score]       deterministic keyword weights + source trust  (reads rules.yml)
       │
       ▼
  [render]       writes data/latest.json + data/latest.js
       │
       ├──────▶  git commit  (only when data changed)
       └──────▶  GitHub Pages → dashboard/index.html
```

The pipeline runs every 6 hours. The dashboard is plain HTML + vanilla JS — no npm, no framework.
