# G-Solution-Smart-Supply-Chains

# ShipGuard — Resilient Logistics & Dynamic Supply Chain Optimization
## Technical Project Documentation & Repository README

[cite_start]This comprehensive documentation covers the architecture, intelligence models, and deployment strategies for **ShipGuard**, a real-time maritime intelligence platform[cite: 5, 10].

---

## 1. Executive Summary
[cite_start]ShipGuard is designed to solve the reactive nature of global maritime logistics[cite: 10, 17]. [cite_start]Instead of discovering disruptions after delivery timelines are compromised, the system provides a continuous, proactive monitoring layer for mid-sized operators[cite: 11, 18].

* [cite_start]**Project Name**: ShipGuard — Resilient Logistics & Dynamic Supply Chain Optimization[cite: 14].
* [cite_start]**Domain Focus**: Maritime / Ship Transport — Global cargo vessel route intelligence[cite: 14].
* [cite_start]**Core Value**: Detect disruptions before they cascade; recommend reroutes before delays materialize[cite: 14].
* [cite_start]**Market Context**: 90%+ of global trade moves by sea, yet disruptions are often identified only after they occur[cite: 8, 12].

---

## 2. System Architecture
[cite_start]The platform utilizes a **Four-Layer Architecture** integrated via **n8n** automation[cite: 27, 42].

### [cite_start]2.1 Layered Breakdown [cite: 28]
| Layer | Components & Responsibility |
| :--- | :--- |
| **Layer 1: Ingestion** | **n8n** workflows poll AIS APIs, Open-Meteo Marine weather, and NewsAPI every 5 minutes. |
| **Layer 2: Intelligence** | **Python FastAPI** service handles ML risk scoring and **Claude API** for natural language briefings. |
| **Layer 3: API Gateway** | **Node.js + Express** serves the React frontend and handles real-time updates via **Socket.io**. |
| **Layer 4: Presentation** | **React + Vite** dashboard featuring interactive Leaflet maps and risk score panels. |

---

## 3. Risk Scoring & Optimization Logic
### [cite_start]3.1 Composite Risk Formula [cite: 77]
The Route Risk Score is a weighted composite (0–100) based on five sub-scores:
* [cite_start]**35% Weather Risk**: Storm severity at route coordinates[cite: 77, 79].
* [cite_start]**25% Port Congestion**: Origin and destination congestion combined[cite: 77, 79].
* [cite_start]**20% Vessel Anomaly**: Speed deviations and off-route heading flags[cite: 77, 79].
* [cite_start]**12% News Sentiment**: Geopolitical and maritime news impact scores[cite: 77, 79].
* [cite_start]**08% Historical Risk**: Baseline disruption rates for the specific corridor[cite: 77, 79].

### [cite_start]3.2 Optimization Flow [cite: 81]
When a score crosses **60 (HIGH)**, the system:
1.  [cite_start]Queries viable alternative routes from a pre-computed graph[cite: 81, 82].
2.  [cite_start]Calculates ETA and cost deltas (fuel/fees) for alternatives[cite: 84, 85].
3.  [cite_start]Uses **Claude API** to reason through the options and recommend the optimal decision[cite: 86].

---

## [cite_start]4. Technical Stack [cite: 34]
### [cite_start]4.1 Frontend (React) [cite: 36]
* **Vite**: Build tooling.
* **Tailwind CSS**: Styling.
* **Leaflet.js**: Interactive world map with vessel markers and weather overlays.
* **Zustand**: Global state management.

### [cite_start]4.2 Backend & AI [cite: 38, 40]
* **Node.js**: Primary REST API and Socket.io server.
* **FastAPI (Python)**: ML service hosting **scikit-learn** models.
* **PostgreSQL**: Primary relational storage for vessels, alerts, and routes.
* **Anthropic Claude API**: Powering the disruption briefings and chatbot.

---

## [cite_start]5. Development Roadmap [cite: 88]
### [cite_start]Phase 1: MVP (Weeks 1–2) [cite: 89, 90]
* **Week 1**: Scaffold environment, integrate Datalastic AIS API, and render live vessels on a map.
* **Week 2**: Implement rule-based risk engine, n8n automation, and Claude AI reports.

### [cite_start]Phase 2: ML & Rerouting (Weeks 3–4) [cite: 91]
* [cite_start]Train **Random Forest** classifier on historical datasets[cite: 94].
* [cite_start]Develop the route optimizer engine and alternative route comparison panels[cite: 98, 100].

---

## [cite_start]6. Project Structure [cite: 63, 64]
```text
shipguard/
├── frontend/             # React + Vite SPA (Components for Map, Dashboard, Alerts)
├── backend/              # Node.js Express (Routes for vessels, alerts, and AI)
├── ai-service/           # Python FastAPI (ML scoring and route optimization)
├── n8n-workflows/        # Automated ingestion and alert JSON files
├── database/             # PostgreSQL migrations and seed data
└── notebooks/            # Google Colab ML development (EDA and Training)
```

---

## 7. Configuration & Deployment
### [cite_start]7.1 Key Environment Variables [cite: 119, 121]
* `DATABASE_URL`: PostgreSQL connection string.
* `DATALASTIC_API_KEY`: AIS data access.
* `ANTHROPIC_API_KEY`: Claude intelligence features.
* `NEWSAPI_KEY`: Real-time news ingestion.

### [cite_start]7.2 Deployment Strategy [cite: 46, 135]
* **Frontend**: Hosted on **Vercel** with auto-deploy from GitHub.
* **Backend/AI**: Dockerized and deployed on **Railway.app**.
* **Automation**: **n8n.cloud** or self-hosted Docker on Railway.
* [cite_start]**Demo**: **Antigravity** for instant shareable URLs during hackathons[cite: 149].
