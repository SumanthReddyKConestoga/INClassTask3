````markdown
# INClassTask3 — Monitoring & Logging (Flask + OpenTelemetry + SigNoz)

**Author:** Sumanth Reddy K  
**Repo:** https://github.com/SumanthReddyKConestoga/INClassTask3

Instrumented Flask microservice that exports traces to **SigNoz** via **OpenTelemetry**. Run everything with Docker Compose, generate traffic, and inspect traces in the SigNoz UI.

---

## ✅ Prerequisites

- Docker Desktop / Docker Engine with **docker compose v2**
- Git
- (Optional) Python 3.10+ if you want to run the app without Docker

Verify:
```bash
docker --version
docker compose version
git --version
````

---

## 🚀 Quick Start (All Docker)

### 1) Clone

```bash
git clone https://github.com/SumanthReddyKConestoga/INClassTask3.git
cd INClassTask3
```

### 2) Start SigNoz stack

```bash
cd signoz/deploy/docker
docker compose up -d --remove-orphans
```

* UI: `http://localhost:3301`
* Give it 60–120 seconds to warm up.

### 3) Start the Flask app (from repo root)

```bash
cd ../../..
docker compose up -d --build
```

* App: `http://localhost:5000`
* Sample endpoint (emits spans): `/rolldice`

### 4) Generate traffic

**Linux/macOS**

```bash
for i in {1..25}; do curl -s http://localhost:5000/rolldice > /dev/null; done
```

**Windows PowerShell**

```powershell
1..25 | % { curl http://localhost:5000/rolldice > $null }
```

### 5) Observe traces

Open **SigNoz → Traces** and filter by your service (as set in `app.py`).

---

## 🧰 Useful Commands

**Show containers**

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

**Tail app logs**

```bash
docker compose logs -f
```

**Stop app**

```bash
docker compose down
```

**Stop SigNoz**

```bash
cd signoz/deploy/docker
docker compose down
```

**Full reset (remove SigNoz data)**

```bash
cd signoz/deploy/docker
docker compose down -v
```

---

## 🏗️ Project Structure

```
INClassTask3/
├─ .devcontainer/
├─ signoz/
│  └─ deploy/docker/           # SigNoz docker-compose + configs
├─ app.py                      # Flask app with OTEL instrumentation
├─ docker-compose.yml          # App compose (port 5000)
├─ otel-collector-config.yaml  # OTEL collector config
├─ requirements.txt
├─ up.yml                      # optional helper
└─ README.md
```

---

## ⚙️ Configuration Notes

* App uses OTLP gRPC → `otel-collector:4317` (container-to-container).
* To tweak sampling, service name, or endpoint, set env vars in `docker-compose.yml`:

```yaml
environment:
  - OTEL_SERVICE_NAME=info8985-monolith
  - OTEL_TRACES_SAMPLER=parentbased_always_on
  - OTEL_EXPORTER_OTLP_ENDPOINT=http://otel-collector:4317
```

---

## 🩺 Troubleshooting

* **SigNoz UI empty:** Wait up to 2 minutes after startup; then refresh.
* **No traces:** Hit `/rolldice` a few times; confirm exporter endpoint matches where the collector runs.
* **Port conflicts:** Ensure `5000` (app), `3301` (SigNoz UI), and `4317` (OTLP gRPC) are free.
* **Compose warning “version is obsolete”:** Harmless on compose v2.

---

## 🧪 Run Locally Without Docker (Optional)

```bash
python -m venv .venv
# Windows: .\.venv\Scripts\Activate.ps1
# Linux/macOS: source .venv/bin/activate

pip install -r requirements.txt
python app.py  # http://localhost:5000
```

> Ensure the exporter endpoint in code/env points to `http://localhost:4317` if the collector is running on your host.

---

## 📜 License

MIT — see `LICENSE`.

---

```
```
