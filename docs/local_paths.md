# Local paths to set before running

The pipeline was written against one machine and still carries absolute paths.
They were left exactly as they are so that the committed script matches the script
that produced the current results. Edit these seven lines for your own machine
before sourcing the file.

All line numbers refer to `pipeline/p4ha1_ecm_pipeline_v4.R`.

| Line | Variable | Current value | What it needs to point at |
|---|---|---|---|
| 81 | `DOWNLOAD_DIR` | `/Users/mahditajbakhsh/Downloads` | folder holding the 23 `*-SeuratObj.rds` files |
| 82 | `OUT_ROOT` | `/Users/mahditajbakhsh/p4ha1_ecm_cohort_v3` | output folder, created if missing |
| 83 | `MANIFEST_CSV` | `/Users/mahditajbakhsh/Downloads/syn61518654.csv` | Synapse manifest, only used in `"manifest"` mode; set to `""` to skip |
| 5005 | `SC_SIG_CSV` | `/Users/mahditajbakhsh/Downloads/estimate_audit/ESTIMATE_signatures_used.csv` | ESTIMATE signature table, section 20 |
| 5367 | `AGG_DIR` | `/Users/mahditajbakhsh/p4ha1_ecm_cohort_v3/aggregate` | the `aggregate` subfolder of `OUT_ROOT`, section 21 |
| 5556 | `AGG_DIR` | `/Users/mahditajbakhsh/p4ha1_ecm_cohort_v3/aggregate` | same folder again, section 22 |
| 6075 | `UPR_DIR` | `/Users/mahditajbakhsh/Downloads/upr_sets` | folder of MSigDB UPR gene set TSVs, section 24 |

Note that `AGG_DIR` is hardcoded twice and must be kept consistent with
`OUT_ROOT`. If you change `OUT_ROOT`, change lines 5367 and 5556 to match.

## Possible cleanup, not yet applied

All seven could be derived from a single project root, for example read from an
environment variable, so the script runs unchanged on any machine. That change was
not made here because it edits the analysis script, and the current committed
version is the one that produced the results already circulated. Worth doing as a
separate, tested commit.
