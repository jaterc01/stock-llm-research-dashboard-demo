---
title: "Stock Research Dashboard"
type: dashboard_readme
as_of: 2026-05-13
status: active
---

# Stock-LLM 研究看板

這個看板是公開展示用的研究進度頁。它會整理：

- `wiki/companies/*.md` 已建立公司頁的標的。
- `wiki/universe/stock-universe-dashboard.md` 的股票候選池。
- `wiki/universe/candidate-universe-dashboard.md` 的 ETF 候選池。

候選池只是研究排程，不是買進清單。分數越高只代表目前研究資料比較完整或刷新優先順序較高，不代表可以買進。

## 產生看板

在 repo 根目錄執行：

```powershell
python scripts/research_dashboard_builder.py
```

會產生：

- `wiki/meta/research-dashboard/dashboard-data.json`
- `wiki/meta/research-dashboard/index.html`

執行完成時會輸出總筆數與需要刷新數。公開發布 workflow 也會檢查：

- 公司頁都有被看板覆蓋。
- candidate universe 至少 80 筆。
- `buy_now_gate_tickers` 必須是空陣列。

## 啟動報價工具

報價工具只是在瀏覽器上加一層即時報價顯示，不會寫回研究庫。

```powershell
python scripts/quote_helper_server.py
```

預設網址：

```text
http://127.0.0.1:8765
```

若要改 port：

```powershell
$env:STOCK_DASHBOARD_QUOTE_PORT = "8876"
python scripts/quote_helper_server.py
```

開啟看板時加上：

```text
?quotePort=8876
```

公開 GitHub Pages 預設不會嘗試連線到使用者本機的 quote helper；畫面會顯示「報價暫無」或「公開版未連接即時報價」。這不代表資料過期被修正，也不代表有最新價格。

## 開啟看板

直接開靜態檔：

```text
wiki/meta/research-dashboard/index.html
```

或啟動簡單 static server：

```powershell
python -m http.server 8899
```

再開：

```text
http://127.0.0.1:8899/wiki/meta/research-dashboard/index.html
```

公開 demo：

```text
https://jaterc01.github.io/stock-llm-research-dashboard-demo/wiki/meta/research-dashboard/index.html
```

## 資料邊界

- 這是 point-in-time 研究看板，不是投資建議。
- 所有股票與 ETF 資料都要看 `as_of` 與 freshness 邊界。
- Quote helper 僅是瀏覽器 overlay；不會更新 `claims/`、公司頁、ETF 頁、持倉或任何來源事實。
- 分數、即時報價、relationship graph、event signal 或 theme narrative 都不是買進理由。
- `needs_refresh_before_decision` 的意思是：投資使用前仍需補官方來源、估值、價格計畫、停利停損、風險預算、技術面、籌碼/資金流、actionability gate 與 linter。
- 若來源文字已有亂碼，只能在看板呈現層標示或 fallback，不可改寫來源事實來美化展示。

## 公開展示提醒

這個頁面適合展示研究範圍、資料缺口與刷新優先順序。對外說明時請避免把「候選標的」、「高分」、「主題關聯」或「報價 overlay」說成買進建議。
