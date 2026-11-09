# Quick Execution Performance Report (v1.2)

Keep the report short: one summary and one timeline per execution, with a shared brief comparison if multiple executions were requested.

## Scope
Environment | Local and UTC window | Correlation tuple(s) | Query count, caps and completeness

## Execution summary
| Execution / attempt | Outcome | E2E duration (evidence) | Slowest supported operation | Confidence |
|---|---|---|---|---|

## Execution flow
| Time | Microservice | What happened | Measured duration | Status / evidence |
|---|---|---|---|---|

## Bottleneck and explanation
- **Top measured bottleneck:** service, operation, duration and exact evidence; if unknown, say so.
- **Why slow / failed:** supported reason, or "not established".
- **Unattributed interval:** timestamp range and size, explicitly an observation gap, not service runtime.
- **Error chain:** source error → retries → final status, only if evidenced.

## Option B follow-up
State whether the one extra query was used, what it targeted, what it found, and why investigation stopped.

## Next actions
At most two concrete actions, only when warranted. No generic lengthy recommendations.

## Safety and evidence
Use sanitized field names, timestamps, counts and query scope; no raw payloads or secrets. Flag truncation, sampling, ambiguous IDs, and missing lifecycle events.
