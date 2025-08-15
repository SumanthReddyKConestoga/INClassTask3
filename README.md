INClassTask3 – Monitoring & Logging (Flask + OpenTelemetry + SigNoz)

Author: Sumanth Reddy K
Repo: https://github.com/SumanthReddyKConestoga/INClassTask3

This repo is a compact, production-adjacent lab that instruments a Flask app with OpenTelemetry and ships traces to a local SigNoz stack. It’s opinionated, minimal, and easy to run—so you can spend time analyzing signals, not wiring them.

Stack at a glance

App: Python/Flask (app.py)

Instrumentation: OpenTelemetry SDK + OTLP exporter (gRPC → otel-collector:4317)

Collector: OpenTelemetry Collector (SigNoz distro)

Backend/UI: SigNoz (ClickHouse, Query Service, Frontend)

Containers: Docker Compose

1) Prerequisites

Docker Desktop (or Docker Engine) with docker compose v2

Git

(Optional for local non-Docker run) Python 3.10+ and pip

Verify:

docker --version
docker compose version
git --version

2) Clone the repository
git clone https://github.com/SumanthReddyKConestoga/INClassTask3.git
cd INClassTask3

3) Bring up SigNoz

SigNoz lives under signoz/deploy/docker.

Windows (PowerShell)
cd .\signoz\deploy\docker
docker compose up -d --remove-orphans

Linux/macOS
cd signoz/deploy/docker
docker compose up -d --remove-orphans


Wait ~1–2 minutes for services to settle.
UI: http://localhost:3301

Health check:

docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}" | grep -i signoz

4) Start the instrumented Flask app

From the repo root (same terminal or a new one):

Windows (PowerShell)
cd $PSScriptRoot\..\..\INClassTask3  # if you're still inside signoz/deploy/docker
# or simply:
cd C:\Users\ksuma\Downloads\INClassTask3   # adjust path to your clone
docker compose up -d --build

Linux/macOS
cd ~/path/to/INClassTask3   # adjust path to your clone
docker compose up -d --build


App endpoint: http://localhost:5000
Sample route that emits spans: /rolldice

Smoke test:

curl http://localhost:5000/rolldice


Generate some load (Windows PowerShell):

1..25 | ForEach-Object { curl http://localhost:5000/rolldice > $null }


Or (Linux/macOS):

for i in {1..25}; do curl -s http://localhost:5000/rolldice > /dev/null; done


Now open SigNoz → Traces and filter by your service name (see app.py).

5) Run locally without Docker (optional)
python -m venv .venv
# Windows
.\.venv\Scripts\Activate.ps1
# Linux/macOS
source .venv/bin/activate

pip install -r requirements.txt
python app.py


App: http://localhost:5000
(Ensure your OTLP endpoint in code or env points to localhost:4317 where the collector runs.)

6) Useful commands (Ops short list)

View containers

docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"


Tail app logs

docker compose logs -f


Stop app

docker compose down


Stop SigNoz

cd signoz/deploy/docker
docker compose down


Nuke SigNoz data (irreversible)

cd signoz/deploy/docker
docker compose down -v

7) Configuration surface

docker-compose.yml – app container, port mapping (default 5000:5000)

otel-collector-config.yaml – pipelines, receivers (otlp gRPC on 4317)

app.py – service/resource attributes, tracer provider, span exporter

Common envs you can add to the app service in docker-compose.yml:

environment:
  - OTEL_SERVICE_NAME=info8985-monolith
  - OTEL_TRACES_SAMPLER=parentbased_always_on
  - OTEL_EXPORTER_OTLP_ENDPOINT=http://otel-collector:4317

8) Project structure (high level)
INClassTask3/
├─ .devcontainer/
├─ signoz/
│  └─ deploy/docker/ (SigNoz docker-compose + configs)
├─ app.py
├─ docker-compose.yml           # Flask app compose
├─ otel-collector-config.yaml   # OTEL collector config (referenced by SigNoz)
├─ requirements.txt
├─ up.yml                       # optional automation (if used)
└─ README.md

9) Troubleshooting

SigNoz UI not loading: wait 60–120 seconds; check docker compose ps under signoz/deploy/docker.

No traces appearing: confirm app is hitting /rolldice; exporter endpoint is otel-collector:4317 inside Docker (or localhost:4317 when running app on host).

Port conflicts: ensure 5000 (app), 3301 (SigNoz UI), and 4317 (OTLP gRPC) are free.

Docker warning “version is obsolete”: harmless with compose v2; safe to ignore.

10) License

MIT — see LICENSE.

Credits

Built by Sumanth Reddy K — lean engineering, clear signals, zero fluff.
