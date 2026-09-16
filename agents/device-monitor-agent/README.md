# device-monitor-agent

Monitors local hardware: CPU usage, CPU temperature, GPU, memory, disk, and thermal zones. Every script is **read-only**, uses the Python standard library, and changes no system configuration.

## Contents

| File | Notes |
|---|---|
| `device-monitor-agent.zip` | The Agent package (16 files, about 24 KB) |
| `monitor-local-cpu.md` | The Task definition |

The package holds a single Skill, `device-monitor`, with nine scripts:

| Script | What it does |
|---|---|
| `monitoring_loop.py` | Samples repeatedly and writes a Markdown report |
| `verify_report.py` | Decides whether a report holds up |
| `log_round.py` | Prints the newest sample into the Task log |
| `cpu_probe.py` / `thermal_probe.py` / `gpu_probe.py` / `memory_probe.py` / `disk_probe.py` | Single-metric snapshots |
| `device_health_probe.py` | Full health check |

## Permissions it needs

| Permission | Used for |
|---|---|
| `env` | Reads `TASK_ID` and nothing else |
| `file_read` | `/proc`, `/sys`, and the report it wrote earlier |
| `file_write` | The requested report path only |
| `shell` | `nvidia-smi` / `rocm-smi`, fixed argument lists |

Because of these, the Security Scanner rates this package higher than `hello-world-agent`. A device with a stricter `SECURITY_SCAN_LEVEL` may refuse it — that is the gate working, not a fault.

## Using it

1. **Import** `device-monitor-agent.zip` (Agents → Agent Management → Import Package)
2. **Create a Task**, picking this Agent and `monitor-local-cpu.md`
3. **START**

The full walkthrough is in the [top-level README](../../README.md).

## What the Task does

| | |
|---|---|
| Goal | Each round samples CPU usage, CPU temperature, and root disk five times, writes one report each, then lets a verification script decide whether the round holds |
| `cycle` / `retry` | 2 / 1 |
| Steps | 3 |
| Runtime | A few minutes |

| Step | Measures | On failure | Has a `check` |
|---|---|---|---|
| 1 | CPU usage | `continue` | no |
| 2 | CPU temperature | `continue` | yes |
| 3 | Root disk | `stop` | yes |

Every Step runs the same command shape:

```bash
python3 skills/device-monitor/scripts/monitoring_loop.py 5 "<report name>" --aspect cpu|temp|disk
```

## Outputs

```text
outputs/cpu-usage-monitor-report.md
outputs/cpu-temperature-report.md
outputs/disk-monitor-report.md
```

The declarations carry no directory, so the platform resolves each to `outputs/<name>` and rewrites the same names in every Step's `execute` and `check` and in the Final Verification.

**A value that cannot be read is written as `Unknown`, never filled in with an estimate.** CPU temperature reads `Unknown` on a machine with no thermal sensor, or in a container that cannot see `/sys/class/thermal`. That is correct behavior, not a failure.

## How the two rounds accumulate

`cycle: 2` means the whole Execution Procedure runs twice, each round in its own fresh session.

Reports accumulate rather than overwrite. A report carries `**Task:** <TASK_ID>` at the top; when the second round's `TASK_ID` matches, its samples are appended to the existing table. The total count and the time span always cover the whole table, so `verify_report.py` agrees with what the report says.

A different Task, a different `--aspect`, or no `TASK_ID` starts a fresh report — which is also how a previous Task's leftover report gets cleared.

The report is deleted before sampling starts, so a run that dies partway leaves no file at all. The platform then reports the Expected Output as missing instead of accepting a stale one.

## How success is decided

Final Verification runs `verify_report.py` against all three reports in a fresh session. Every one must exit `0`.

## When it does not run

| Symptom | Usually means |
|---|---|
| `RISK_BLOCKED` at import | The device's `SECURITY_SCAN_LEVEL` is stricter than this package's risk level |
| CPU temperature column is all `Unknown` | No thermal sensor, or a container that cannot see `/sys/class/thermal`. **Not a fault** |
| Step 2's `check` fails though Step 1 passed | The report was produced but does not hold up; read `verify_report.py`'s output |
| Step 3 fails and the whole Task stops | Step 3 is `on failure: stop`, unlike the first two |
| The second round's report has only 5 rows | `TASK_ID` did not match, so the report was started fresh |

## What else it can do

`TOOLS.md` declares a task type mapping. This Task uses only the first row:

| Task type | Purpose |
|---|---|
| `monitoring` | Repeated CPU / temperature / disk sampling with a report |
| `device_health_check` | Full CPU, GPU, memory, disk, and thermal snapshot |
| `cpu_status` / `gpu_status` / `memory_status` / `disk_status` / `thermal_check` | Individual snapshots |

To use the others, write your own Task Markdown and upload it through Create Task. The package does not change.
