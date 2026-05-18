# 🌐 GeoSentinel 2.0

> **⚠️ This is a concept / proof-of-concept project.** It demonstrates what a free, fully open-source outbreak early-warning layer *could* look like if built on top of public APIs. It is **not** a clinical or operational decision-making tool. Signals are unverified, geocoding is approximate, and coverage is heavily biased toward English-language sources. Do not use this to make travel, medical, or public-health decisions.

**Global Disease Surveillance via OSINT**

A weekend-project sketch of a multi-source disease-outbreak monitor. The point isn't the dashboard itself — it's to show that the moving pieces (WHO API, PAHO RSS, GDELT news, Mastodon, Reddit OAuth) can be stitched together with stdlib Python and zero infrastructure cost.

<img src="https://img.shields.io/badge/status-concept-yellow" alt="Concept"> <img src="https://img.shields.io/badge/sources-5-blue" alt="Sources"> <img src="https://img.shields.io/badge/updates-every%2030%20min-orange" alt="Updates"> <img src="https://img.shields.io/badge/license-MIT-lightgrey" alt="MIT">

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
- **Case/death count extraction** — regex pulls counts from source text when present
- **Free-text search** — substring match across disease, location, summary
- **Interactive map** — Leaflet-based with severity-coded markers
- **Source-filtered views** — filter by WHO, PAHO, news, Mastodon, or Reddit

## 🚧 Limitations (read this)

This system has serious limitations as outbreak intelligence. Listed honestly:

- **Single-source signals are unverified.** A Reddit post about feeling sick is not an outbreak. The confidence score reflects source reliability, not signal truth.
- **Coverage is English-skewed.** A Lassa fever outbreak in rural Mali with no English-language news has zero signal density here for days. Absence of signal ≠ absence of outbreak.
- **Geocoding is keyword-based and imperfect.** It can misplace a signal to a country mentioned only in passing (e.g. "Q&A session" matching against an abbreviation).
- **Case/death extraction is regex-only.** When source text says "N cases" it works; when it says "dozens affected" it doesn't. Most WHO DON titles are just "Disease – Country" with no numbers.
- **No cross-source corroboration tiers.** A single tweet renders with the same visual weight as a WHO DON alert (the `source` badge is the only differentiator).
- **No notification of national health authorities.** Real outbreak intelligence systems coordinate with the relevant Ministry of Health. This one publishes to GitHub Pages and that's it.
- **Pipeline depends on third-party feeds staying up.** PAHO RSS, GDELT, Mastodon, Reddit — any of these can change format or rate-limit without notice.

For real outbreak surveillance, look at [WHO EIOS](https://www.who.int/initiatives/eios), [HealthMap](https://healthmap.org/), [ProMED](https://promedmail.org/), and the original clinic-based [GeoSentinel](https://www.istm.org/geosentinel).

## 🏗️ Architecture

```
GitHub Actions (cron 30 min)
    → scanner_v2.py (Python stdlib only)
        → WHO + PAHO + GDELT + Mastodon + Reddit OAuth
        → NLP disease detection + geocoding + anomaly scoring
        → case/death count extraction (regex)
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

1. **Collection** — sequential queries across five source APIs
2. **Disease Detection** — word-boundary regex against ~45 disease patterns
3. **Geocoding** — ~100 city/country database with word-boundary matching for short keys
4. **Severity Scoring** — base disease severity + modifiers (deaths, outbreak scale, traveler)
5. **Case/Death Extraction** — conservative regex with sanity caps
6. **Anomaly Detection** — 2× historical baseline comparison
7. **Deduplication** — hash-based + location clustering
8. **Flight Risk** — IATA hub mapping for affected countries

## ⚕️ Background

The original [GeoSentinel](https://www.istm.org/geosentinel) is the ISTM/CDC travel-medicine surveillance network — ~70 clinics worldwide collecting validated, patient-level data from returning travelers. It is the authoritative source for travel-related infectious disease epidemiology; this project is **not** a substitute for it.

**GeoSentinel 2.0** is a sketch of what an OSINT layer on top of that might look like: faster but noisier, useful for flagging signals that warrant a closer look from real epi systems.

## 📜 License

[MIT](LICENSE). Use freely; attribution appreciated. No warranty — see LICENSE for the full disclaimer of liability.
