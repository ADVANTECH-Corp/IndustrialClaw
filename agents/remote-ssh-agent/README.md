# remote-ssh-agent

## Contents

| File | Description |
|---|---|
| `remote-ssh-agent.zip` | Agent |
| `remote-print-hello-world-task.md` | Task |
| `materials.zip` | Materials |

## Agent skills

- `gen-ssh-key`: sets up passwordless SSH access to a remote computer. Use this Skill when SSH remote access is needed so subsequent SSH connections do not require manually entering a password. It generates a fresh Ed25519 SSH key for each Task run, installs its public key using the provided bootstrap credentials, and verifies passwordless SSH access with `BatchMode=yes`.

## Task Goal

Connect to a remote computer using the SSH credentials provided in the materials, then create `hello-world.txt` in the remote user's home directory. Runs 1 cycle with 1 retry configured and stops if either step fails.

### Input

`materials/ssh_password.txt`, containing:

```text
host: <remote host or IP>
username: <remote SSH username>
password: <bootstrap password>
```

### Output

Remote file:

```text
~/hello-world.txt
```

The `gen-ssh-key` Skill also generates a setup report and a current-run identifier:

```text
outputs/ssh/<RUN_ID>/ssh-key-setup-report.md
outputs/ssh/.current-run-id
```

The setup report records the SSH key fingerprint and connection verification result without exposing the password or private key.
