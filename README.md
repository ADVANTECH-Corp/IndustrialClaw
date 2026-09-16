# IC Agents

Agent packages ready to import into an Industrial Claw device. Each Agent ships with a runnable Task, so it works the moment it is imported.

## What is here

Every folder holds the same three things:

| File | Purpose |
|---|---|
| `<agent-name>.zip` | The Agent package. **The filename is the Agent's name** |
| `*.md` | The Task definition. Created after the Agent is imported |
| `README.md` | Notes for that Agent |

## How to use it

Three steps, all in the Dashboard, all requiring **Administrator**.

### 1. Import the Agent

Agents → Agent Management → **Import Package**, and pick `<agent-name>.zip`.

The Security Scanner then decides whether the package gets in:

- All **weak** → passes straight through
- Any **medium** → you must confirm that scan result before it is allowed
- Any **high** → blocked

Confirm, then press **Create Agent**. The system builds the workspace and registers the Agent:

```text
workspace-<agent-name>/
├── skills/        what was just imported
├── tasks/         empty, waiting for the next step
├── materials/     empty, input files for a Task go here
└── outputs/       empty, where a Task writes unless it says otherwise
```

**A freshly created Agent has no Tasks and an empty Work Queue. That is normal.**

### 2. Create the Task

Task Management → **Create Task**:

1. Pick the **Agent**
2. Pick the **Task Markdown** — the `.md` sitting in that folder
3. Press **Create**

The format and the Expected Output paths are validated on creation; anything wrong is refused there and then. Afterwards you can click the row in **Available Tasks** to see exactly what is stored on the device.

### 3. Run it

Find the Task in **Available Tasks** and press **START**. A new row appears under **Task Runs** with Status, Tokens, and Duration. A running Task has a **STOP**.

## Where the output files land

An Expected Output with **no directory of its own** (say `cpu-usage-monitor-report.md`) resolves to `outputs/cpu-usage-monitor-report.md`. The declaration, every Step's `execute` and `check`, and the Final Verification are all rewritten together, so the check always looks where the command writes.

A name that already carries a directory (say `reports/cpu.md`) is taken exactly as written, and the Task owns creating that directory.

## Limits

| Item | Maximum |
|---|---|
| Agent ZIP | 20 MB |
| Task Markdown | 64 KB |
| Material ZIP (optional) | 1 GB |

An Agent name may use lowercase letters, digits, and hyphens only, and cannot be `main`.

## Building your own Agent

One folder holding exactly two things, zipped:

```text
my-agent/
├── TOOLS.md       Skill catalog: which Skills this Agent has
└── skills/        The Skills themselves, one folder each
    └── my-skill/
        └── SKILL.md
```

Both are required, and both must sit at the top level — **do not wrap them in another folder**.

The Skills listed in `TOOLS.md` must match the folders under `skills/` **exactly**. This is the most common reason an import fails.

**The ZIP must not contain `tasks/`** — one that does is refused. Tasks are always created afterwards, one at a time, in the Dashboard.
