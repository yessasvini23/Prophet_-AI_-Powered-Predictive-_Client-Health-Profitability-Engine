# Prophet – AI-Powered Predictive Client Health & Profitability Engine

![Status](https://img.shields.io/badge/status-alpha-orange) ![Build](https://img.shields.io/badge/build-aws%20amplify%20%7C%20docker-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Made%20with](https://img.shields.io/badge/made%20with-AWS%20SageMaker%20%26%20Bedrock-232F3E)

Prophet is a predictive analytics **AI agent** for Managed Service Providers (MSPs) that turns raw operational data (tickets, time entries, contracts, costs) into **forward‑looking insights**: churn risk, true client profitability, and resource/budget forecasts—with **plain‑English, LLM‑generated recommendations**.

---

## 🌐 Live Demo

👉 [Prophet Insight Engine – Live Application](https://prophet-insight-engine.lovable.app/)

📺 [SuperHack 2025 Idea Submission Deck (PDF)](./Superhack-2025-IDEA-SUBMISSION.pdf)

---

## ✨ Why Prophet

* **From hindsight → foresight**: predicts churn and cost spikes **weeks in advance**.
* **True profitability**: calculates **net profit per client** by blending MRR with time, licenses, hardware, and alert volume.
* **Actionable**: Bedrock‑powered insights translate numbers into **decisions you can take today**.
* **Built for MSP realities**: integrates with SuperOps and common PSA/RMM data structures.

---

## 🧩 Problem → Solution

**MSPs operate on thin margins and live/die by retention.** They’re reactive, lack line‑of‑sight into true client profitability, and struggle to forecast workload/budget.

**Prophet** solves this with:

1. **Predictive Client Health Score (0‑100)** and churn likelihood.
2. **Profit & loss by client**, exposing VIP vs. Vampire accounts.
3. **AI‑generated playbooks** to fix root causes and upsell intelligently.
4. **Workforce & budget forecasting** for proactive planning.

---

## 🏗️ Architecture Overview

```
        +------------------+            +-------------------+          +----------------+
        |  SuperOps API    |  ETL via   |   AWS Glue Jobs   |  S3 Raw  | Amazon S3 Data |
        |  (tickets, time, |--------->  |  (batch & stream) |--------> |   Lake (raw)   |
        |  contracts, etc.)|            +-------------------+          +----------------+
                 |                                                           |
                 |                                                           v
                 |                                                +---------------------+
                 |                                                |  AWS Glue (curate)  |
                 |                                                +---------------------+
                 |                                                           |
                 v                                                           v
         +-------------+                                             +----------------+
         | DynamoDB    |  <---- real‑time aggregates / features ---- |  Feature Store |
         +-------------+                                             +----------------+
                 |                                                           |
                 v                                                           v
          +----------------+       train/deploy        +----------------------------+
          |  SageMaker     |  -----------------------> |  Churn & Profit Models     |
          |  Pipelines     |                          |  (endpoints)               |
          +----------------+                          +----------------------------+
                 |                                                           |
                 v                                                           v
          +----------------+                                      +---------------------+
          | Amazon Bedrock |  insights & NLG prompts  ---------> |  Insights Service    |
          +----------------+                                      |  (Node/Express API) |
                 ^                                                           |
                 |                                                           v
          +----------------+                                        +------------------+
          |  Cognito (Auth)|<---------------------------------------|  React Frontend  |
          +----------------+     JWT                                +------------------+
```

---

## 🔑 Core Features

1. **Predictive Client Health Score** (0‑100) with risk tiers and early‑warning flags.
2. **True Profitability Analysis** (MRR vs. all costs: time, licenses, hardware, alerts) → VIP / Vampire segmentation.
3. **AI‑Generated Actionable Insights** via Bedrock (e.g., “Schedule QBR; propose managed OS upgrade worth \$5k”).
4. **Resource & Budget Forecasting** for technician workload and spend.
5. **What‑If Scenarios** (raise MRR 5%, deprecate legacy asset, add automation) and **expected impact**.

---

## 🛠️ Tech Stack

* **Frontend**: React, Recharts/D3, Tailwind, Vite
* **Backend**: Node.js, Express.js, TypeScript
* **Data & Storage**: AWS Glue, S3 (raw/curated), DynamoDB (real‑time metrics), Feature Store
* **ML**: SageMaker Pipelines (XGBoost/LightGBM for churn; regression for effort/spend; SHAP for explainability)
* **GenAI**: Amazon Bedrock (Claude family) for NLG and action recommendations
* **Auth**: AWS Cognito (Hosted UI + JWT)
* **Infra**: Docker, AWS Amplify (frontend) / ECS or EC2 (API), CloudWatch, IAM
* **Integrations**: SuperOps API (PSA/RMM) for tickets, time, assets, contracts

---

## 📦 Monorepo Structure

```
prophet/
├── apps/
│   ├── web/                  # React app
│   └── api/                  # Node/Express API
├── data/                     # sample datasets & schemas
├── infra/                    # IaC (Terraform/CDK)
├── ml/
│   ├── pipelines/            # SageMaker pipeline definitions
│   ├── training/             # training scripts
│   └── inference/            # model serving handlers
├── packages/
│   ├── shared/               # shared types, utils
│   └── prompts/              # Bedrock prompt templates
└── tools/                    # scripts, Makefile tasks
```

---

## 🚀 Quickstart (Local Dev)

### Prereqs

* Node.js ≥ 18, Docker Desktop, AWS CLI v2, Terraform or AWS CDK, Python 3.10+
* Access to **SuperOps API** (or load sample data) and an AWS account

### 1) Clone & bootstrap

```bash
git clone https://github.com/<your-org>/prophet.git
cd prophet
npm run bootstrap            # installs deps via npm/pnpm workspaces
cp .env.example .env
```

### 2) Start services (Dev)

```bash
# API
cd apps/api
npm run dev

# Web
cd ../web
npm run dev
```

### 3) Docker (optional)

```bash
docker compose up --build
```

---

## 🔒 Environment Variables (.env.example)

```ini
# Global
NODE_ENV=development
REGION=us-east-1

# Auth
COGNITO_USER_POOL_ID=
COGNITO_CLIENT_ID=
COGNITO_DOMAIN=

# Data
S3_BUCKET_RAW=prophet-raw
S3_BUCKET_CURATED=prophet-curated
DDB_TABLE_METRICS=prophet-metrics

# SuperOps
SUPEROPS_BASE_URL=https://api.superops.ai
SUPEROPS_API_KEY=

# ML
SAGEMAKER_EXECUTION_ROLE_ARN=
CHURN_ENDPOINT_NAME=prophet-churn-endpoint
PROFIT_ENDPOINT_NAME=prophet-profit-endpoint

# Bedrock
BEDROCK_REGION=us-east-1
BEDROCK_MODEL_ID=anthropic.claude-3-sonnet-20240229-v1:0
```

---

## 🧪 Sample Data (for demos)

* `data/sample_tickets.csv`: ticket\_id, client\_id, created\_at, closed\_at, priority, sentiment, tags
* `data/time_entries.csv`: entry\_id, client\_id, tech\_id, ticket\_id, minutes, rate
* `data/contracts.csv`: client\_id, mrr, start\_date, renewal\_date, sla\_tier
* `data/assets.csv`: asset\_id, client\_id, type, lifecycle\_stage, risk\_score

Use `tools/load_sample_data.ts` to seed S3/DynamoDB.

---

## 🔗 API (Express)

### Auth

JWT via **Cognito**. Include `Authorization: Bearer <token>`.

### Endpoints

```http
GET   /health                                  # liveness
GET   /clients                                  # list with health/profitability summary
GET   /clients/:id                               # detail with drivers & insights
POST  /ingest/superops/sync                      # run ETL for a time window
POST  /whatif                                   # scenario simulation
```

### Example: SuperOps ingest (pseudo-code)

```ts
// apps/api/src/integrations/superops.ts
const fetchTickets = async (from: string, to: string) => {
  const res = await fetch(`${SUPEROPS_BASE_URL}/tickets?from=${from}&to=${to}`, {
    headers: { 'x-api-key': process.env.SUPEROPS_API_KEY! }
  });
  const items = await res.json();
  await s3.putObject({ Bucket: RAW_BUCKET, Key: `tickets/${from}_${to}.json`, Body: JSON.stringify(items) })
}
```

---

## 🤖 ML: Models & Features

* **Churn Classifier**: Gradient‑boosted trees (XGBoost/LightGBM). Labels from historical churn/renewal.

  * **Features**: ticket volume trend, mean/95p resolution time, SLA breach rate, sentiment trend, asset risk, QBR cadence, renewal proximity, change backlog.
  * **Explainability**: SHAP values returned per prediction; exposed in UI as top drivers.
* **Profitability**: Net profit regression = `MRR – (time_cost + license_cost + hardware_depr + alert_cost)`.
* **Forecasts**: ARIMA/Prophet/GBTs for ticket inflow & effort per client.
* **Pipelines**: SageMaker Pipelines for build/train/eval/deploy with model registry & CI hooks.

### Example: Endpoint contract

```json
{
  "client_id": "abc-123",
  "health_score": 62,
  "churn_risk_pct": 0.31,
  "top_drivers": [
    {"feature": "sla_breach_rate", "shap": 0.14},
    {"feature": "sentiment_trend", "shap": 0.09}
  ]
}
```

---

## 🧠 Bedrock: Insight Prompts

```
SYSTEM: You are a pragmatic MSP growth advisor. Be specific, concise, and actionable.
USER: Given the metrics below, explain the likely causes of churn and 3 actions with expected impact.
DATA: {health_score, churn_risk_pct, top_drivers, profit_delta_qoq, asset_risks, renewal_date}
STYLE: Bulleted, include estimated $ impact and effort rating (Low/Med/High).
```

---

## 🖥️ Frontend (React)

* **Views**: Portfolio (all clients), Client Detail, What‑If Studio, Forecasts, Settings.
* **UI**: Recharts for health gauges, waterfall for profit, stacked bars for effort forecast, SHAP beeswarm for drivers.
* **Auth**: Cognito Hosted UI, token persisted in memory + refresh.

---

## 📈 Evaluation & KPIs

* **Churn AUC** ≥ 0.80, **Recall\@Top10% Risk** ≥ 0.60
* **Forecast MAPE** ≤ 15%
* **Adoption**: % clients with QBR scheduled after alert; **Revenue Lift** from upsells/renegotiations
* **Profit Accuracy**: within ±5% of finance reconciliation

---

## 🔐 Security & Compliance

* Principle of least privilege (IAM). Encrypted at rest (S3, DDB) & in transit (TLS 1.2+).
* PII minimization & data retention policies. Audit via CloudTrail.
* Secrets via AWS Secrets Manager / SSM Parameter Store. No secrets in code.

---

## 📦 Deployment

### One‑click (Amplify + ECS)

```bash
make infra.apply        # terraform/cdk to provision S3, DDB, roles, pipelines
make build              # web + api
make deploy             # push containers, create endpoints, connect Amplify
```

### Terraform snippet (infra/terraform/main.tf)

```hcl
resource "aws_s3_bucket" "raw" { bucket = var.s3_raw }
resource "aws_s3_bucket" "curated" { bucket = var.s3_curated }
resource "aws_dynamodb_table" "metrics" {
  name         = var.ddb_metrics
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "client_id"
  attribute { name = "client_id" type = "S" }
}
```

---

## 🗺️ Roadmap

* [ ] Real‑time streaming via Kinesis Firehose
* [ ] Auto‑generated QBR decks with charts
* [ ] Playbook automation (create tickets/changes directly)
* [ ] Multi‑PSA connectors (ConnectWise, Autotask)
* [ ] Cost anomaly detection & alerting

---

## 🤝 Contributing

PRs welcome! Please open an issue to discuss major changes. Run `npm run test` before submitting.

---

## 📜 License

MIT © Your Organization

---

## 🙌 Acknowledgments

* SuperOps for the API surface and MSP ecosystem insights.
* AWS for SageMaker, Bedrock, and generous free‑tier resources.

