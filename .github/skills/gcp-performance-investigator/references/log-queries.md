# GCP Cloud Logging query patterns — v1.3

Use actual discovered fields; these are **templates**, not guaranteed schemas. All filters require explicit project and UTC bounds. PowerShell examples:

```powershell
gcloud --version
gcloud auth list
gcloud config get-value project
gcloud logging read 'timestamp>="START_UTC" AND timestamp<"END_UTC" AND jsonPayload.correlation_id="CORRELATION_ID"' --project="PROJECT_ID" --limit=200 --format=json --order=asc
```

Use `jsonPayload.query_name`, `jsonPayload.execution_id`, `jsonPayload.request_id` or `trace` only when validated. AND filters can accidentally omit services that lack a field. Use the most selective confirmed key and verify adjacent IDs after retrieval.

## Adaptive slicing
1. Convert the bounded user window to UTC once.
2. Partition into consecutive 2-minute intervals with no overlap or holes: `timestamp >= START` and `timestamp < END`.
3. Query each interval with same validated project and key filter, `--limit=200 --format=json --order=asc`.
4. For each result of **200**, bisect interval and query both children; do not use the capped parent as complete coverage.
5. Count **all** returned records toward 5,000, including parent queries and duplicates; count all logging reads toward 30. Stop on budget exhaustion.
6. Deduplicate final records; prefer `logName`, resource identity, `insertId`; do not assume `insertId` globally unique. If unavailable, use an explicitly documented stable composite and mark uncertainty.
7. If a very narrow interval still saturates, report incomplete coverage; do not loop indefinitely.
8. Show total query count, total records returned, unique records, uncovered/saturated slices, and whether start/end lifecycle events were observed. Under-cap slices mean only the query filter was not capped, **not** proof all execution logs exist.

## Option B targeted follow-up
Only after primary or adaptive coverage, one targeted read per execution if necessary, within budgets:
```powershell
gcloud logging read 'timestamp>="GAP_START_UTC" AND timestamp<"GAP_END_UTC" AND resource.type="k8s_container" AND resource.labels.container_name="CONFIRMED_SERVICE"' --project="PROJECT_ID" --limit=200 --format=json --order=asc
```
Validate identity independently for records missing original correlation key. Do not equate proximity with causation.

## Semantics
- Controller `jsonPayload.msg`, executor `event_name`, `http_path`, `http_status`, `latency_ms` are candidates only.
- Use `execution_id` to distinguish attempts; for tool/MCP pairs validate call ID plus connection/pod scope.
- Controller failures can precede later successful HTTP wrappers.
- Do not sum overlapping child operations, or call log silence measured service duration.
- Avoid sensitive raw payloads in outputs and saved reports.
