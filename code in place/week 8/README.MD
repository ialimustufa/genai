# Week 8: Monitoring, Security & Compliance

## Day 1: Defending Against Prompt Injection

1. **Concepts & Reading**
   - Review prompt-injection attacks: https://owasp.org/www-community/attacks/Prompt_Injection  
   - Understand input sanitization and use of delimiters.

2. **Implementation**
   - In `sanitize.py`:
     ```python
     import re

     def sanitize_prompt(text: str) -> str:
         # Remove dangerous characters and enforce delimiter
         clean = re.sub(r'["'`;]', '', text)
         return f"###BEGIN###\n{clean}\n###END###"
     ```

3. **Test Cases**
   ```python
   prompts = ["Give me a joke; DROP TABLE users;", "Tell me a story"]
   for p in prompts:
       print(sanitize_prompt(p))
   ```

---

## Day 2: Metrics & Observability with Prometheus & Grafana

1. **Install Client Libraries**
   ```bash
   pip install prometheus-client
   ```

2. **Instrument a FastAPI Service**
   In `metrics.py`:
   ```python
   from fastapi import FastAPI, Request, Response
   from prometheus_client import Counter, generate_latest, CONTENT_TYPE_LATEST
   from prometheus_client import Histogram

   app = FastAPI()
   REQUEST_COUNT = Counter('app_requests_total', 'Total HTTP requests', ['method', 'endpoint'])
   REQUEST_LATENCY = Histogram('app_request_latency_seconds', 'Request latency', ['endpoint'])

   @app.middleware("http")
   async def metrics_middleware(request: Request, call_next):
       REQUEST_COUNT.labels(request.method, request.url.path).inc()
       with REQUEST_LATENCY.labels(request.url.path).time():
           response = await call_next(request)
       return response

   @app.get("/metrics")
   def metrics():
       return Response(generate_latest(), media_type=CONTENT_TYPE_LATEST)
   ```

3. **Visualize in Grafana**
   - Add Prometheus data source pointing to your `/metrics` endpoint.
   - Build dashboards for request count and latency.

---

## Day 3: GDPR & Data Privacy

1. **Principles of GDPR**
   - Read key articles: data minimization, purpose limitation, user consent.

2. **Pseudonymization Example**
   In `anonymize.py`:
   ```python
   import uuid

   def pseudonymize(record: dict) -> dict:
       record['user_id'] = str(uuid.uuid4())
       if 'email' in record:
           record.pop('email')
       return record
   ```

3. **Consent Tracking**
   - Store user consent flags in a database table with timestamps.

---

## Day 4: EU AI Act & Compliance Checklist

1. **Overview**
   - Review high-level requirements of EU AI Act: risk categories, documentation.

2. **Documentation Template**
   Create `compliance.md` with sections:
   - Intended purpose
   - Data sources and quality
   - Risk assessment
   - Monitoring plan
   - Incident response

---

## Day 5: Logging, Alerting & Incident Response

1. **Structured Logging**
   ```python
   import logging

   logging.basicConfig(
       level=logging.INFO,
       format='%(asctime)s %(levelname)s %(message)s'
   )
   logger = logging.getLogger(__name__)

   logger.info("User login attempt", extra={"user_id": "1234"})
   ```

2. **Alerting with Alertmanager**
   Create `alert_rules.yml`:
   ```yaml
   groups:
   - name: app_alerts
     rules:
     - alert: HighErrorRate
       expr: increase(app_requests_total{status_code=~"5.."}[5m]) > 5
       for: 2m
       labels:
         severity: critical
       annotations:
         summary: "High error rate detected"
   ```

---

## Day 6: Authentication & Rate Limiting

1. **API Key Middleware**
   In `auth.py`:
   ```python
   from fastapi import Request, HTTPException

   async def verify_api_key(request: Request):
       api_key = request.headers.get("x-api-key")
       if api_key != "expected_key":
           raise HTTPException(status_code=401, detail="Invalid API Key")
   ```

2. **Rate Limiting**
   ```bash
   pip install slowapi
   ```
   In `rate_limit.py`:
   ```python
   from slowapi import Limiter
   from slowapi.util import get_remote_address
   from fastapi import FastAPI

   limiter = Limiter(key_func=get_remote_address)
   app = FastAPI()
   app.state.limiter = limiter

   @app.get("/items")
   @limiter.limit("5/minute")
   async def read_items():
       return {"items": []}
   ```

---

## Day 7: Mini-Project — Secure Monitored RAG Service

**Project Description**  
Enhance your Week 4 RAG application by:
- Adding prompt sanitization (`sanitize.py`)  
- Instrumenting Prometheus metrics and Grafana dashboards (`metrics.py`)  
- Enforcing API key auth and rate limits (`auth.py`, `rate_limit.py`)  
- Implementing GDPR pseudonymization (`anonymize.py`)  
- Providing an incident response plan in `compliance.md`

**Deliverables**  
- Updated `app.py`, `sanitize.py`, `metrics.py`, `auth.py`, `anonymize.py`  
- `compliance.md`  
- **README.md** documenting security & monitoring setup  

---

```bash
pip install -r requirements.txt
uvicorn app:app --reload
```  
