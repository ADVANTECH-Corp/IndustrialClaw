---
schema_version: "1.0"
---

# Hello World Skill Execution Test

version: 1.0
author: Judy
date: 2026-08-20

## Goal

Verify that Industrial Claw can invoke the installed hello-world Skill and execute its bundled script successfully.

## General Setting

- cycle: 1
- retry: 0

## Expected Outputs

- `hello-world-result.txt`

## Execution Procedure

## Step 1

- name: Execute hello world skill
- prompt: Execute this step using the required hello-world Skill. Run the exact execute command. Do not only explain or summarize. The step is complete only after the command has actually executed.
- execute: `bash skills/hello-world/scripts/hello.sh > hello-world-result.txt`
- check: `test -s hello-world-result.txt && grep -q "Industrial Claw skill executed successfully." hello-world-result.txt`
- on failure: stop
- note: Do not modify system settings. Only create hello-world-result.txt in the current Task workspace.

## Final Verification

- execute: `test -s hello-world-result.txt && grep -q "Industrial Claw skill executed successfully." hello-world-result.txt`
- success condition: hello-world-result.txt exists and contains the expected Industrial Claw skill execution success message.
