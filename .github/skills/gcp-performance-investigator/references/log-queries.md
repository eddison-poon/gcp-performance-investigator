# Cloud Logging query patterns (PowerShell / gcloud)

Always replace placeholders, explicitly set `--project`, and bound every investigation by timestamp. Cloud Logging field paths are examples; verify them from discovered logs before relying on them.

## Preflight
```powershell
gcloud --version
gcloud auth list
gcloud config get-value project
```
The configured default project is informational only; never use it as the investigation target.

## Small schema sample
```powershell
gcloud logging read 'timestamp>="2026-11-09T06:00:00Z" AND timestamp<="2026-11-09T06:30:00Z"' --project="PROJECT_ID" --limit=10 --format=json
```

## Request ID (only if this field is confirmed)
```powershell
gcloud logging read 'timestamp>="2026-11-09T06:00:00Z" AND timestamp<="2026-11-09T06:30:00Z" AND jsonPayload.requestId="REQUEST_ID"' --project="PROJECT_ID" --limit=200 --format=json
```

## HTTP 5xx (only if httpRequest.status exists)
```powershell
gcloud logging read 'timestamp>="2026-11-09T06:00:00Z" AND timestamp<="2026-11-09T06:30:00Z" AND httpRequest.status>=500 AND httpRequest.status<600' --project="PROJECT_ID" --limit=200 --format=json
```

## Slow HTTP requests (only if httpRequest.latency exists)
```powershell
gcloud logging read 'timestamp>="2026-11-09T06:00:00Z" AND timestamp<="2026-11-09T06:30:00Z" AND httpRequest.latency>"60s"' --project="PROJECT_ID" --limit=100 --format=json
```

## Rules
- Convert user-local timestamps to RFC3339 UTC correctly, including date rollover and daylight saving.
- If field paths differ, construct a new query from observed schema. Avoid blind broad full-text scans.
- Do not silently treat `trace`, `spanId`, `requestId` or session ID as interchangeable.
- Search logs in relevant services using validated IDs; distinguish client-visible status from internal service status.
- Keep query counts, window and truncation limits in the report.
