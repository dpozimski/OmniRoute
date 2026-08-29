# Evidence for PR #11945 — continuous call-log export

Artefacts from a live run of the log-export feature against a real BigQuery dataset. This branch
carries only these files and is not part of the pull request diff.

| File | What it is |
| --- | --- |
| `01-destinations-list.png` | The `/dashboard/log-export` page: hourly schedule, cursor high-water mark, and the configured destinations with their per-destination actions. |
| `02-config-form.png` | The Add destination form, scrolled to the payload controls: the `Export prompts and responses` toggle, its guidance, `Max payload bytes per field`, and partition retention. |
| `omniroute-call-logs-sample.csv` | 9 real exported rows pulled straight back out of BigQuery with `SELECT *`, all 47 columns. |

## About the CSV

These are genuine call logs from live Cursor requests through the proxy, exported by the shipped
code and read back from `esky-ets-transaction-ci.omniroute_live_e2e.call_logs_prompts_v2`.

Worth looking at:

- `request_body` and `response_body` carry the prompt and the completion.
- `pipeline_client_request` / `pipeline_provider_request` / `pipeline_provider_response` /
  `pipeline_client_response` carry both sides of the call, as the Logs detail pane shows them.
- Rows written under a `noLog` API key have every payload column empty. The summary still exports.
- `authorization` inside the captured headers reads `[REDACTED]`, which is the existing payload
  protection applied before anything is stored, not something the exporter does afterwards.
- The two oldest rows have payloads but no pipeline columns: they predate enabling pipeline
  capture (`call_log_pipeline_enabled`), which those four columns depend on.

No credential appears in this file. It was scanned for service-account keys, bearer tokens, JWTs,
provider keys and unredacted auth headers before publishing.
