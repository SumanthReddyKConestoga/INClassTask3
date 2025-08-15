# INClassTask3 – Monitoring & Logging (Flask + OpenTelemetry + SigNoz)

Author: **Sumanth Reddy K**  
Repo: **https://github.com/SumanthReddyKConestoga/INClassTask3**

This repo is a compact, production-adjacent lab that instruments a Flask app with **OpenTelemetry** and ships traces to a local **SigNoz** stack. It’s opinionated, minimal, and easy to run—so you can spend time analyzing signals, not wiring them.

## Stack at a glance

- **App**: Python/Flask (`app.py`)
- **Instrumentation**: OpenTelemetry SDK + OTLP exporter (gRPC → `otel-collector:4317`)
- **Collector**: OpenTelemetry Collector (SigNoz distro)
- **Backend/UI**: SigNoz (ClickHouse, Query Service, Frontend)
- **Containers**: Docker Compose

## 1) Prerequisites

- Docker Desktop (or Docker Engine) with **docker compose v2**
- Git
- (Optional for local non-Docker run) Python 3.10+ and `pip`

Verify:
```bash
docker --version
docker compose version
git --version
2) Clone the repository
bash
Copy
Edit
git clone https://github.com/SumanthReddyKConestoga/INClassTask3.git
cd INClassTask3
3) Bring up SigNoz
SigNoz lives under signoz/deploy/docker.

Windows (PowerShell)
powershell
Copy
Edit
cd .\signoz\deploy\docker
docker compose up -d --remove-orphans
Linux/macOS
bash
Copy
Edit
cd signoz/deploy/docker
docker compose up -d --remove-orphans
UI: http://localhost:3301

4) Start the instrumented Flask app
From the repo root:

Windows (PowerShell)
powershell
Copy
Edit
cd C:\Users\ksuma\Downloads\INClassTask3   # adjust path
docker compose up -d --build
Linux/macOS
bash
Copy
Edit
cd ~/path/to/INClassTask3
docker compose up -d --build
Smoke test:

bash
Copy
Edit
curl http://localhost:5000/rolldice
Generate load:

powershell
Copy
Edit
1..25 | % { curl http://localhost:5000/rolldice > $null }
5) Run locally without Docker (optional)
bash
Copy
Edit
python -m venv .venv
# Windows: .\.venv\Scripts\Activate.ps1
# Linux/mac: source .venv/bin/activate
pip install -r requirements.txt
python app.py
6) Useful commands
bash
Copy
Edit
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker compose logs -f
docker compose down
cd signoz/deploy/docker && docker compose down
cd signoz/deploy/docker && docker compose down -v   # remove data
7) Config
docker-compose.yml (app)

otel-collector-config.yaml (collector)

app.py (resource attrs/exporter)

8) Structure
arduino
Copy
Edit
INClassTask3/
├─ signoz/deploy/docker/
├─ app.py
├─ docker-compose.yml
├─ otel-collector-config.yaml
├─ requirements.txt
└─ README.md
9) Troubleshooting
Wait 60–120s after starting SigNoz.

Ensure ports 5000/3301/4317 are free.

Check exporter endpoint matches where the collector runs.

10) License
MIT — see LICENSE.

Built by Sumanth Reddy K.
'@; Set-Content -Path README.md -Value $c -Encoding UTF8"

sql
Copy
Edit

Then commit and push:
```powershell
git add README.md
git commit -m "Add comprehensive README with setup and runbook"
git push
