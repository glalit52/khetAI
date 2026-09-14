[README.md](https://github.com/user-attachments/files/32191085/README.md)
# Khet AI MVP

Run `python3 app.py`, then open `http://localhost:8000`. The application serves the dashboard and its API from one origin.

## Vercel deployment

`vercel.json` makes Vercel serve `outputs/index.html` at the deployment root, preventing a `404 NOT_FOUND` for `/`. In Vercel, set the Framework Preset to **Other** and leave the Build Command empty, then redeploy.

This Vercel configuration deploys the dashboard. The included `app.py` uses a local SQLite file and a long-running local HTTP server, neither of which is suitable for production Vercel persistence. Keep the dashboard on Vercel only for a UI preview, or move the API to a persistent service/database before enabling real farm data and approval actions. A production Vercel backend needs a managed database (for example Vercel Postgres, Neon, or Supabase), environment variables for provider credentials, and Vercel Functions or a FastAPI service—not the local `app.py` process.

## What is implemented

- SQLite-backed farm digital twin: farms, fields, observations, recommendations, alerts and audit log.
- Farm onboarding API with fields, location and crop context.
- Weather connector using Open-Meteo (no API key). It ingests a farm-specific forecast and evaluates rules.
- Generic satellite/sensor/partner ingestion webhook. A Sentinel/STAC or IoT connector can POST normalized observations without changing the decision model.
- Deterministic crop-risk rules for irrigation, humidity-related tomato inspection, and satellite vigour anomalies.
- Recommendation provenance, confidence, urgency and human approval state.
- In-app alert records. WhatsApp, SMS, IVR and irrigation-controller adapters should be implemented with a provider account and explicit farmer consent.

## API examples

`GET /api/health` · `GET /api/farms` · `GET /api/farms/1/twin` · `GET /api/farms/1/recommendations`

`POST /api/farms/1/refresh-weather` refreshes Open-Meteo data. `POST /api/farms/1/evaluate` runs the crop rules. `POST /api/recommendations/1` approves a recommendation. `POST /api/ingest/observation` receives a normalized weather, sensor or satellite observation.

Example observation body:

```json
{"farm_id":1,"field_id":1,"source":"satellite","kind":"vegetation","provider":"sentinel_processor","payload":{"ndvi":0.58,"baseline_ndvi":0.67,"anomaly":"west zone decline"}}
```

This is an MVP, not agricultural advice or a chemical/irrigation control system. Production deployment needs authenticated users, consent, encryption, provider contracts, agronomist validation, alert delivery adapters, rate limiting, monitoring and regulatory review.
