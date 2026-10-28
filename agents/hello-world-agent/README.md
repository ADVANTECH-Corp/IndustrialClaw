# hello-world-agent

## Description

Minimal example agent. Confirms Industrial Claw can invoke an installed Skill and run its bundled script.

## Agent Resume

| Item | Content |
|---|---|
| Object | Verify that Industrial Claw can invoke the installed `hello-world` Skill and execute its bundled script. |
| Skills | `hello-world`: runs `hello.sh`, which prints one line. |
| Task | Hello World Skill Execution Test (`hello-world-task.md`) |
| Prerequisite | None |
| Input | None |
| Output | `outputs/hello-world-result.txt`, containing `Industrial Claw skill executed successfully.` |
| Step | 1 Step, 1 cycle: run `hello.sh` and write its output to `hello-world-result.txt`. |
| Error Handling | Checks the file contains the success message; no retry, stops on failure. |
| Verify | `hello-world-result.txt` exists and contains the success message. |

## Contents

| File | Description |
|---|---|
| `hello-world-agent.zip` | Agent |
| `hello-world-task.md` | Task |

## Verified Models

- `ollama/qwen3.5:4b-q4_K_M`
- `ollama/gemma4:26b-a4b-it-Q8_0`

## Note

The Task only creates `hello-world-result.txt` in the Task workspace and does not modify system settings.
