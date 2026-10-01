# device-monitor-agent

## Contents

| File | Description |
|---|---|
| `device-monitor-agent.zip` | Agent |
| `monitor-local-cpu-task.md` | Task |

## Agent skills

- `device-monitor`: read-only snapshots of CPU, temperature, GPU, memory, disk, and thermal zones, plus repeated sampling into a Markdown report

## Task Goal

Sample CPU usage, CPU temperature, and root disk five times each, write one report per metric, and verify each report. Runs 2 rounds; the second round appends to the same reports.

### Input

None

### Output

```text
outputs/cpu-usage-monitor-report.md
outputs/cpu-temperature-report.md
outputs/disk-monitor-report.md
```

A value that cannot be read is written as `Unknown`.
