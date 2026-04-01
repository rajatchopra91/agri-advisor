# 🌾 Karnataka Agricultural Advisory Agent

An AI-powered farming advisor for Karnataka farmers — built with Google ADK, Gemini 2.5 Flash, and Cloud Run as part of the **Google GenAI Academy APAC Hackathon 2026**.

**Live demo → [rajatchopra91.github.io/agri-advisor](https://rajatchopra91.github.io/agri-advisor)**

---

## What It Does

Farmers describe their problem in plain language. The agent finds the right answer — even without exact keywords.

| Query | What happens |
|---|---|
| *"My ragi crop has grey spots on the leaves"* | Finds Blast Disease without the farmer knowing the disease name |
| *"What should I grow in Dharwad this week?"* | Combines live rainfall forecast + crop recommendation for black cotton soil |
| *"What are current cotton prices in Dharwad?"* | Fetches live wholesale mandi prices from Agmarknet |
| *"What about mango farming in Ramanagara?"* | Map zooms to Ramanagara, returns soil type and crop advisory |

---

## The Problem It Solves

Karnataka has 4 completely different agro-climatic zones. A farmer in Dharwad growing cotton on black cotton soil and a farmer in Udupi growing arecanut on coastal sandy loam need completely different advice — different soil, different rainfall, different crops entirely.

Most agri apps give generic pan-India advice. This agent is hyper-local — scoped to Karnataka's 4 zones across Kharif, Rabi, and Zaid seasons.

---

## Architecture — 5 Layers

```
┌─────────────────────────────────────────────────┐
│  Layer 1 — UI                                   │
│  GitHub Pages (this repo)                       │
│  Chat interface + OpenStreetMap district zoom   │
└────────────────────┬────────────────────────────┘
                     │ HTTP calls
┌────────────────────▼────────────────────────────┐
│  Layer 2 — Framework                            │
│  Google ADK on Cloud Run                        │
│  Registers tools, manages sessions, HTTP server │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│  Layer 3 — Brain                                │
│  Gemini 2.5 Flash                               │
│  Understands query, decides which tool to call  │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│  Layer 4 — Tools (Python functions)             │
│  get_advisory()      → vector search            │
│  get_weather()       → Open-Meteo API           │
│  get_mandi_price()   → Agmarknet API            │
│  get_crop_advisory() → static knowledge         │
│  get_pest_disease_info() → static knowledge     │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│  Layer 5 — Data                                 │
│  Cloud SQL + pgvector (semantic search)         │
│  Open-Meteo API (live weather, free)            │
│  Agmarknet API (live mandi prices, free)        │
└─────────────────────────────────────────────────┘
```

---

## What Makes It Different

**Semantic search** — The agent uses vector embeddings (Google `gemini-embedding-001`) to find answers by meaning, not keywords. A farmer describing symptoms finds the right disease without knowing its name.

**Hyper-local** — 4 Karnataka agro-climatic zones, each with its own crop calendar, soil profile, and pest patterns. Not generic India-wide advice.

**Live data** — Real rainfall forecasts from Open-Meteo and live mandi prices from Government of India's Agmarknet API — not last season's averages.

**District map** — OpenStreetMap auto-zooms to the mentioned district with soil type colour coding and crop context panel.

---

## Tech Stack

| Component | Technology |
|---|---|
| AI Agent Framework | Google ADK (Agent Development Kit) |
| Language Model | Gemini 2.5 Flash |
| Embedding Model | gemini-embedding-001 (3072 dimensions) |
| Vector Database | Cloud SQL for PostgreSQL + pgvector |
| Deployment | Google Cloud Run (asia-south1) |
| Weather Data | Open-Meteo API (free, no key needed) |
| Mandi Prices | Agmarknet / data.gov.in API |
| Frontend Map | Leaflet.js + OpenStreetMap |
| Frontend Hosting | GitHub Pages |
| AI Pair | Claude (Anthropic) |

---

## The Data

The knowledge base has two tiers:

**Tier 1 — Curated Karnataka knowledge**
20 records covering crop advisories, pest and disease remedies, soil profiles, and seasonal calendars — sourced from ICAR and Karnataka State Department of Agriculture published guidelines. Covers all 4 agro-climatic zones across Kharif, Rabi, and Zaid seasons.

**Tier 2 — Live APIs**
- Open-Meteo: 7-day rainfall and temperature forecast by district coordinates
- Agmarknet (data.gov.in): Current wholesale prices by commodity and district

> Note: Gemini 2.5 Flash is Google's pre-trained model. No model training was done. The knowledge base is what the agent looks things up from — like giving a smart assistant a well-organised filing cabinet specific to Karnataka agriculture.

---

## Coverage

**Districts:** 24 Karnataka districts with GPS coordinates and soil mapping

**Agro-climatic zones:**
- 🌾 North Karnataka — Black cotton soil, cotton, soybean, tur
- 🌾 South Karnataka — Red laterite / alluvial, ragi, paddy, maize
- 🌾 Coastal Karnataka — Sandy loam, coconut, arecanut, pepper
- 🌾 Hyderabad-Karnataka — Black cotton, semi-arid, tur, groundnut

**Seasons:** Kharif (June–Nov), Rabi (Oct–Mar), Zaid (Feb–Jun)

---

## Run Locally

```bash
# Clone the repo
git clone https://github.com/rajatchopra91/agri-advisor.git
cd agri-advisor

# Serve locally (avoids file:// CORS issues)
python3 -m http.server 3000

# Open in browser
open http://localhost:3000
```

The frontend calls the live Cloud Run API — no local backend setup needed.

---

## Build Your Own Version

The architecture is reusable for any domain-specific advisory agent. To adapt this:

**1. Replace the knowledge base**
Edit `tools.py` — swap Karnataka agri data for your domain (flood risk, client advisories, product recommendations).

**2. Replace the live APIs**
Swap Open-Meteo and Agmarknet for your own real-time data sources.

**3. Deploy the agent**
```bash
pip install google-adk
gcloud run deploy your-agent-name \
  --source . \
  --region asia-south1 \
  --allow-unauthenticated
```

**4. Update the frontend**
Point `API` in `index.html` to your new Cloud Run URL.

Full agent code (Python) available on request — contact via LinkedIn.

---

## Hackathon Context

Built for **Google GenAI Academy APAC Hackathon 2026** — three tracks submitted:

| Track | What was built |
|---|---|
| Track 1 — Build AI Agents | ADK agent + 5 tools + Cloud Run deployment |
| Track 2 — Real-World Data | Live weather (Open-Meteo) + mandi prices (Agmarknet) via MCP pattern |
| Track 3 — AI-Ready Databases | Semantic vector search via Cloud SQL + pgvector + gemini-embedding-001 |

---

## How This Was Built

This was built using **AI pair programming** — Claude (Anthropic) as the coding partner throughout.

Every decision was mine: the domain choice (Karnataka agriculture), the 4-zone structure, the tool design, the choice of Cloud SQL over AlloyDB when networking issues arose, adding Ramanagara district when a colleague asked about mango farming there.

Claude wrote code faster than I could type it. I directed what to build and why. That's the skill being demonstrated here — not writing every character manually, but knowing what to build, making the right calls, and shipping something real.

Total build time: **1 day**
Total GCP cost: **~₹18**

---

## Contact

**Rajat Chopra** — Platform PM, SatSure Analytics
[LinkedIn](https://linkedin.com/in/rajatchopra91) · Bengaluru, India

*Built with Google ADK · Gemini 2.5 Flash · Cloud Run · pgvector · OpenStreetMap*
