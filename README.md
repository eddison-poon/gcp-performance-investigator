# GCP Performance Investigator

Lightweight, read-only GitHub Copilot agent + skill for investigating Agent Hub performance and HTTP errors using GCP Cloud Logging via `gcloud`.

## Setup (Windows / VS Code)
1. Clone this private repository and add its folder to your VS Code workspace.
2. Copy `config/gcp-config.example.md` to `config/gcp-config.local.md`; replace placeholders with DEV/SIT/UAT/PPD/PROD project IDs. The local config is gitignored.
3. Ensure `gcloud` is installed, authenticated through company-approved processes, and has permission to read Cloud Logging.
4. In VS Code Copilot Chat Agent Mode, select `gcp-investigator`. If VS Code does not discover it in a multi-root workspace, open this repository as the active folder and check custom agent availability.

## Example prompts
- "Investigate request ID abc123 in UAT between 14:00 and 14:30 HKT. Reconstruct the timeline and identify supported bottlenecks."
- "Investigate HTTP 500 and 504 errors in PPD between 14:00 and 15:00 HKT; identify representative requests and produce an RCA."

## Design
- Each environment has a separate GCP project ID; all log reads use explicit `--project`.
- Request ID correlation is available end-to-end; log schema is discovered on first use.
- Logs-only reconstruction is not distributed tracing. Unknown durations and root causes are explicitly marked.
- No deployment, IAM change, Jira update, script execution or resource mutation is in scope.
- Follow company policies for processing sensitive production logs with AI.

## v1.1 Cloud Trace discovery
The investigator can now check Cloud Trace command availability and, where supported and approved, retrieve traces read-only. It never enables APIs or changes IAM. See `.github/skills/gcp-performance-investigator/references/cloud-trace.md`. If no approved retrieval method is available, the result is **unknown/unsupported**, not proof tracing is disabled.
