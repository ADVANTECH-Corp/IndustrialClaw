---
schema_version: "1.0"
---

# Monitor Local CPU and Disk
version: 1.0
author: user
date: 20260812

## Goal
每一輪採樣五次本機的 CPU 使用率、CPU 溫度、根目錄 Disk 使用率與剩餘空間，寫入各自的報告，再由檢查腳本判定這一輪是否成立。總輪數由 General Setting 的 `cycle` 決定

## General Setting
- cycle: 2
- retry: 1

## Expected Outputs

- `cpu-usage-monitor-report.md`
- `cpu-temperature-report.md`
- `disk-monitor-report.md`

## Execution Procedure

## Step 1
- name: CPU 使用率
- prompt: 監控本機的 CPU 使用率，報告寫在宣告的檔名，預設會落在 workspace 的 outputs/ 下
- execute: `python3 skills/device-monitor/scripts/monitoring_loop.py 5 "cpu-usage-monitor-report.md" --aspect cpu`
- check:
- on failure: continue
- note:

## Step 2
- name:  CPU 溫度
- prompt: 監控本機的 CPU 溫度，報告寫在宣告的檔名，預設會落在 workspace 的 outputs/ 下
- execute: `python3 skills/device-monitor/scripts/monitoring_loop.py 5 "cpu-temperature-report.md" --aspect temp`
- check: `python3 skills/device-monitor/scripts/verify_report.py "cpu-temperature-report.md"`，回傳 `0` 才算成功。
- on failure: continue
- note: CPU 溫度資訊讀不到時該欄寫 `Unknown`，不得以估算值補齊。

## Step 3
- name: 根目錄 Disk 使用狀況
- prompt: 監控本機根目錄 Disk 使用率與剩餘空間，報告寫在宣告的檔名，預設會落在 workspace 的 outputs/ 下
- execute: `python3 skills/device-monitor/scripts/monitoring_loop.py 5 "disk-monitor-report.md" --aspect disk`
- check: `python3 skills/device-monitor/scripts/verify_report.py "disk-monitor-report.md"`，回傳 `0` 才算成功。
- on failure: stop
- note: Disk 資訊讀不到時該欄寫 `Unknown`，不得以估算值補齊。

## Final Verification
- execute: `python3 skills/device-monitor/scripts/verify_report.py "cpu-usage-monitor-report.md" && python3 skills/device-monitor/scripts/verify_report.py "cpu-temperature-report.md" && python3 skills/device-monitor/scripts/verify_report.py "disk-monitor-report.md"`
- success condition: The command exits with code `0`.
