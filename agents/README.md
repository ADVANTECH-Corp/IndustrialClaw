# Agents

Each folder here is one Agent:

| File | Description |
|---|---|
| `<agent-name>.zip` | Agent |
| `*-task.md` | Task |
| `README.md` | What the Agent does, and what goes in and comes out |

## Use an Agent

You'll need an **Administrator** account on the Dashboard. To download the files, see [Download](../README.md#download).

1. **Import the Agent**: go to Agents → Agent Management → **Import Package**, pick the `.zip`, then press **Create Agent**
2. **Create the Task**: go to Task Management → **Create Task**, pick the Agent and its `.md` file, then press **Create**
3. **Run it**: find the Task in **Available Tasks** and press **START**

Results show up in the Agent's `outputs/` folder.

## Build your own Agent

The easiest way to start is to copy [`hello-world-agent`](hello-world-agent/) and change it. In this example, the new Agent is called `my-agent` and its Skill is called `my-skill`.

### 1. Unzip hello-world

```bash
mkdir my-agent && cd my-agent
unzip ../hello-world-agent/hello-world-agent.zip
```

You get three files:

```text
TOOLS.md                              lists the Agent's Skills
skills/hello-world/SKILL.md           tells the Agent what the Skill does and how to run it
skills/hello-world/scripts/hello.sh   the script that does the work
```

### 2. Make it your Skill

```bash
mv skills/hello-world skills/my-skill
mv skills/my-skill/scripts/hello.sh skills/my-skill/scripts/my-script.sh
```

- **`my-script.sh`**: write what your Skill should do
- **`SKILL.md`**: describe the Skill, the command that runs it, and what a successful result looks like
- **`TOOLS.md`**: change the Skill link to `[my-skill](skills/my-skill/SKILL.md)`

The Skill names in `TOOLS.md` must match the folders in `skills/` **exactly**. This is the most common reason an import fails.

### 3. Zip it

Run this from inside `my-agent/`, so `TOOLS.md` and `skills/` sit at the top of the ZIP:

```bash
zip -r ../my-agent.zip TOOLS.md skills
```

- The ZIP filename becomes the Agent name: lowercase letters, digits, and hyphens only
- Don't include a `tasks/` folder
- Size limit: 20 MB

### 4. Write the Task

Copy `hello-world-agent/hello-world-task.md` to `my-task.md`, then change:

| Section | What to write |
|---|---|
| `# Title` / `## Goal` | What this Task is for |
| `## General Setting` | `cycle`: how many rounds to run. `retry`: how many times to retry a failed Step |
| `## Expected Outputs` | The files the Task produces. A bare filename lands in `outputs/` |
| `## Step N` | `execute`: the command. `check`: a command that must return `0`. `on failure`: `stop` or `continue` |
| `## Final Verification` | A command that decides whether the whole Task succeeded |

Keep the `schema_version: "1.0"` header at the top. Size limit: 64 KB.

### 5. Try it

Import `my-agent.zip` and run `my-task.md` using the steps in [Use an Agent](#use-an-agent).

### 6. Add it here

Create a folder named after the Agent, and put the ZIP, the Task, and a `README.md` in it. Use the [hello-world README](hello-world-agent/README.md) as the template.
