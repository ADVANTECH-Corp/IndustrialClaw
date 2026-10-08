---
schema_version: "1.0"
---

# Remote Agent Test
version: 1.8
author: user
date: 20261008

## Goal
使用materials中的連線資訊建立遠端 SSH 連線, 並在遠端建立一個hello-world檔案。

## General Setting
- cycle: 1
- retry: 1

## Expected Outputs

## Execution Procedure

## Step 1
- name: 建立遠端連線
- prompt: 使用 materials/ssh_password.txt 內的連線資訊，建立連線到遠端電腦。
- on failure: stop
- note:

## Step 2
- name: 在遠端建立檔案
- prompt: 在遠端的home目錄下建立檔案`hello-world.txt`
- execute: 
- on failure: stop
- note:

## Step 3
- name: 檢查遠端檔案
- prompt: 在遠端的home目錄下確認檔案`hello-world.txt`是否存在
- execute: 
- on failure: stop
- note:

## Final Verification
- execute: `exit 0`
- success condition: 成功
