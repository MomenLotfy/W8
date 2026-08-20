# W8 — M13 Observability, Logging, Monitoring & Incident Response

Status: Implementation Specification
Milestone: M13

---

## 1. Objective

Build complete observability before production.

W8 must be able to answer:

- Is the system healthy?
- What is failing?
- Who is affected?
- When did it start?
- What changed?
- How severe is it?
- What request caused it?
- Can it recover automatically?

---

## 2. Observability Pillars

W8 uses:

1. Logs
2. Metrics
3. Traces
4. Audit events
5. Alerts

---

## 3. Correlation ID

Every backend request must receive:

`X-Request-ID`

If supplied by trusted infrastructure, preserve it.

Otherwise generate one.

The ID must appear in:

- Logs
- Error responses where appropriate
- Traces
- Audit records

---

## 4. Structured Logging

Use JSON structured logs.

Example:

```json
{
  "timestamp": "...",
  "level": "ERROR",
  "service": "w8-api",
  "environment": "production",
  "request_id": "...",
  "route": "/v1/workouts",
  "method": "GET",
  "status": 500,
  "duration_ms": 142,
  "error_code": "WORKOUT_QUERY_FAILED"
}
```

Never log:

- passwords
- access tokens
- refresh tokens
- payment credentials
- full private conversations
- sensitive personal data unnecessarily

---

## 5. Log Levels

### DEBUG
Development only.

### INFO
Normal business/system events.

### WARNING
Unexpected but recoverable conditions.

### ERROR
Operation failed.

### CRITICAL
System-level incident.

Production DEBUG logging must be disabled by default.

---

## 6. Metrics

Required API metrics:

- request count
- request latency
- error rate
- 4xx rate
- 5xx rate
- active requests

Database:

- connection pool usage
- query latency
- connection failures

Redis:

- memory
- hit/miss
- connection failures

Workers:

- queue depth
- task success
- task failure
- task latency

AI:

- requests
- latency
- failures
- token usage
- estimated cost

Notifications:

- sent
- delivered
- failed

Payments:

- webhook received
- webhook failed
- payment state transitions

---

## 7. Golden Signals

Monitor:

### Latency
p50 p95 p99

### Traffic
Requests per second.

### Errors
5xx percentage.

### Saturation
CPU Memory Database Queue Connection pool

---

## 8. Distributed Tracing

Use OpenTelemetry-compatible tracing.

Trace:

```text
Mobile
 ↓
API
 ↓
Service
 ↓
Database / Redis / External API
```

External services must have bounded timeout values.

---

## 9. Health Endpoints

### Liveness

`GET /health/live`

Indicates process is alive.

Must not depend on external services.

### Readiness

`GET /health/ready`

Checks required dependencies.

Example:

- database
- Redis if required
- required service dependencies

---

## 10. Error Taxonomy

All API errors must have stable codes.

Example:

```json
{
  "error": {
    "code": "AUTH_TOKEN_EXPIRED",
    "message": "Authentication required."
  }
}
```

Do not expose stack traces to clients.

---

## 11. Monitoring Dashboards

Minimum dashboards:

### API
- Requests
- Latency
- Errors

### Database
- Connections
- Query latency
- Slow queries

### Infrastructure
- CPU
- Memory
- Disk
- Network

### Workers
- Queue depth
- Failures

### AI
- Requests
- Cost
- Latency
- Safety events

### Payments
- Webhook health

---

## 12. Alerting

Critical alerts:

- API unavailable
- 5xx spike
- Database unavailable
- Queue backlog
- High memory
- High latency
- Authentication anomaly
- Payment webhook failures
- Notification delivery failure
- AI provider outage

Alerts must have thresholds and severity.

Avoid noisy alerts.

---

## 13. Incident Severity

### SEV1
Major outage/security incident.

### SEV2
Major feature/system degradation.

### SEV3
Limited functionality issue.

### SEV4
Minor issue.

---

## 14. Incident Response

Process:

```text
Detect
↓
Alert
↓
Acknowledge
↓
Assess
↓
Mitigate
↓
Recover
↓
Verify
↓
Postmortem
```

---

## 15. Postmortem

Every SEV1/SEV2 incident requires:

- Summary
- Timeline
- Impact
- Root cause
- Contributing factors
- Detection
- Mitigation
- Corrective actions
- Preventive actions

No blame-oriented language.

---

## 16. Audit vs Logs

Do not treat them as the same.

Logs:

operational debugging

Audit:

security/business accountability

Audit records must be durable.

---

## 17. Privacy

Observability must follow data minimization.

Sensitive user content should be:

- redacted
- hashed where appropriate
- omitted
- sampled carefully

AI conversations must not automatically become application logs.

---

## 18. Retention

Retention must be configurable by environment and data type.

Example:

- application logs: limited operational retention
- audit logs: longer retention
- metrics: longer aggregation retention
- traces: sampled retention

Do not hardcode retention assumptions into business logic.

---

## 19. Testing

Test:

- log generation
- request IDs
- health endpoints
- metrics
- trace propagation
- error codes
- alert conditions

---

## 20. Definition of Done

M13 is complete when:

- Structured logs exist
- Correlation IDs exist
- Metrics exist
- Tracing exists
- Health endpoints exist
- Dashboards exist
- Alerts exist
- Sensitive data is redacted
- Audit is separate from operational logging
- Incident procedure documented
- Monitoring works in staging
