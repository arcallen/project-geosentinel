# 🌐 GeoSentinel 2.0

**Global Disease Surveillance via OSINT**

Real-time disease outbreak monitoring using open-source intelligence. An early-warning layer that complements (does not replace) clinic-based surveillance like the original [GeoSentinel](https://www.istm.org/geosentinel) network — useful for picking up the first weak signals of an outbreak before they reach formal channels.

<img src="https://img.shields.io/badge/signals-live-brightgreen" alt="Live"> <img src="https://img.shields.io/badge/sources-5-blue" alt="Sources"> <img src="https://img.shields.io/badge/updates-every%2030%20min-orange" alt="Updates">

## 🛰️ Data Sources

| Source | Type | Coverage |
|--------|------|----------|
| 🏥 **WHO** | Disease Outbreak News API | Official alerts, global |
| 🌎 **PAHO** | News RSS (WHO Americas region) | Faster than HQ for the Americas |
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
        → WHO + PAHO + GDELT + Mastodon + Reddit OAuth
        → NLP disease detection + geocoding + anomaly scoring
        → case/death count extraction (regex on official sources)
        → signals.json (generated in-runner, not committed)
    → GitHub Pages (deployed from same job's artifact)
        → index.html (Leaflet map + dashboard)
```

**Zero infrastructure cost.** Runs entirely on GitHub Actions + Pages.

## 🚀 Deployment

Auto-deploys via GitHub Pages. Every 30 minutes:

1. Scanner collects signals from five sources
2. Processes, deduplicates, scores, geocodes, and extracts case/death counts
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

The original [GeoSentinel](https://www.istm.org/geosentinel) is the ISTM/CDC travel-medicine surveillance network — ~70 clinics worldwide collecting validated, patient-level data from returning travelers. It is the authoritative source for travel-related infectious disease epidemiology; this project is **not** a substitute for it.

**GeoSentinel 2.0** is an OSINT layer: faster but noisier, useful for flagging signals that warrant a closer look from real epi systems. Absence of signal here does not mean absence of outbreak — coverage is English-skewed and biased toward regions with active news/social media.

## 📜 License

MIT — Use freely. Attribution appreciated.
