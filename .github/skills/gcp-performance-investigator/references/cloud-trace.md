# Cloud Trace read-only discovery (Windows / PowerShell)

## Preconditions
- Follow company policy before sending production trace metadata to Copilot.
- Use only an explicitly mapped GCP project and already-authenticated gcloud identity.
- Do not enable APIs, change IAM, change gcloud configuration, install SDK components, or create tokens/credentials.
- Never execute a Trace command guessed from memory: verify CLI help first.

## Step 1: Check available commands
```powershell
gcloud --version
gcloud help trace
```
If the installed CLI exposes a read-only trace retrieval/listing command, inspect its `--help` and run only with explicit project and a known exact trace ID. Some SDK installations do not offer trace retrieval via `gcloud trace`. Do not confuse trace export or configuration commands with read-only retrieval.

## Step 2: Trace availability and retrieval
If CLI does not support read-only retrieval, report `CLI retrieval unsupported`, **not** `Cloud Trace disabled`.

Where company-approved tooling already provides read-only Trace API access, the Cloud Trace v1 REST retrieval endpoint is:
`GET https://cloudtrace.googleapis.com/v1/projects/PROJECT_ID/traces/TRACE_ID`
and list endpoint:
`GET https://cloudtrace.googleapis.com/v1/projects/PROJECT_ID/traces?filter=...&startTime=...&endTime=...`.

Cloud Trace v2 stores spans and uses a different API model; do not assume v1 endpoints cover all instrumentation. Do not invoke REST endpoints unless the user's corporate-approved read-only API access method is already available; do not mint access tokens or add tools to bypass restrictions. If none is available, ask user for an approved access method or a sanitized trace export.

Use a trace ID validated as a GCP Cloud Trace ID. An application `jsonPayload.trace_id` may use a different format/namespace. Never search by a partial or guessed identifier.

## Step 3: Interpret outcomes
- `FOUND`: matching spans retrieved, with timestamps and service identity.
- `NO_MATCH`: successful authorized query but no matching trace (does not prove tracing is disabled).
- `PERMISSION_DENIED`: access refused.
- `CLI_UNSUPPORTED`: SDK does not provide read-only trace retrieval.
- `UNKNOWN`: no approved retrieval method or ambiguous API response.

## Step 4: Attribution
- Calculate duration from span start/end, retain parent-child relationships and overlap.
- Attribute duration to service/operation only when span identity supports it.
- Do not sum nested spans or claim uninstrumented time is idle time.
- If trace unavailable, continue Logging-only RCA with explicit evidence gaps.
