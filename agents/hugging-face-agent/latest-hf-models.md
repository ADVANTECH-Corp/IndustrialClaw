---
schema_version: "1.0"
---

# Hugging Face 七日精選模型週報

## Goal
以本次搜尋工作命令啟動時間往前七天為範圍，精選最多15個公開模型家族、每類最多3個，逐一提供繁體中文用途、適用對象、入選理由、來源原文證據與限制；依 frontend-design 的設計規範與已覆核的 HF 設計計畫生成響應式網頁，產生本次執行可核對的網頁與JSON，避免舊檔或中斷後晚到檔被當成成功。

## General Setting
- cycle: 1
- retry: 0

## Expected Outputs
- `outputs/hf-weekly/report.html`
- `outputs/hf-weekly/models.json`

## Execution Procedure

## Step 1
- name: 離線輸出一致性與終止回歸
- prompt: 執行既有離線回歸，驗證舊執行、混合輸出、未完成提交及自有子程序終止後晚到寫入均被拒絕，保留本次測試證據。
- execute: `python3 skills/hf-weekly-review/scripts/test_review.py`
- check: `python3 skills/hf-weekly-review/scripts/test_review.py --check`
- on failure: stop
- note: 測試只啟動並終止自己的測試子程序，不修改平台監控，不等於平台stop-task整合已驗證。

## Step 2
- name: Frontend 設計與相容性回歸
- prompt: 先讀取 skills/frontend-design/SKILL.md 與 skills/frontend-design/DESIGN.md，再檢查設計 renderer 與既有資料驗證器相容、HTML escape、空結果與響應式設計規則。
- execute: `python3 -m skills.frontend-design.scripts.report test`
- check: `python3 -m skills.frontend-design.scripts.report test-check`
- on failure: stop
- note: 所有命令必須從 Agent 工作區逐字執行。本 Task 的 execute/check 優先於 Skill 文件中的舊呼叫範例；使用 python3 -m skills.frontend-design.scripts.report，不得改成路徑、不得省略 skills. 前綴、不得自行改工作目錄。靜態與離線檢查不等於實際瀏覽器截圖驗收，不得宣稱已看過手機或桌面畫面。

## Step 3
- name: 依 Frontend 設計生成七日模型報告
- prompt: 使用 frontend-design 的 HF 設計 renderer，沿用既有品質篩選、家族合併與摘要，生成 outputs/hf-weekly/report.html 和 outputs/hf-weekly/models.json，真實回報進度並保存分段成果與執行識別。
- execute: `python3 -m skills.frontend-design.scripts.report build`
- check: `python3 -m skills.frontend-design.scripts.report verify`
- on failure: stop
- note: 在原 build 發布及計算雜湊之前套用設計，不事後修改網頁。沿用 Python 標準函式庫與原本機 Gemma；不增加套件、模型下載、外部字體或CDN。腳本自身硬上限720秒；收集240秒、讀卡90秒、摘要300秒。來源暫時錯誤最多重試3次，遵守限流；平台TASK_ID或TASK_ARTIFACT_DIR必須存在。公開資料、不需Token、不下載權重。本機Gemma摘要無原文依據不得入選，缺資料與不完整搜尋明示，不湊數；舊輸出保留但不得驗收為本次成功。

## Final Verification
- execute: `python3 -m skills.frontend-design.scripts.report verify --final`
- success condition: 命令退出碼為0；本次原回歸與設計回歸均通過，本次平台身份、run_id、完成收據、雜湊與執行副本一致，七日時窗/來源/用途原文依據/家族與分類上限/網頁與JSON一致；網頁須與本版 frontend-design renderer 從相同JSON重建結果逐字一致，部分搜尋或零入選必須明確說明。實際手機及桌面瀏覽器版面另行檢查，若無可用環境須明示未驗證，不得用靜態CSS檢查冒充視覺驗收。
