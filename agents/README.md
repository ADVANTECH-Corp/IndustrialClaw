# Agents

The Agent packages that can be imported as they are. Import and run steps are in the [top-level README](../README.md).

| Agent | What it does | Task it ships with | Runtime |
|---|---|---|---|
| [`device-monitor-agent`](device-monitor-agent/) | Monitors local CPU usage, CPU temperature, and root disk | `monitor-local-cpu.md` | A few minutes (2 rounds x 3 Steps x 5 samples) |
| [`hello-world-agent`](hello-world-agent/) | Runs one script and prints a success message | `hello-world-task.md` | Under a minute |

## Which one to run first

**On a freshly installed device, run `hello-world-agent` first.** Three files, one Step: enough to prove the whole path works — import, create the Task, run it, produce the file, pass Final Verification. Once that works, move on to the real one.

## How the two differ

| | `hello-world-agent` | `device-monitor-agent` |
|---|---|---|
| Files | 3 | 16 |
| Skill | `hello-world` | `device-monitor` |
| Permissions | none | `env`, `file_read`, `file_write`, `shell` |
| Steps | 1 | 3 |
| `cycle` / `retry` | 1 / 0 | 2 / 1 |
| Expected Outputs | 1 | 3 |

`device-monitor-agent` reads `/proc` and `/sys` and calls `nvidia-smi` / `rocm-smi`, so the Security Scanner rates it higher than `hello-world-agent`. A device with a stricter `SECURITY_SCAN_LEVEL` may refuse it — that is the gate working, not a fault.

## Naming

Folder name, ZIP filename, and Agent name are the same string. **The Agent's name is the ZIP's filename** — importing `device-monitor-agent.zip` gives you an Agent called `device-monitor-agent` whose workspace is `workspace-device-monitor-agent/`.

To rename an Agent, rename the ZIP. Nothing inside the package changes.
