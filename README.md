# IndustrialClaw Agents

Ready-to-use Agents for IndustrialClaw. Each one comes with a Task, so you can import it and run it right away.

Browse the Agents in the [`agents/`](agents/) folder.

## Download

Each Agent needs two files: the `.zip` (Agent) and the `.md` (Task).

**Option A: from the GitHub page**

1. Open the Agent's folder, e.g. [`agents/hello-world-agent`](agents/hello-world-agent/)
2. Click the file (`hello-world-agent.zip`)
3. Click the **Download raw file** button (⬇) at the top right of the file view
4. Do the same for the `.md` Task file

Want everything at once? On the repo's main page, click **Code → Download ZIP**.

**Option B: with curl**

```bash
BASE=https://raw.githubusercontent.com/ADVANTECH-Corp/IndustrialClaw/master/agents
curl -LO $BASE/hello-world-agent/hello-world-agent.zip
curl -LO $BASE/hello-world-agent/hello-world-task.md
```

For another Agent, swap in its folder and file names.

## Quick start

You'll need an **Administrator** account on the Dashboard.

1. **Import the Agent**: go to Agents → Agent Management → **Import Package**, pick the `.zip`, then press **Create Agent**
2. **Create the Task**: go to Task Management → **Create Task**, pick the Agent and its `.md` file, then press **Create**
3. **Run it**: find the Task in **Available Tasks** and press **START**

Results show up in the Agent's `outputs/` folder.

> New device? Try [`hello-world-agent`](agents/hello-world-agent/) first. It takes under a minute and confirms everything works.

## Troubleshooting

- **Import asks for confirmation**: the Security Scanner rated the package *medium*. Review it and confirm.
- **Import is blocked**: the package was rated *high* risk, or the ZIP layout is wrong (see below).
- **No Tasks after import**: that's normal. Create one in step 2.

## Build your own Agent

Start from `hello-world-agent`. See the step-by-step guide in [agents/README.md](agents/README.md#build-your-own-agent).
