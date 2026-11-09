# GCP Project Mapping Template

Copy this file to `config/gcp-config.local.md` on your work machine and fill in actual IDs. Never commit the local file.

| Environment | GCP Project ID |
| --- | --- |
| DEV | <DEV_PROJECT_ID> |
| SIT | <SIT_PROJECT_ID> |
| UAT | <UAT_PROJECT_ID> |
| PPD | <PPD_PROJECT_ID> |
| PROD | <PROD_PROJECT_ID> |

Rules: choose exactly one environment per investigation; use explicit `--project`; never infer missing IDs.
