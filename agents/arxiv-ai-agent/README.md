# arxiv-ai-agent

## Description

Collects up to 30 of the latest arXiv papers across ten AI-related categories, showing titles, original links, and categories in a self-contained HTML report with desktop and mobile browser checks.

## Agent Resume

| Item | Content |
|---|---|
| Object | Collect up to 30 unique papers sorted by submitted date, newest first, generate an HTML report and JSON data, and verify sources and responsive layout. |
| Skills | `arxiv-fulltext-review`: collects title/link/category records with bounded retries and source verification.<br>`frontend-design`: provides the reviewed report renderer and browser-based responsive checks. |
| Task | Latest 30 arXiv Papers: frontend-design Web Report (`latest-arxiv-ai.md`) |
| Prerequisite | Python 3, network access to arXiv, and the `frontend-design` Skill's declared Playwright runtime for browser checks. |
| Input | None; queries the fixed categories listed in [Note](#note). |
| Output | `outputs/arxiv-ai/report.html`<br>`outputs/arxiv-ai/papers.json` |
| Step | 3 Steps, 1 cycle: offline regression tests → collect papers and generate the report → desktop (1440px) and mobile (390px) browser checks. |
| Error Handling | 0 Task retries configured; stops if any Step fails. Collection allows two extra tries per failed request, respects `Retry-After`, and discloses skipped pages and partial results. |
| Verify | Final verification returns exit code `0`: both outputs are non-empty files from the current run; titles, links, categories, sorting, and deduplication match the hashed raw sources; browser evidence and screenshots match the current report hash and run ID. |

## Contents

| File | Description |
|---|---|
| `arxiv-ai-agent.zip` | Agent |
| `latest-arxiv-ai.md` | Task |

## Verified Models

- `ollama/gemma4:26b-a4b-it-Q8_0`

## Note

The fixed categories are `cs.CV`, `cs.GR`, `cs.CL`, `cs.AI`, `cs.CE`, `cs.GT`, `cs.IT`, `cs.LG`, `cs.NE`, and `stat.ML`. Collection requests ten records per page, up to nine pages, within a 600-second collection budget. Each run starts with fresh records and does not mix historical caches.

The report contains only titles, original links, and categories; it does not download PDFs or full text, generate summaries, or make model calls. Fewer than 30 papers, including zero results, may pass verification only when the shortfall is disclosed and the data matches the sources.

Browser checks read the local report without visiting paper links or loading external assets. Screenshots and `browser-check.json` are supporting evidence, not additional Expected Outputs. Automated layout and interaction checks do not establish subjective visual approval; screenshots must be inspected before claiming it.

Report labels and generated descriptions are English. Original-source titles and evidence quotes are preserved verbatim.
