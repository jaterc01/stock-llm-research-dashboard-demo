---
title: "Stock-LLM 研究看板公開版"
type: dashboard_readme
as_of: 2026-05-13
status: active
---

# Stock-LLM 研究看板公開版

這個資料夾放的是可以公開分享的靜態研究看板。它整理 Stock-LLM_Wiki 裡的候選股票與 ETF 研究進度，但不是投資建議，也不是買進清單。

## 資料邊界

- 所有股票、ETF、價格、估值與研究摘要都必須保留 `as_of`、`price_as_of`、`retrieved_at` 或 freshness 邊界。
- 候選標的只代表「值得排入研究流程」，不代表可以買進。
- 高分只代表目前研究資料比較完整，不是買進分數。
- `needs_refresh_before_decision` 的意思是：正式投資使用前仍要刷新官方資料、估值、停利停損、風險預算與 buy-decision linter。
- Yahoo chart 可以作為公開行情快照來源；看板會標示 `source = public_market_data - yahoo chart`，但這仍然只是價格位置輔助，不是買賣建議。

## 產生看板

在 repo 根目錄執行：

```powershell
python scripts/research_dashboard_builder.py
```

產生器會更新：

- `wiki/meta/research-dashboard/dashboard-data.json`
- `wiki/meta/research-dashboard/index.html`

執行結果會印出股票數、需要刷新數、低信心數與價格無法判定數。

## 本機開啟

可以用任何靜態檔案伺服器開啟，例如：

```powershell
python -m http.server 8899
```

然後打開：

```text
http://127.0.0.1:8899/wiki/meta/research-dashboard/index.html
```

若 8899 被占用，可以改用其他 port：

```powershell
python -m http.server 8898
```

## Quote helper

Quote helper 只是瀏覽器 overlay，用來在本機補充報價顯示。它不會改寫 `claims/`、`wiki/companies/` 或任何投資結論。

```powershell
python scripts/quote_helper_server.py
```

預設網址：

```text
http://127.0.0.1:8765
```

如果要改 port：

```powershell
$env:STOCK_DASHBOARD_QUOTE_PORT = "8876"
python scripts/quote_helper_server.py
```

看板網址可加上：

```text
?quotePort=8876
```

GitHub Pages 公開版通常無法連到你的本機 quote helper，所以會顯示靜態 Yahoo chart 公開行情快照，或顯示「報價暫無」。這是正常狀態，不代表資料被隱藏。

## GitHub Pages 公開版

公開版網址：

```text
https://jaterc01.github.io/stock-llm-research-dashboard-demo/wiki/meta/research-dashboard/index.html
```

`.github/workflows/publish-research-dashboard-demo.yml` 會在本體 repo 的 dashboard、company、ETF、claims 或 workflow 更新後，重新產生看板並發布到 demo repo。workflow 只能發布產生後的靜態檔案，不得提交 deploy key、私鑰或任何 secret。

## 公開展示注意事項

- 看板可以拿來展示研究流程與資料完整度。
- 看板不能拿來當作買進、賣出、保證獲利或持倉建議。
- 價格位置只說明「目前價格在估值或近一年區間中的位置」。
- `public_market_data - yahoo chart` 代表公開市場資料快照；正式投資前仍需以官方來源、券商或交易所資料複查。
- 若看到「需要更新後才能判斷」，代表資料還不能直接用於正式投資決策。
