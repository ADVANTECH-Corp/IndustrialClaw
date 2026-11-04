---
schema_version: "1.0"
---

# Hugging Face Seven-Day Model Selection Report

## Goal
Generate report labels, categories, notices, and generated descriptions in English. Preserve original-source titles and evidence quotes verbatim.

Select up to 15 public model families from the seven days preceding the start of this run's collection command, with at most three per category. For each selection, provide its purpose, intended audience, selection reasons, original-source evidence, and limitations in English. Generate a responsive report following the frontend-design guidance and the reviewed HF design plan. Produce verifiable HTML and JSON for this run, preventing old files or late writes after interruption from being accepted as success.

## General Setting
- cycle: 1
- retry: 0

## Expected Outputs
- `outputs/hf-weekly/report.html`
- `outputs/hf-weekly/models.json`

## Execution Procedure

## Step 1
- name: Offline output-consistency and termination regression
- prompt: Run the existing offline regression tests to verify that stale runs, mixed outputs, incomplete commits, and late writes after termination of the tests' own subprocesses are rejected. Preserve evidence from this test run.
- execute: `python3 skills/hf-weekly-review/scripts/test_review.py`
- check: `python3 skills/hf-weekly-review/scripts/test_review.py --check`
- on failure: stop
- note: The tests start and terminate only their own test subprocesses. They do not modify platform monitoring or establish that platform stop-task integration has been verified.

## Step 2
- name: Frontend design and compatibility regression
- prompt: First read skills/frontend-design/SKILL.md and skills/frontend-design/DESIGN.md, then check the design renderer's compatibility with the existing data validator, HTML escaping, empty results, and responsive design rules.
- execute: `python3 -m skills.frontend-design.scripts.report test`
- check: `python3 -m skills.frontend-design.scripts.report test-check`
- on failure: stop
- note: Execute every command exactly as written from the Agent workspace. This Task's execute/check commands take precedence over older invocation examples in the Skill documents. Use python3 -m skills.frontend-design.scripts.report; do not replace it with a file path, omit the skills. prefix, or change the working directory. Static and offline checks do not constitute actual browser screenshot acceptance. Do not claim to have viewed mobile or desktop rendering.

## Step 3
- name: Generate the seven-day model report with the frontend design
- prompt: Use frontend-design's HF report renderer while preserving the existing quality filters, family consolidation, and summaries. Generate outputs/hf-weekly/report.html and outputs/hf-weekly/models.json, report progress truthfully, and save intermediate results and run identifiers.
- execute: `python3 -m skills.frontend-design.scripts.report build`
- check: `python3 -m skills.frontend-design.scripts.report verify`
- on failure: stop
- note: Apply the design before the original build publishes outputs and computes hashes; do not modify the HTML afterward. Use the Python standard library and the existing local Gemma model, without adding packages, downloading models, or loading external fonts or CDNs. The script has a 720-second hard limit, with budgets of 240 seconds for collection, 90 seconds for reading model cards, and 300 seconds for summaries. Retry temporary source errors at most three times and respect rate limits. The platform must provide TASK_ID or TASK_ARTIFACT_DIR. Use public data without a token and do not download weights. Exclude local Gemma summaries without original-source evidence. Disclose missing data and incomplete searches; do not pad the selections. Preserve old outputs, but do not accept them as this run's success.

## Final Verification
- execute: `python3 -m skills.frontend-design.scripts.report verify --final`
- success condition: The command exits with code 0. Both the original and design regression tests pass for this run. The current platform identity, run_id, completion receipt, hashes, and run copies agree. The seven-day window, sources, original-source evidence for intended uses, family and category limits, and HTML/JSON content are consistent. The HTML must exactly match the current frontend-design renderer's output when rebuilt from the same JSON. Partial searches or zero selections must be explicitly disclosed. Check actual mobile and desktop browser layouts separately. If no suitable environment is available, explicitly state that they remain unverified; do not present static CSS checks as visual acceptance.
