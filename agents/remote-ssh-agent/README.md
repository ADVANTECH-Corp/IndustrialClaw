# remote-ssh-agent

## Description

Connects to a remote computer over SSH using the credentials provided in the materials, sets up passwordless SSH access, and creates `hello-world.txt` in the remote user's home directory.

> [!IMPORTANT]
> Run IndustrialClaw in **develop-mode** to use this agent. See [Note](#note) for details and security risks.

## Agent Resume

| Item | Content |
|---|---|
| Object | Verify that the agent can reach a remote computer over SSH and run commands on it. |
| Skills | `gen-ssh-key`: sets up passwordless SSH access to a remote computer. Use this Skill when SSH remote access is needed so subsequent SSH connections do not require manually entering a password. It generates a fresh Ed25519 SSH key for each Task run, installs its public key using the provided bootstrap credentials, and verifies passwordless SSH access with `BatchMode=yes`. |
| Task | Remote Agent Test (`remote-print-hello-world-task.md`) |
| Prerequisite | IndustrialClaw in **develop-mode**, or the Skill runtime on the host network (see [Note](#note)). Input data is uploaded as `materials.zip`. |
| Input | `materials/ssh_password.txt`:<br>`host: <remote host or IP>`<br>`username: <remote SSH username>`<br>`password: <bootstrap password>` |
| Output | Remote file `~/hello-world.txt`.<br>`gen-ssh-key` also writes `outputs/ssh/<RUN_ID>/ssh-key-setup-report.md` and `outputs/ssh/.current-run-id`. The setup report records the SSH key fingerprint and connection verification result without exposing the password or private key. |
| Step | 3 Steps, 1 cycle: connect to the remote computer → create `~/hello-world.txt` → check that it exists. |
| Error Handling | 1 retry configured; stops if any Step fails. |
| Verify | All Steps complete and `~/hello-world.txt` exists on the remote computer. |

## Contents

| File | Description |
|---|---|
| `remote-ssh-agent.zip` | Agent |
| `remote-print-hello-world-task.md` | Task |
| `materials.zip` | Materials |

## Verified Models

- `openai/gpt-5.6-sol`

## Note

> [!WARNING]
> This agent only works when one of the following is true:
>
> - IndustrialClaw runs in **develop-mode**, or
> - `docker-compose-skill.yml` puts the Skill runtime on the same network as the host (`network_mode: host`), so it can reach external computers and external computers can connect back to it.
>
> **Security risk:** Both options remove the Skill runtime's network isolation. Other computers on the network can reach it, and it holds SSH credentials that can run commands on the remote host. Only use this on a trusted network, use a low-privilege SSH account, and switch back to runtime mode / remove `network_mode: host` when you are done.
