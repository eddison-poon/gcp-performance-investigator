# Compact RCA contract — v1.3

Use the same findings in chat and HTML. Keep text short.

1. **Scope and coverage:** environment, local/UTC window, redacted execution identifier, Quick/Extended mode, queries, returned and unique records, capped/uncovered intervals, observed start/end.
2. **Execution summary:** observed outcome, measured E2E/handler duration and confidence; distinguish accepted HTTP 202 from final success.
3. **Timeline:** chronological time, service, operation, measured duration or Unknown, status/evidence.
4. **Slow operations:** top three (Quick) or top five (Extended) **measured child** operations. Show parent E2E separately. Never add overlapping durations.
5. **Latency attribution:** strongest supported bottleneck; if internal work cannot be measured, say "Unattributed interval", not a confirmed slow microservice. State observation gap boundaries and length.
6. **Error chain:** originating failure -> retries -> terminal status, if supported.
7. **Option B:** whether one targeted follow-up was used, its scope and result.
8. **Conclusion:** one or two evidence-based sentences, confidence, and up to two next actions.

Generate standalone HTML following [html-report.md](html-report.md) by default. No raw logs, secrets or full identifiers.
