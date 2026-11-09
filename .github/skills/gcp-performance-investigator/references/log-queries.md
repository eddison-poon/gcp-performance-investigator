# Quick Cloud Logging queries (PowerShell / gcloud)

All queries must use an explicit project, bounded UTC timestamps, and a limit. Field names below are examples: use only paths observed in the target environment. Prefer PowerShell single-quoted filter strings; replace placeholders before execution.

## Preflight (read-only)
```powershell
gcloud --version
gcloud auth list
gcloud config get-value project
```
Default project is informational only.

## Optional schema probe (only if mapping unknown)
```powershell
gcloud logging read 'timestamp>="START_UTC" AND timestamp<"END_UTC"' --project="PROJECT_ID" --limit=10 --format=json
```

## Primary exact correlation (choose a validated key)
```powershell
gcloud logging read 'timestamp>="START_UTC" AND timestamp<"END_UTC" AND jsonPayload.correlation_id="CORRELATION_ID"' --project="PROJECT_ID" --limit=200 --format=json --order=asc
```
Alternative verified fields: `jsonPayload.query_name`, `jsonPayload.execution_id`, `jsonPayload.request_id`, `trace`. For multiple IDs, use verified fields and conjunction only if they coexist in relevant logs; otherwise retrieve one key and validate secondary IDs in results. Avoid an AND filter that silently excludes services lacking one field.

## One targeted follow-up (Option B)
Use a narrow service/namespace/cluster filter plus exact validated secondary identifier **or** the unexplained interval, never a broad project-wide dump. Example template:
```powershell
gcloud logging read 'timestamp>="GAP_START_UTC" AND timestamp<"GAP_END_UTC" AND resource.type="k8s_container" AND resource.labels.container_name="CONFIRMED_SERVICE"' --project="PROJECT_ID" --limit=200 --format=json --order=asc
```
Before attributing any result, validate matching execution/trace/call identity; unrelated events in the same interval are not evidence.

## Short-window incident mode
Use verified numeric HTTP status fields (e.g. `httpRequest.status` or `jsonPayload.http_status`) or confirmed error event names. Keep the same query budget and report sampling.

## Rules adapted from Agent Hub observability
- Use `correlation_id` + `query_name` + `execution_id` where available to distinguish execution attempts, not as interchangeable universal IDs.
- Controller lifecycle may be in `jsonPayload.msg`; executor boundary may use `event_name`, `http_path=/execute`, `http_status`, `latency_ms`. Validate schema first.
- For tool/MCP pairs, require scoped tool-call/RPC IDs and compatible pod/connection identity.
- Controller failure may take precedence over later successful wrapper HTTP status.
- Do not add overlapping child timings, average p95s, or treat log gaps as service durations.
- Do not claim absence of errors/events when queries hit limits.
