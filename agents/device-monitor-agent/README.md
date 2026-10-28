# device-monitor-agent

## Description

Monitors the local machine's CPU usage, CPU temperature, and root disk, writing one Markdown report per metric and verifying each report.

## Agent Resume

| Item | Content |
|---|---|
| Object | Sample CPU usage, CPU temperature, and root disk usage / free space five times each per round, write one report per metric, and verify each report. |
| Skills | `device-monitor`: read-only snapshots of CPU, temperature, GPU, memory, disk, and thermal zones, plus repeated sampling into a Markdown report. |
| Task | Monitor Local CPU and Disk (`monitor-local-cpu-task.md`) |
| Prerequisite | None |
| Input | None |
| Output | `outputs/cpu-usage-monitor-report.md`<br>`outputs/cpu-temperature-report.md`<br>`outputs/disk-monitor-report.md` |
| Step | 3 Steps, 2 cycles: CPU usage → CPU temperature → root disk. The second cycle appends to the same reports. |
| Error Handling | 1 retry configured. CPU temperature and disk reports are checked with `verify_report.py`. CPU usage and CPU temperature continue on failure; root disk stops on failure. |
| Verify | `verify_report.py` passes for all three reports (exit code `0`). |

## Contents

| File | Description |
|---|---|
| `device-monitor-agent.zip` | Agent |
| `monitor-local-cpu-task.md` | Task |

## Verified Models

- `ollama/qwen3.5:4b-q4_K_M`
- `ollama/gemma4:26b-a4b-it-Q8_0`

## Note

A value that cannot be read (e.g. CPU temperature on machines without a sensor) is written as `Unknown`, never estimated.
