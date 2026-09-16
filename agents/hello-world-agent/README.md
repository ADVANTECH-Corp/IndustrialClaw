# hello-world-agent

The smallest Industrial Claw Agent there is — three files. It confirms that a device can import a package, create a Task, invoke a Skill, and produce a file.

**On a freshly installed device, run this one first.**

## Contents

| File | Notes |
|---|---|
| `hello-world-agent.zip` | The Agent package (3 files, under 2 KB) |
| `hello-world-task.md` | The Task definition |

The package holds a single Skill:

```text
TOOLS.md
skills/hello-world/SKILL.md
skills/hello-world/scripts/hello.sh
```

`hello.sh` prints one line. It changes no system configuration, leaves no background process, and needs no permissions.

## Using it

1. **Import** `hello-world-agent.zip` (Agents → Agent Management → Import Package)
2. **Create a Task**, picking this Agent and `hello-world-task.md`
3. **START**

The full screen-by-screen walkthrough is in the [top-level README](../../README.md).

## What the Task does

| | |
|---|---|
| Goal | Confirm Industrial Claw can invoke an installed Skill and run its script |
| `cycle` / `retry` | 1 / 0 |
| Steps | 1 |
| Runtime | Under a minute |

**Step 1 — Execute hello world skill**

```bash
bash skills/hello-world/scripts/hello.sh > hello-world-result.txt
```

`on failure: stop` — it stops rather than retrying.

## Output

```text
outputs/hello-world-result.txt
```

It should contain:

```text
Industrial Claw skill executed successfully.
```

The declaration says `hello-world-result.txt` with no directory, so the platform resolves it to `outputs/hello-world-result.txt` and rewrites the same name in the Step's `execute`, its `check`, and the Final Verification.

## How success is decided

Final Verification runs in a fresh session:

```bash
test -s hello-world-result.txt && grep -q "Industrial Claw skill executed successfully." hello-world-result.txt
```

Exit code `0` is the only pass. Only after that does the platform check the Expected Output exists.

## When it does not run

| Symptom | Usually means |
|---|---|
| Refused at import | The ZIP wraps its contents in another folder, or contains `tasks/` |
| The Task will not create | Wrong Agent selected, or the `.md` was edited and no longer matches schema 1.0 |
| Step 1 fails | `hello.sh` is not executable, or the workspace is not writable |
| Step passes but Final Verification fails | The output file is empty — `test -s` rejects a 0-byte file |
