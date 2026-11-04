---
schema_version: "1.0"
---

# arXiv 最新30篇 frontend-design 網頁報告

## Goal
沿用原十分類，按 submittedDate 最新排序，目標取得30篇不重複論文，只呈現標題、原文連結與分類。網頁生成必須採用 frontend-design 官方指引及已審閱的設計，保留資料真實性、部分結果揭露和來源驗證；進行桌面與手機版瀏覽器檢查。

## General Setting
- cycle: 1
- retry: 0

## Expected Outputs
- outputs/arxiv-ai/report.html
- outputs/arxiv-ai/papers.json

## Execution Procedure
依序執行固定命令，不自行重跑抓取或解析論文。Step 2 前讀取 skills/frontend-design/SKILL.md 與 skills/frontend-design/DESIGN.md；生成器直接使用該技能的 scripts/design.py，不是只列出技能名稱。最多九頁，每頁10篇，600秒收集預算，單次timeout20秒，逾時額外重試2次，尊重Retry-After。只使用本次資料；來源失敗可跳過且明確揭露。瀏覽器只讀本機報告，無外部素材、無安裝及模型呼叫。截圖與 browser-check.json 為輔助證據，不增加正式 Expected Outputs。自動化瀏覽器檢查不代表主觀美感已獲人工核准。

## Step 1
- name: 離線回歸測試
- prompt: 執行重試、去重上限、失敗跳過、部分結果、資料竄改、舊資料隔離與設計轉義測試。
- execute: python3 -u skills/arxiv-fulltext-review/scripts/latest.py selftest
- check: python3 -u skills/arxiv-fulltext-review/scripts/latest.py selftest --strict
- on_failure: stop

## Step 2
- name: 收集論文並以 frontend-design 產生網頁
- prompt: execution_timeout 設為900秒；先讀取 frontend-design 指引及 DESIGN.md，透過已套用該技能設計的生成器保存本次 outputs/arxiv-ai/report.html 及 outputs/arxiv-ai/papers.json；不下載全文或生成摘要。
- execute: python3 -u skills/arxiv-fulltext-review/scripts/latest.py run --out outputs/arxiv-ai
- check: python3 -u skills/arxiv-fulltext-review/scripts/latest.py verify --out outputs/arxiv-ai
- on_failure: stop

## Step 3
- name: 桌面與手機瀏覽器檢查
- prompt: execution_timeout 設為180秒；在 frontend-design 宣告的 Playwright runtime 驗證1440px及390px排版、標題連結、篩選恢復、焦點及橫向溢出，將截圖與本次報告hash證據存入 outputs/arxiv-ai；失敗停止，不冒稱視覺驗收通過。
- execute: python3 skills/frontend-design/scripts/browser_check.py run --out outputs/arxiv-ai
- check: python3 skills/frontend-design/scripts/browser_check.py verify --out outputs/arxiv-ai
- on_failure: stop

## Final Verification
- execute: python3 skills/frontend-design/scripts/browser_check.py final --out outputs/arxiv-ai
- success_condition: 真實exit0；兩個Expected Outputs為本次非空regular files，raw來源hash及標題／連結／分類／排序／去重一致且至多30篇；不足或跳過頁面明確披露，零篇不冒充完整結果。桌面及手機版瀏覽器證據與當前報告hash及run_id一致且截圖存在。截圖仍需由System檢視後才能宣稱主觀視覺核准。
