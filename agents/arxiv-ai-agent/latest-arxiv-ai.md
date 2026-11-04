---
schema_version: "1.0"
---

# Latest 30 arXiv Papers: frontend-design Web Report

## Goal
Generate report labels, categories, notices, and generated descriptions in English. Preserve original-source titles and evidence quotes verbatim.

Use the existing ten categories and sort by submittedDate, newest first, aiming to collect 30 unique papers. Present only titles, original links, and categories. Generate the report using the official frontend-design guidance and the reviewed design, preserving data authenticity, partial-result disclosures, and source verification. Perform desktop and mobile browser checks.

## General Setting
- cycle: 1
- retry: 0

## Expected Outputs
- outputs/arxiv-ai/report.html
- outputs/arxiv-ai/papers.json

## Execution Procedure
Execute the fixed commands in order. Do not independently rerun paper collection or parsing. Before Step 2, read skills/frontend-design/SKILL.md and skills/frontend-design/DESIGN.md. The generator must directly use that Skill's scripts/design.py, rather than merely mention the Skill name. Request at most nine pages of ten papers each, with a 600-second collection budget, a 20-second timeout per request, and two additional retries on timeout. Respect Retry-After. Use only data from the current run; failed sources may be skipped if explicitly disclosed. Browser access is limited to the local report, with no external assets, installations, or model calls. Screenshots and browser-check.json are supporting evidence, not additional Expected Outputs. Automated browser checks do not establish human approval of subjective visual quality.

## Step 1
- name: Offline regression tests
- prompt: Test retries, deduplication and record limits, failure skipping, partial results, data tampering, isolation from old data, and design escaping.
- execute: python3 -u skills/arxiv-fulltext-review/scripts/latest.py selftest
- check: python3 -u skills/arxiv-fulltext-review/scripts/latest.py selftest --strict
- on_failure: stop

## Step 2
- name: Collect papers and generate the report with frontend-design
- prompt: Set execution_timeout to 900 seconds. First read the frontend-design guidance and DESIGN.md, then use the generator implementing that Skill's design to save outputs/arxiv-ai/report.html and outputs/arxiv-ai/papers.json for this run. Do not download full text or generate summaries.
- execute: python3 -u skills/arxiv-fulltext-review/scripts/latest.py run --out outputs/arxiv-ai
- check: python3 -u skills/arxiv-fulltext-review/scripts/latest.py verify --out outputs/arxiv-ai
- on_failure: stop

## Step 3
- name: Desktop and mobile browser checks
- prompt: Set execution_timeout to 180 seconds. In frontend-design's declared Playwright runtime, verify layouts at 1440px and 390px, title links, filtering and restoration, focus, and horizontal overflow. Save screenshots and evidence of the current report hash in outputs/arxiv-ai. Stop on failure and do not falsely claim visual acceptance.
- execute: python3 skills/frontend-design/scripts/browser_check.py run --out outputs/arxiv-ai
- check: python3 skills/frontend-design/scripts/browser_check.py verify --out outputs/arxiv-ai
- on_failure: stop

## Final Verification
- execute: python3 skills/frontend-design/scripts/browser_check.py final --out outputs/arxiv-ai
- success_condition: The command actually exits with code 0. Both Expected Outputs are non-empty regular files from this run. Raw-source hashes, titles, links, categories, sorting, and deduplication are consistent, with at most 30 papers. Shortfalls and skipped pages are explicitly disclosed; zero papers must not be presented as complete results. Desktop and mobile browser evidence matches the current report hash and run_id, and screenshots exist. The System must still inspect the screenshots before claiming subjective visual approval.
