# G-Solution-Smart-Supply-Chains

⚓  SHIPGUARD
Resilient Logistics & Dynamic Supply Chain Optimization
─────────────────────────────────────────────────────
COMPLETE TECHNICAL PROJECT DOCUMENTATION
Maritime Transport Focus  |  Open Innovation Challenge  |  v2.0
April 2026

0. Challenge Brief
Theme: Resilient Logistics and Dynamic Supply Chain Optimization

Context
Modern global supply chains manage millions of concurrent shipments across highly complex and inherently volatile transportation networks. Critical transit disruptions — ranging from sudden weather events to hidden operational bottlenecks — are chronically identified only after delivery timelines are already compromised.
Objective
Design a scalable system capable of continuously analyzing multifaceted transit data to preemptively detect and flag potential supply chain disruptions. Formulate dynamic mechanisms that instantly execute or recommend highly optimized route adjustments before localized bottlenecks cascade into broader delays.

0.1 Our Response — ShipGuard
ShipGuard is a real-time maritime supply chain intelligence platform built around the two pillars of this challenge: proactive disruption detection and dynamic route optimization. Rather than alerting stakeholders after a disruption has cascaded into a delay, ShipGuard continuously scores risk across every active maritime route and executes or recommends adjustments before bottlenecks become crises.

90%+
of global trade moves by sea
89%
of executives investing in gen AI for supply chain
326%
more profitability for supply chain innovators
51%
cite real-time demand response as top priority

1. Executive Summary
Project Name
ShipGuard — Resilient Logistics & Dynamic Supply Chain Optimization
Challenge Theme
Smart Supply Chains — Open Innovation
Domain Focus
Maritime / Ship Transport — Global cargo vessel route intelligence
Target Users
Logistics managers, freight forwarders, port operators, supply chain analysts
Core Value Prop
Detect disruptions before they cascade. Recommend reroutes before delays materialise.

1.1 Problem Statement
Global maritime supply chains handle millions of concurrent shipments daily across volatile ocean routes. The core failure mode is timing: disruptions — storms, port congestion, piracy, geopolitical closures — are discovered reactively, after delivery timelines are already broken. There is no continuous, intelligent, proactive monitoring layer accessible to mid-sized logistics operators.

1.2 How ShipGuard Solves It
    • Continuously ingests live AIS vessel position data, marine weather forecasts, port congestion feeds, and news events
    • Calculates a composite risk score (0–100) for every tracked route every 5 minutes
    • Triggers instant alerts when risk crosses configurable thresholds — before delays occur
    • Recommends or auto-selects optimized alternate routes using AI-driven comparative analysis
    • Generates plain-English AI briefings (via Claude API) explaining the disruption and recommended action
    • Automates the entire pipeline via n8n — no manual monitoring required

2. System Architecture
2.1 Four-Layer Architecture
Layer
Components & Responsibility
Layer 1 Data Ingestion
n8n workflows poll AIS APIs (Datalastic/AISHub), Open-Meteo Marine weather, NewsAPI, and port status feeds every 5 minutes. Raw data is normalised and written into PostgreSQL.
Layer 2 Intelligence
Python FastAPI service: ML risk scoring model reads from DB, computes per-route disruption risk score, writes back scores + alerts. Claude API called for NL report generation. Anomaly detector flags vessel deviations.
Layer 3 API Gateway
Node.js + Express REST API serves the React frontend and external consumers. Socket.io pushes real-time vessel position and alert updates. Redis caches frequent queries.
Layer 4 Presentation
React + Vite dashboard: interactive Leaflet vessel map, risk score panels, alert inbox, port status cards, AI briefing panel, alternative route recommender.

2.2 Data Flow — End to End
# ── INGESTION LAYER (n8n, every 5 min) ──────────────────────────────────
[AIS API]        ──►  n8n:fetch_vessels   ──►  DB: vessels
[Open-Meteo]     ──►  n8n:fetch_weather   ──►  DB: weather_events
[NewsAPI]        ──►  n8n:fetch_news      ──►  DB: news_events
[Port Status]    ──►  n8n:fetch_ports     ──►  DB: port_status

# ── INTELLIGENCE LAYER (Python FastAPI) ────────────────────────────────
DB: vessels + weather + news + ports
  ──►  risk_scorer.py  ──►  risk_score (0-100 per route)  ──►  DB: route_risk
  ──►  anomaly.py      ──►  vessel deviation flags         ──►  DB: anomalies
  ──►  Claude API      ──►  NL disruption report           ──►  DB: ai_reports

# ── ALERT DISPATCH (n8n, triggered on risk_score > threshold) ──────────
DB: route_risk  ──►  n8n:alert_workflow
  ──►  Gmail node  (email alert to subscribed users)
  ──►  Twilio node (SMS for critical severity)
  ──►  Slack node  (team channel notification)

# ── PRESENTATION LAYER (React ◄──► Node.js API) ─────────────────────────
React  ──►  GET /api/vessels        ──►  Node.js  ──►  Redis / PostgreSQL
React  ◄──  Socket.io push          ◄──  Node.js  (real-time vessel updates)
React  ──►  GET /api/alerts         ──►  Node.js  ──►  PostgreSQL
React  ──►  GET /api/report/daily   ──►  Node.js  ──►  Claude API
React  ──►  POST /api/reroute       ──►  Python FastAPI (route optimiser)

2.3 Database Schema
Key Tables
Table
Key Columns
Purpose
vessels
mmsi, imo, name, lat, lon, speed, heading, destination, eta, vessel_type, last_updated
Live AIS vessel positions updated every 5 min
weather_events
id, lat, lon, wave_height, wind_speed, storm_flag, recorded_at
Marine weather snapshots from Open-Meteo Marine API
port_status
port_id, name, congestion_index, vessels_at_anchor, weather_risk, updated_at
Top 50 global port congestion indicators
route_risk
route_id, origin_port, dest_port, risk_score, risk_factors (JSON), recommended_action, computed_at
Composite AI risk score per active route
disruption_alerts
id, alert_type, severity (1-5), affected_region, risk_score, description, created_at, resolved_at
Active and historical disruption events
route_alternatives
id, original_route_id, alt_route (JSON), eta_delta_hrs, cost_delta_pct, ai_recommendation, created_at
AI-generated alternative route suggestions
ai_reports
id, report_type, content, generated_at, model_used
Claude-generated daily and on-demand briefings
user_subscriptions
id, email, phone, tracked_routes (JSON), alert_threshold, notify_email, notify_sms
User alert preferences and subscribed routes

3. Technology Stack
3.1 Frontend
Technology
Version
Role
React.js
18+
Component-based UI framework — main dashboard
Vite
5+
Lightning-fast development build tooling
Tailwind CSS
3+
Utility-first styling for responsive layout
Leaflet.js / React-Leaflet
Latest
Interactive world map with vessel markers, weather overlays, route lines
Recharts / Chart.js
Latest
Risk score gauges, trend charts, congestion heat maps
Socket.io Client
Latest
Real-time vessel position updates without page refresh
TanStack Query
Latest
Data fetching, caching and background sync for API calls
Zustand
Latest
Lightweight global state management (selected vessel, filters)

3.2 Backend (Node.js)
Technology
Version
Role
Node.js + Express.js
20 LTS
REST API gateway — serves frontend, routes to Python AI service
Socket.io Server
Latest
Broadcasts real-time vessel + alert events to connected clients
PostgreSQL
15+
Primary relational DB — vessels, alerts, routes, users
Redis
7+
API response caching (vessel list), session store, pub/sub for alerts
node-postgres (pg)
Latest
PostgreSQL client with connection pooling
ioredis
Latest
Redis client with retry logic and cluster support
Axios
Latest
Internal HTTP calls to Python AI service endpoints

3.3 AI & ML Service (Python)
Technology
Version
Role
Python + FastAPI
3.11+
Async REST API service exposing /risk/score, /reroute, /anomaly endpoints
scikit-learn
1.4+
Trained ML classification model for disruption risk prediction
pandas + numpy
Latest
Feature engineering from raw AIS, weather, and news data
Anthropic Claude API
claude-sonnet-4
Natural language disruption reports, alternative route reasoning, chatbot
AIML.com API
Latest
Secondary LLM: cargo demand classification, sentiment scoring on news
Google Colab
—
ML prototyping: EDA, feature engineering, model training notebooks
joblib
Latest
Model serialisation — save/load trained .pkl risk model

3.4 Automation & Orchestration (n8n)
n8n is the backbone data pipeline — it replaces the need for writing custom cron jobs and polling scripts. All five core workflows are visual, maintainable, and re-triggerable.
Workflow Name
What It Does
01_vessel_ingest
Every 5 min: polls Datalastic AIS API → normalises vessel JSON → upserts into PostgreSQL vessels table
02_weather_ingest
Every 15 min: polls Open-Meteo Marine API for 50 major shipping corridors → stores wave/wind data
03_news_ingest
Every 30 min: fetches maritime keywords from NewsAPI → Claude sentiment score → stores in news_events
04_alert_dispatch
Triggered when risk_score > threshold in DB: sends email (Gmail), SMS (Twilio), Slack message
05_daily_report
06:00 UTC daily: aggregates overnight data → calls Claude API → generates HTML email report → sends to all subscribers

3.5 Deployment Infrastructure
Component
Service
Notes
Frontend
Vercel (free)
Auto-deploys from GitHub main branch; custom domain support
Node.js API
Railway.app
Dockerfile-based deploy, auto-restarts, env vars in dashboard
Python AI Service
Railway.app (separate service)
FastAPI with uvicorn, internal private networking
PostgreSQL
Railway addon or Supabase
Free tier: 500MB Railway / 500MB Supabase
Redis
Upstash Redis (serverless)
Free tier: 10k req/day, zero infrastructure
n8n
n8n.cloud free OR Railway
n8n.cloud: 5 free workflows; or self-host Docker on Railway
CI/CD
GitHub Actions
Lint → test → build → deploy on push to main
Demo Sharing
Antigravity
Instant shareable URL for hackathon judges, zero config
Monitoring
UptimeRobot + Sentry
Free uptime checks + error tracking with stack traces

4. APIs & Data Sources
4.1 Vessel / AIS Tracking APIs
API / Source
Data Available
Cost
Recommended Use
Datalastic API datalastic.com
Real-time AIS positions, vessel DB (MMSI/IMO), ETA, port calls, historical tracks
Free trial (credits), then paid
PRIMARY — best free trial for dev/demo
AISHub.net
Aggregated AIS JSON/CSV/XML, global coverage, near real-time
Free (share AIS station data)
FREE option — ideal for MVP if you share an AIS feed
MarineTraffic API marinetraffic.com
Gold-standard: AIS, voyage data, port arrivals/departures, expected arrivals, vessel photos
Paid (credit bundles)
PRODUCTION — use when scaling beyond demo
VesselFinder API vesselfinder.com
Live positions, voyage info, ship details, port congestion
Freemium tier available
Backup AIS source if Datalastic credits run out
ShipXplorer Widget shipxplorer.com
Embeddable vessel tracking map widget, coverage map, port view
Free widget embed tier
DEMO MAP — embed for zero-effort live map in MVP
OpenSeaMap (OSM)
Port locations, shipping lane polygons, nautical chart overlays
100% free and open
Leaflet tile layer for maritime chart backdrop

Free-first API Strategy for Development / Hackathon:
Step 1 — Register at datalastic.com (free API key, ~100 credits to start). Use for all vessel queries during dev and demo.
Step 2 — Embed ShipXplorer free widget for the map view. No API key needed — just an iframe.
Step 3 — When credits run low, fall back to AISHub.net demo feed or use a CSV of cached vessel snapshots for the demo.

4.2 Weather & Marine Condition APIs
API
Marine Data Provided
Cost & Limits
Open-Meteo Marine API open-meteo.com/en/docs/marine-weather-api
Wave height, swell direction/period, wind speed at sea, ocean current speed, sea surface temp, 7-day marine forecast
100% FREE — no API key, no rate limit for reasonable use. BEST for this project.
OpenWeatherMap openweathermap.org
Current wind, visibility, storm alerts, 5-day forecast for port coordinates
Free tier: 1,000 API calls/day
Stormglass.io
Marine-specific data: wave period, water temp, ice conditions, UV
Free tier: 10 req/day (sufficient for demo)

4.3 Intelligence APIs
API
Role in ShipGuard
Cost
Anthropic Claude API anthropics.com
Generate plain-English disruption briefings, explain risk scores, power chatbot ('Is the Red Sea safe this week?'), reason about alternative routes
Pay-per-token. Very affordable for demo scale (~$0.01 per report).
AIML.com API
Secondary LLM tasks: cargo demand classification, news sentiment scoring, route feasibility pre-screening
Free tier available — good fallback
NewsAPI.org
Fetch maritime, port, and geopolitical news in real time to feed disruption context into risk model
Free tier: 100 req/day, 1-month archive

4.4 Notification APIs
Service
Alert Channel
Cost
Gmail (n8n built-in node)
Email alerts and daily digest reports to subscribers
Free with Google account
Twilio SMS API
SMS for severity-5 (critical) disruptions — immediate action needed
Free trial credits (~$15 value)
Slack Incoming Webhook
Post disruption cards to a team Slack channel
Free — no account upgrade needed

5. Feature Specification
5.1 Core Features — Phase 1 MVP
#
Feature
Detail
Challenge Pillar
F1
Live Vessel Map
Interactive Leaflet.js map displaying all active cargo vessels with colour-coded risk pins. Click any vessel for full detail drawer. Toggle weather overlay and shipping lane layers.
Detection
F2
Route Risk Score Engine
Every active route scored 0–100 every 5 minutes based on: marine weather severity, port congestion at origin/destination, vessel deviation anomalies, news sentiment. Score breakdown visible per factor.
Detection
F3
Disruption Alert Dashboard
Sorted alert inbox: severity badge, alert type (WEATHER/CONGESTION/ANOMALY/NEWS), affected region, risk score, time elapsed. One-click route detail view from each alert.
Detection
F4
Dynamic Route Optimizer
When risk > threshold: system auto-generates 2–3 alternative routes with comparative data: new ETA, ETA delta, cost delta estimate, risk score of alternative. User selects or dismisses.
Optimization
F5
Port Status Panel
Top 50 global ports: congestion index (Low/Medium/High/Severe), vessels at anchor, weather risk, time-to-berth estimate. Sortable and filterable.
Detection
F6
AI Disruption Briefing
Claude-generated daily and on-demand plain-English reports summarising global maritime risk. Explains the why behind each alert. Accessible from dashboard and via email.
Both
F7
Alert Subscription
Users register email/SMS, select routes by port pair or vessel MMSI. Set personalised risk threshold (e.g. alert me when score > 60). Managed in Settings page.
Detection
F8
Marine Weather Overlay
Toggle layer on map showing wave height heatmap and wind speed arrows from Open-Meteo, updated every 15 min. Storm cells visually marked.
Detection

5.2 Advanced Features — Phase 2
#
Feature
Detail
Challenge Pillar
F9
Vessel Anomaly Detector
ML model flags vessels deviating significantly from expected route or speed profile. Uses z-score on speed, heading delta. Flags piracy risk zones using historical incident data.
Detection
F10
News Sentiment Feed
Real-time maritime news with AI sentiment tagging (Positive/Neutral/Negative/Critical) and route impact mapping. Click a news item to see affected vessels.
Detection
F11
AI Route Chatbot
Ask natural-language questions: 'Is the Suez Canal clear this week?' or 'What is the safest route from Mumbai to Rotterdam right now?' Powered by Claude API + web search.
Optimization
F12
Fleet Tracker
Companies register their own vessel MMSI numbers. Custom fleet dashboard, fleet-wide risk overview, consolidated alert inbox for owned vessels only.
Both
F13
Disruption Archive
Browse and filter all historical alerts and disruptions with timeline view. Export CSV for insurance, compliance, or post-incident analysis.
Detection
F14
Cost Impact Calculator
When a disruption is detected: auto-calculates estimated cost impact of delay (fuel, port fees, contract penalties) based on vessel type and cargo class.
Optimization

6. Full Project File Structure
6.1 Root Repository
shipguard/
├── frontend/                  # React + Vite SPA
├── backend/                   # Node.js + Express REST API
├── ai-service/                # Python FastAPI — ML risk scoring + route optimiser
├── n8n-workflows/             # Exported n8n workflow JSON files (import & activate)
├── database/
│   ├── migrations/
│   │   └── 001_init.sql       # All table CREATE statements
│   └── seeds/
│       └── seed_ports.sql     # Top 50 ports reference data
├── notebooks/                 # Google Colab ML notebooks
│   ├── 01_EDA.ipynb
│   ├── 02_feature_engineering.ipynb
│   └── 03_model_training.ipynb
├── docs/                      # This document + API references
├── .github/
│   └── workflows/
│       ├── frontend-ci.yml    # Lint + build + deploy to Vercel
│       └── backend-ci.yml     # Test + deploy to Railway
├── docker-compose.yml         # Local: PostgreSQL + Redis + Node + Python
├── .env.example               # All env var names (no values)
└── README.md

6.2 Frontend
frontend/
├── public/favicon.ico
└── src/
    ├── components/
    │   ├── Map/
    │   │   ├── VesselMap.jsx           # Main Leaflet map — vessel markers, route lines
    │   │   ├── VesselMarker.jsx        # Risk-colour-coded ship pin with popup
    │   │   ├── WeatherOverlay.jsx      # Marine weather heatmap tile layer
    │   │   └── ShippingLaneLayer.jsx   # OpenSeaMap nautical overlay
    │   ├── Dashboard/
    │   │   ├── AlertList.jsx           # Sortable live alert inbox
    │   │   ├── RiskGauge.jsx           # Animated 0-100 gauge per route
    │   │   ├── PortStatusPanel.jsx     # Port congestion cards grid
    │   │   └── StatsBar.jsx            # Top: active alerts, vessels tracked, avg risk
    │   ├── Vessel/
    │   │   ├── VesselDrawer.jsx        # Slide-in detail panel on vessel click
    │   │   ├── VesselSearch.jsx        # Search by name or MMSI number
    │   │   └── AnomalyBadge.jsx        # Red badge for flagged anomalous vessels
    │   ├── RouteOptimizer/
    │   │   ├── AlternativeRoutes.jsx   # Cards showing 2-3 alternate routes
    │   │   └── RouteCompareTable.jsx   # ETA / cost / risk comparison table
    │   ├── Reports/
    │   │   ├── AIBriefingPanel.jsx     # Claude daily report rendered as rich text
    │   │   └── ChatbotPanel.jsx        # Floating AI chatbot interface
    │   └── common/
    │       ├── Navbar.jsx
    │       ├── Sidebar.jsx
    │       ├── SeverityBadge.jsx       # Colour-coded 1-5 severity pill
    │       └── LoadingSpinner.jsx
    ├── pages/
    │   ├── Dashboard.jsx               # Main view: map + alerts + ports
    │   ├── Alerts.jsx                  # Full alert management + history
    │   ├── RouteOptimizer.jsx          # Route search + disruption + alternatives
    │   ├── Reports.jsx                 # AI reports + archive
    │   ├── Fleet.jsx                   # Company fleet tracker (Phase 2)
    │   └── Settings.jsx                # Subscription + alert threshold config
    ├── hooks/
    │   ├── useVessels.js               # React Query: vessel list with 30s polling
    │   ├── useAlerts.js                # React Query: active alerts
    │   ├── useRouteRisk.js             # React Query: risk scores per route
    │   └── useSocket.js                # Socket.io connection + real-time events
    ├── services/
    │   ├── api.js                      # Axios instance with base URL + auth header
    │   ├── vesselService.js
    │   ├── alertService.js
    │   ├── routeService.js
    │   └── reportService.js
    ├── utils/
    │   ├── riskUtils.js                # risk score → colour, label, severity
    │   └── formatters.js               # Dates, coordinates, knots → km/h
    ├── store/                          # Zustand: selectedVessel, mapFilters
    ├── App.jsx
    └── main.jsx

6.3 Backend (Node.js)
backend/
├── src/
│   ├── routes/
│   │   ├── vessels.js            # GET /api/vessels, GET /api/vessels/:mmsi
│   │   ├── alerts.js             # GET /api/alerts, POST /api/alerts/:id/resolve
│   │   ├── ports.js              # GET /api/ports/status
│   │   ├── routes.js             # GET /api/routes/risk, POST /api/reroute
│   │   ├── reports.js            # GET /api/reports/daily, POST /api/reports/generate
│   │   └── users.js              # POST /api/users/subscribe, PUT /api/users/prefs
│   ├── controllers/
│   │   ├── vesselController.js
│   │   ├── alertController.js
│   │   ├── routeController.js    # Calls Python AI service for reroute
│   │   └── reportController.js   # Calls Claude API via claudeService
│   ├── services/
│   │   ├── aisService.js         # Datalastic / AISHub API client
│   │   ├── claudeService.js      # Anthropic API wrapper — report + chat
│   │   ├── cacheService.js       # Redis get/set helpers with TTL
│   │   └── aiService.js          # HTTP client to Python FastAPI service
│   ├── models/
│   │   ├── Vessel.js             # DB query helpers for vessels table
│   │   ├── Alert.js
│   │   └── RouteRisk.js
│   ├── socket/
│   │   └── vesselSocket.js       # Emits vessel:update, alert:new events
│   ├── middleware/
│   │   ├── auth.js               # JWT verify middleware
│   │   └── rateLimiter.js        # express-rate-limit: 100 req/min per IP
│   ├── config/
│   │   ├── db.js                 # pg Pool — PostgreSQL connection
│   │   └── redis.js              # ioredis client singleton
│   └── app.js                    # Express + middleware setup
├── server.js                     # Entry point: HTTP server + Socket.io attach
├── package.json
└── .env.example

6.4 AI Service (Python FastAPI)
ai-service/
├── main.py                          # FastAPI app: CORS, routers, startup
├── routers/
│   ├── risk_score.py                # POST /risk/score — compute route risk 0-100
│   ├── reroute.py                   # POST /reroute — generate alt route options
│   └── anomaly.py                   # POST /anomaly/detect — vessel deviation flag
├── models/
│   ├── risk_model.pkl               # Serialised scikit-learn trained model
│   └── schemas.py                   # Pydantic request/response schemas
├── services/
│   ├── feature_builder.py           # Build feature vector from raw vessel+weather data
│   ├── weather_client.py            # Open-Meteo Marine API calls
│   ├── news_sentiment.py            # NewsAPI fetch + AIML.com sentiment scoring
│   └── reroute_engine.py            # Route comparison logic + Claude reasoning call
├── utils/
│   └── geo_utils.py                 # Haversine distance, bounding box helpers
└── requirements.txt

6.5 Google Colab Notebooks
Notebook
Contents
01_EDA.ipynb
Load AIS + weather CSV datasets; explore distributions of vessel speed, route deviations, port wait times; visualise disruption frequency by region
02_feature_engineering.ipynb
Build ML features: weather_severity_score, route_deviation_index, port_congestion_lag, news_sentiment_rolling_avg, historical_disruption_rate per corridor
03_model_training.ipynb
Train Random Forest classifier: disrupted vs non-disrupted routes; evaluate precision/recall; export risk_model.pkl for FastAPI deployment

7. Risk Scoring Algorithm
7.1 Composite Risk Score Formula
The Route Risk Score is a weighted composite of five independently computed sub-scores, each normalised 0–100. The weights reflect empirical significance of each factor in maritime disruption events:

# Route Risk Score Formula
Risk_Score = (
    0.35 * weather_risk_score       # Dominant factor — storm severity at route coords
  + 0.25 * port_congestion_score    # Origin + destination congestion combined
  + 0.20 * vessel_anomaly_score     # Speed deviation + off-route heading
  + 0.12 * news_sentiment_score     # Negative news sentiment for route region
  + 0.08 * historical_risk_score    # Base rate: historical disruptions on this corridor
)

# All sub-scores are 0–100. Final score is 0–100.
# Thresholds:
#   0-39  = LOW     (green)  — no action
#   40-59 = MEDIUM  (yellow) — monitor closely
#   60-79 = HIGH    (orange) — alert dispatched
#   80+   = CRITICAL(red)    — immediate reroute recommended

7.2 Sub-Score Computation
Sub-Score
Weight
How Computed
weather_risk_score
35%
Open-Meteo Marine API: wave_height > 4m → +60 pts, wind_speed > 60kmh → +40 pts, storm_flag=true → +80 pts. Normalised 0-100. Sampled at 5 waypoints along route.
port_congestion_score
25%
Average of origin and destination port congestion_index (Low=10, Medium=40, High=70, Severe=100). Updated every 15 min from port status feed.
vessel_anomaly_score
20%
Z-score of current speed vs. vessel type average. If |z| > 2.5 → high anomaly. Heading deviation from expected bearing > 30°  → additional penalty. Combined 0-100.
news_sentiment_score
12%
NewsAPI: fetch last 24h news for route region keywords. Claude/AIML.com classifies each article sentiment (-1 to +1). Aggregate score inverted to risk: negative news = high risk score.
historical_risk_score
8%
Lookup: for this origin-destination port pair, what % of routes in past 90 days were disrupted? Stored in DB as a base_rate per corridor. Static but updated weekly.

7.3 Route Optimisation Logic
When a route's Risk Score crosses 60 (HIGH), the route optimiser is triggered:
    1. Query all viable alternative routes between origin and destination port pair from a pre-computed graph of major shipping corridors
    2. For each alternative: compute its Risk Score using the same formula above
    3. Calculate ETA delta (hours added/saved vs original route) using distance + average vessel speed
    4. Estimate cost delta using fuel consumption model (distance × vessel fuel rate) + port fee lookup
    5. Pass top 3 alternatives to Claude API with the prompt: 'Given these route options and their risk/cost/time profiles, recommend the optimal rerouting decision and explain your reasoning'
    6. Present Claude's recommendation + all alternatives to the user in the Route Optimizer panel

8. Development Roadmap
Phase 1 — Foundation & MVP (Weeks 1–2)
Timeline
Tasks
Exit Criteria
W1 Day 1–2
GitHub repo init, folder structure, docker-compose (PostgreSQL + Redis), Vite React scaffold, Express server scaffold, PostgreSQL schema (001_init.sql)
Local dev environment running all services
W1 Day 3–4
Datalastic API integration → store vessels in DB, Leaflet map with vessel markers, basic vessel detail drawer, Open-Meteo Marine weather fetch + weather layer on map
Live vessels visible on interactive map
W1 Day 5
Port status panel (50 ports, congestion index from API or seeded data), Socket.io setup for real-time vessel position push to React
Real-time updates working in browser
W2 Day 1–2
Rule-based Risk Score engine (weather + congestion only, first pass), risk colour-coded map pins, alert dashboard panel, alert DB table + insert on high score
Risk scores visible, alerts in dashboard
W2 Day 3
n8n: workflow 01 (vessel ingest every 5 min), workflow 04 (alert dispatch via Gmail on risk > 60)
Automated data pipeline + email alerts live
W2 Day 4
Claude API: daily disruption briefing report, AI Briefing Panel in React, n8n workflow 05 (daily report email)
AI report rendering in dashboard
W2 Day 5
UI polish, error handling, deploy: Vercel (frontend) + Railway (backend + DB), end-to-end test, Antigravity demo link
Live deployed MVP, shareable URL

Phase 2 — ML & Route Optimiser (Weeks 3–4)
    • Google Colab: EDA on historical AIS + weather + disruption datasets
    • Feature engineering: build feature vectors for ML model (5 sub-scores)
    • Train scikit-learn Random Forest classifier on labelled disruption events
    • Evaluate model (precision, recall, F1) — target >80% F1 on test set
    • Export risk_model.pkl → deploy as FastAPI /risk/score endpoint
    • Replace rule-based scoring with ML model output in Node.js API
    • Build route optimiser: alternative route graph + ETA/cost delta calculator
    • Claude API reroute reasoning: pass alternatives, get recommended action
    • Alternative Routes Panel in React: comparison table + Claude recommendation
    • Vessel anomaly detector: z-score model on speed deviation, heading delta

Phase 3 — Advanced Features & Production Hardening (Weeks 5–6)
    • News sentiment feed with AI tagging (AIML.com API integration)
    • AI chatbot panel (Claude + web search tool): natural language route queries
    • Fleet tracker: company registers own MMSIs, custom fleet dashboard
    • User authentication: Firebase Studio auth or Supabase Auth + JWT
    • Redis caching strategy: cache vessel list (30s TTL), port status (5min TTL)
    • API rate limiting, input validation, HTTPS enforcement
    • GitHub Actions CI/CD: lint → test → auto-deploy on merge
    • Sentry error monitoring, UptimeRobot health checks
    • Write unit tests (Vitest frontend, Jest backend), integration tests (Supertest)
    • Final demo video, README, submission documentation

9. Tools — Roles & Integration
Tool
Where Used
Specific Role in ShipGuard
Firebase Studio
Frontend development IDE
Build and iterate React UI components with live preview; optionally use Firestore for real-time vessel data sync as alternative to Socket.io; Firebase Auth for user login (Phase 2)
Antigravity
Demo sharing
One-command instant public URL for sharing the frontend build during hackathon — judges click a link, see live system with no setup required
n8n
Data pipeline + alerts
All 5 automated workflows: vessel data ingestion every 5 min, weather polling, news fetch, alert dispatch (email/SMS/Slack), daily AI report generation — zero custom cron code
Google Colab
ML development
EDA on historical AIS datasets, feature engineering notebooks, training the Random Forest risk classification model — then export .pkl to FastAPI service
AIML.com API
AI tasks (secondary)
News sentiment scoring, cargo demand classification, secondary LLM for route pre-screening — used alongside Claude API to distribute token costs
Claude API
Core AI intelligence
Primary AI for: plain-English disruption briefings, route optimisation reasoning, chatbot Q&A, risk score explanations. Uses claude-sonnet-4 model.
GitHub Actions
CI/CD
Automated pipelines: on push to feature branch → lint + test; on merge to main → auto-deploy frontend to Vercel + backend to Railway
Docker Compose
Local dev
Single docker-compose up starts PostgreSQL, Redis, pgAdmin panel — developers don't need to install DBs locally

10. Environment Variables
All secrets are stored as environment variables — never committed to Git. Copy .env.example to .env in each service folder and populate values.

10.1 Backend (/backend/.env)
# ── PostgreSQL ──────────────────────────────────────────
DATABASE_URL=postgresql://user:password@localhost:5432/shipguard

# ── Redis ───────────────────────────────────────────────
REDIS_URL=redis://localhost:6379

# ── AIS Vessel Data APIs ────────────────────────────────
DATALASTIC_API_KEY=your_datalastic_api_key
MARINETRAFFIC_API_KEY=your_mt_key_for_production
AISHUB_USERNAME=your_aishub_username

# ── Weather ─────────────────────────────────────────────
OPENWEATHER_API_KEY=your_openweather_key
# Open-Meteo Marine: no key needed

# ── AI ──────────────────────────────────────────────────
ANTHROPIC_API_KEY=your_claude_api_key
AIML_API_KEY=your_aiml_api_key

# ── News ─────────────────────────────────────────────────
NEWSAPI_KEY=your_newsapi_key

# ── Notifications ───────────────────────────────────────
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
TWILIO_PHONE_NUMBER=+1234567890

# ── Application ─────────────────────────────────────────
JWT_SECRET=minimum_32_character_random_secret_here
PORT=3001
FRONTEND_URL=http://localhost:5173
AI_SERVICE_URL=http://localhost:8000
NODE_ENV=development

10.2 AI Service (/ai-service/.env)
DATABASE_URL=postgresql://user:password@localhost:5432/shipguard
ANTHROPIC_API_KEY=your_claude_api_key
AIML_API_KEY=your_aiml_api_key
NEWSAPI_KEY=your_newsapi_key
MODEL_PATH=models/risk_model.pkl
PORT=8000

10.3 Frontend (/frontend/.env)
VITE_API_URL=http://localhost:3001
VITE_SOCKET_URL=http://localhost:3001
VITE_ANTHROPIC_API_KEY=your_claude_api_key   # Only for Artifact AI features
VITE_MAPBOX_TOKEN=optional_for_premium_maps

11. Deployment Guide
11.1 Local Development Setup
    7. Clone: git clone https://github.com/yourteam/shipguard && cd shipguard
    8. Start infrastructure: docker-compose up -d  (launches PostgreSQL + Redis)
    9. Run schema: psql $DATABASE_URL < database/migrations/001_init.sql
    10. Seed ports: psql $DATABASE_URL < database/seeds/seed_ports.sql
    11. Backend: cd backend && npm install && cp .env.example .env && npm run dev
    12. AI Service: cd ai-service && pip install -r requirements.txt && uvicorn main:app --reload
    13. Frontend: cd frontend && npm install && cp .env.example .env && npm run dev
    14. n8n: import files from n8n-workflows/*.json into n8n interface, set credentials, activate
    15. Open http://localhost:5173 — vessel map should load with live AIS data

11.2 Production Deployment
Frontend → Vercel
    16. Connect GitHub repo to vercel.com, select frontend/ as root directory
    17. Set env var: VITE_API_URL=https://your-backend.railway.app
    18. Every push to main auto-deploys — Vercel handles CDN and HTTPS

Backend + AI Service + Database → Railway
    19. Create new project at railway.app, add PostgreSQL and Redis plugins
    20. Deploy backend: connect GitHub repo, set root to backend/, add all env vars
    21. Deploy ai-service: second service in same Railway project, root = ai-service/
    22. Set AI_SERVICE_URL in backend env to Railway's internal URL for ai-service
    23. Run database migrations from Railway's PostgreSQL console

n8n → n8n.cloud or Railway
    24. Option A: n8n.cloud free account — import workflows, add credentials, activate
    25. Option B: Deploy n8n Docker image as a Railway service for self-hosted control

Quick Demo via Antigravity
    • cd frontend && npm run build
    • antigravity deploy dist/  — instant public shareable URL, zero server setup
    • Use for judge demos, team reviews, or quick sharing during development

12. Developer Quick-Start Checklist
Before Writing Code — Get API Keys
    • Datalastic: register at datalastic.com → copy API key to backend .env
    • Anthropic Claude: console.anthropic.com → create API key → backend .env + ai-service .env
    • OpenWeatherMap: openweathermap.org/api → free tier key → backend .env
    • NewsAPI: newsapi.org → register → backend .env
    • Twilio: twilio.com → free trial → SID + token + phone → backend .env
    • n8n: signup at n8n.cloud (free) OR install locally with Docker
    • Firebase: create project in Firebase Studio for auth + optional Firestore

Day 1 — Scaffold
    • Run: git init shipguard && cd shipguard, create folder structure as per Section 6.1
    • Run: npm create vite@latest frontend -- --template react in frontend/
    • Run: npm init -y in backend/, install: express pg ioredis socket.io axios cors dotenv
    • Run in ai-service/: pip install fastapi uvicorn sqlalchemy psycopg2 pandas scikit-learn anthropic
    • Run: docker-compose up -d to start PostgreSQL and Redis
    • Apply database schema: psql < database/migrations/001_init.sql

Day 2 — First Integration
    • Fetch 20 vessels from Datalastic API, insert into PostgreSQL vessels table
    • Build GET /api/vessels endpoint in Express, return JSON array
    • Render vessels on Leaflet map in React with basic blue markers
    • Fetch marine weather for 3 routes from Open-Meteo Marine API, log results

Day 3 — Risk Scoring
    • Implement rule-based risk_score computation in Python (weather + port congestion)
    • Expose POST /risk/score FastAPI endpoint, test with curl
    • Call Python endpoint from Node.js routeController, store result in route_risk table
    • Colour-code vessel map pins: green/yellow/orange/red by risk score
    • Build Alert Dashboard in React — list active HIGH/CRITICAL risk routes

Day 4 — Automation & AI
    • Import n8n workflows 01 and 04 — activate vessel ingest and alert dispatch
    • Verify email alert arrives in inbox when a route's risk score exceeds 60
    • Integrate Claude API: POST prompt with route risk context → receive NL report
    • Render Claude report in AI Briefing Panel in React dashboard

Day 5 — Route Optimiser & Deploy
    • Build AlternativeRoutes component: hardcode 2 alt routes, display comparison table
    • Wire Claude reroute reasoning: pass alternatives + risk scores → display recommendation
    • Polish UI: navbar, sidebar, responsive layout, loading states, error boundaries
    • Deploy: push to GitHub → Vercel auto-deploys frontend, Railway auto-deploys backend
    • Generate Antigravity demo link for judges
    • Record 3-minute demo video: live map → disruption alert → AI report → route suggestion
