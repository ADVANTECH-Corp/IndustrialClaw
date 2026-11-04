# hugging-face-agent

## Description

Selects up to 15 public Hugging Face model families from the past seven days and produces a responsive HTML report with English descriptions, intended audiences, selection reasons, source evidence, and limitations.

## Agent Resume

| Item | Content |
|---|---|
| Object | Select up to 15 model families, with at most three per category, within the seven days preceding the collection command's start time; generate and verify an HTML report and JSON data for the current run. |
| Skills | `hf-weekly-review`: collects public model metadata and model cards, selects model families, and generates evidence-based English summaries with run-bound verification.<br>`frontend-design`: applies the reviewed HF report design before outputs are published and hashed. |
| Task | Hugging Face Seven-Day Model Selection Report (`latest-hf-models.md`) |
| Prerequisite | Python 3 on a platform supporting `fcntl`, network access to Hugging Face, and the configured local Ollama model `gemma4:26b-a4b-it-Q8_0` at `http://host.docker.internal:23951/api/chat`. The platform must provide `TASK_ID` or `TASK_ARTIFACT_DIR`. |
| Input | None; uses public Hugging Face API data and model cards without a token. |
| Output | `outputs/hf-weekly/report.html`<br>`outputs/hf-weekly/models.json` |
| Step | 3 Steps, 1 cycle: offline output-consistency and termination regression → frontend design and compatibility regression → collect models, summarize, and generate the report. |
| Error Handling | 0 Task retries configured; stops if any Step fails. Temporary source errors allow up to three retries, respecting `Retry-After`. Interrupted or timed-out builds do not publish outputs; old outputs are retained but cannot count as the current run's success. |
| Verify | Final verification returns exit code `0`: regression evidence, platform identity, run ID, completion receipt, hashes, and run copies agree; the seven-day window, source evidence, family/category limits, and HTML/JSON content are consistent. Re-rendering the same JSON with the current renderer must reproduce the HTML exactly. |

## Contents

| File | Description |
|---|---|
| `hugging-face-agent.zip` | Agent |
| `latest-hf-models.md` | Task |

## Verified Models

- `ollama/gemma4:26b-a4b-it-Q8_0`

## Note

The report is a curated selection, not a performance ranking. Summaries use the configured local Gemma model; entries without original-source evidence are excluded. Missing information, incomplete searches, and zero selections are disclosed rather than padded with unsupported entries. Model weights are not downloaded.

The build has a 720-second hard limit, with budgets of 240 seconds for collection, 90 seconds for reading model cards, and 300 seconds for summaries. It uses the Python standard library without installing packages or loading external fonts or CDNs.

Run the commands in `latest-hf-models.md` exactly from the Agent workspace. Its `python3 -m skills.frontend-design.scripts.report` commands take precedence over older path-based examples in the packaged Skill documents. Install all packaged scripts together when updating so the English renderer, summary prompt, validator, and regression fixtures remain consistent.

Offline design checks do not establish desktop or mobile browser acceptance. Actual rendering, keyboard interaction, and visual accessibility require separate browser checks. The termination regression tests only their own subprocesses and do not establish platform `stop-task` integration.

Report labels and generated descriptions are English. Original-source titles and evidence quotes are preserved verbatim.
