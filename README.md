# 🌐 GeoSentinel 2.0

**Global Disease Surveillance via OSINT**

Real-time disease outbreak monitoring using open-source intelligence. Replacing expensive, slow clinic-based surveillance with automated multi-source signal detection.

<img src="https://img.shields.io/badge/signals-live-brightgreen" alt="Live"> <img src="https://img.shields.io/badge/sources-4-blue" alt="Sources"> <img src="https://img.shields.io/badge/updates-every%2030%20min-orange" alt="Updates">

## 🛰️ Data Sources

| Source | Type | Coverage |
|--------|------|----------|
| 🏥 **WHO** | Disease Outbreak News API | Official alerts, global |
| 📰 **GDELT** | Global news (CDC, Reuters, AFP, Al Jazeera...) | Breaking outbreaks |
| 🐘 **Mastodon** | Public hashtag timelines | Symptom reports, early signals |
| 💬 **Reddit** | Community reports (OAuth, app-only) | Travel health experiences |

## 🔬 Features

- **Real-time scanning** — automated every 30 minutes via GitHub Actions
- **Anomaly detection** — historical baseline comparison flags unusual spikes
- **Traveler signal detection** — NLP patterns identify "came back sick from..." reports
- **Flight risk modeling** — maps potential disease spread via air routes
- **Interactive map** — Leaflet-based with severity-coded markers
- **Multi-severity scoring** — 1-10 scale: critical, high, moderate, low
- **Source-filtered views** — filter by WHO, news, Mastodon, or Reddit

## 🏗️ Architecture

```
GitHub Actions (cron 30 min)
    → scanner_v2.py (Python stdlib only)
        → WHO + GDELT + Mastodon + Reddit OAuth
        → NLP disease detection + geocoding + anomaly scoring
        → signals.json (generated in-runner, not committed)
    → GitHub Pages (deployed from same job's artifact)
        → index.html (Leaflet map + dashboard)
```

**Zero infrastructure cost.** Runs entirely on GitHub Actions + Pages.

## 🚀 Deployment

Auto-deploys via GitHub Pages. Every 30 minutes:

1. Scanner collects signals from four sources
2. Processes, deduplicates, scores, and geocodes
3. Writes `signals.json` to the runner filesystem (not committed)
4. Pages serves the updated dashboard from the artifact

`signal_history.json` (baselines for anomaly detection) is persisted across runs via GitHub Actions cache.

## ⚙️ Configuration

For Reddit signals, register a "script" app at <https://www.reddit.com/prefs/apps> and add these to **Settings → Secrets and variables → Actions**:

- `REDDIT_CLIENT_ID`
- `REDDIT_CLIENT_SECRET`

If either is missing, Reddit is skipped silently and the other sources still run.

## 📊 Signal Processing Pipeline

1. **Collection** — sequential queries across four source APIs
2. **Disease Detection** — regex matching against 40+ disease patterns
3. **Geocoding** — 100+ country/city database with region classification
4. **Severity Scoring** — base disease severity + modifiers (deaths, outbreak scale, traveler)
5. **Anomaly Detection** — 2× historical baseline comparison
6. **Deduplication** — hash-based + location clustering
7. **Flight Risk** — IATA hub mapping for affected countries

## ⚕️ Background

The original [GeoSentinel](https://www.istm.org/geosentinel) is a WHO/CDC/ISTM clinic-based surveillance network — 70 clinics worldwide, slow reporting, limited coverage.

**GeoSentinel 2.0** is an OSINT-based complement: faster signal, broader sources, free to run.

## 📜 License

MIT — Use freely. Attribution appreciated.
