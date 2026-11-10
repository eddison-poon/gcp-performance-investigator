# Standalone HTML RCA report specification — v1.3

## Delivery
After collecting and analyzing logs, write **one self-contained .html file** to `reports/gcp-rca-YYYYMMDD-HHMM-ENV.html` in the current local investigator repository, or another explicitly approved local reports folder. Use local file-writing capability only; **do not change GCP resources or skill/config files**. Do not commit or push the report. If local report writing is blocked, present chat summary and say why.

## Format
- Plain HTML5 with inline CSS; **no external fonts, JavaScript, libraries, CDN, tracking, or network assets**. It must open offline by double-clicking.
- Clean responsive design, high contrast, compact layout; readable on desktop, printable via browser.
- Header: title, environment, investigated time window/timezone, report generation time, Quick/Extended mode.
- Summary cards: final outcome/status, observed E2E duration, slowest measured child operation (or Unknown), completeness/confidence.
- Chronological timeline table: timestamp, microservice, action, duration, status/evidence. Keep to important events; collapse repetitive streaming events into summarized counts.
- Top measured operations table (3 Quick / 5 Extended) excluding parent request time; mark missing timing as Unknown, never 0.
- Analysis: confirmed observations, error chain (if any), major observation gaps with start/end, possible explanations explicitly unconfirmed.
- Conclusion: 1-3 sentences with confidence, at most two recommended next actions.
- Coverage footer: query count, total returned, unique, cap hits/uncovered slices, matching identity confidence, start/end observed.
- For multiple executions: one summary per execution and a short comparison, avoiding huge repeated tables.

## Privacy and correctness
- **Escape all interpolated text as HTML text**, including any event names and service labels; never inject raw log values as markup. Do not embed full prompts, request/response bodies, raw JSON, credentials, user data, or sensitive IDs. Redact identifiers (e.g. short opaque suffix) unless explicitly permitted.
- Do not use misleading stacked bars for overlapping durations. If showing a bar, label parent E2E separately and only plot validated child durations without implying they sum to total.
- Make evidence and uncertainty visible: "Measured", "Observation gap", "Unknown", "Partial coverage".
- Do not fabricate missing timing. Ensure the HTML file exists and opens as valid HTML before reporting success.

## Minimal skeleton
```html
<!doctype html>
<html lang="en">
<head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1"><title>GCP Execution RCA</title>
<style>
body{font:15px/1.5 system-ui,sans-serif;max-width:1100px;margin:auto;padding:24px;color:#18202b;background:#f7f9fc}
header,.panel{background:white;border:1px solid #dfe5ee;border-radius:10px;padding:18px;margin:14px 0}
table{border-collapse:collapse;width:100%}th,td{border-bottom:1px solid #dfe5ee;padding:10px;text-align:left;vertical-align:top}
th{background:#f0f4f9}small,.muted{color:#526074}@media print{body{background:white}.panel,header{break-inside:avoid}}
</style></head>
<body><header><h1>Execution Performance RCA</h1><p>Environment / time window / mode</p></header>
<section class="panel"><h2>Summary</h2><p>Observed outcome, measured duration, confidence</p></section>
<section class="panel"><h2>Execution timeline</h2><table><thead><tr><th>Time</th><th>Service</th><th>Operation</th><th>Duration</th><th>Evidence</th></tr></thead><tbody></tbody></table></section>
<section class="panel"><h2>Top measured operations</h2></section>
<section class="panel"><h2>Analysis &amp; gaps</h2></section>
<section class="panel"><h2>Conclusion</h2></section>
<footer><small>Coverage and evidence limitations</small></footer></body></html>
```
