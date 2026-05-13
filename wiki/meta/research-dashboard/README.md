---
title: "Stock-LLM 研究看板公開版"
type: dashboard_readme
as_of: 2026-05-13
status: active
---

# Stock-LLM 研究看板公開版

這個資料夾產生可公開瀏覽的 Stock-LLM 研究看板。它是 point-in-time 研究快照，不是買進清單，也不是投資建議。

## 資料邊界

- 每筆股票、ETF、價格、營收、股數與估價資料都必須保留 `as_of`、`price_as_of`、`retrieved_at` 或 freshness 邊界。
- 候選標的只是研究清單，不代表可以買。
- 高分、價格位置、營收估價、relationship graph、event signal 或 theme narrative 都不是 `buy_now`。
- `needs_refresh_before_decision` 的意思是：投資使用前仍需要刷新官方來源、估值、停利停損、風險預算與正式 linter 檢查。
- Yahoo chart 公開行情只能支撐「價格位置」，也就是目前股價在近一年偏高、偏中或偏低；它不能支撐「便宜」、「昂貴」、「合理價」或買進判斷。

## 兩層估價框架

公開版把價格與估價拆成兩層：

1. **價格位置 `market_position_snapshot`**
   - 來源：`public_market_data - yahoo chart`
   - 用途：只描述目前股價在近一年收盤區間的位置。
   - 不是估價，不代表便宜，也不代表可買。

2. **營收基礎估價 `revenue_based_valuation`**
   - 來源必須是官方財報、10-Q / 10-K、年報、法說、公司 IR、公開資訊觀測站或交易所資料。
   - 至少需要營收、股數、每股營收、P/S、P/S 區間、合理價區間、來源 URL 與 `as_of`。
   - 如果缺官方營收或股數，看板會顯示「尚缺官方營收或股數資料，不能做營收基礎估價」。
   - P/S 區間只是研究邊界，不是買賣建議。

## 產生看板

在本體 repo 根目錄執行：

```powershell
python scripts/research_dashboard_builder.py
```

會產生：

- `wiki/meta/research-dashboard/dashboard-data.json`
- `wiki/meta/research-dashboard/index.html`

產生器會輸出股票數、需要刷新數、低信心數、價格未判定數、價格位置數、營收估價可用數、營收估價缺資料數，以及 `buy_now_gate_tickers`。

## 本機瀏覽

啟動靜態伺服器：

```powershell
python -m http.server 8899
```

打開：

```text
http://127.0.0.1:8899/wiki/meta/research-dashboard/index.html
```

如果 8899 被占用，可以改用其他 port：

```powershell
python -m http.server 8898
```

## Quote helper

Quote helper 只是本機瀏覽器 overlay，用來在本機試看報價輔助狀態。公開 GitHub Pages 版不依賴它。

```powershell
python scripts/quote_helper_server.py
```

預設位置：

```text
http://127.0.0.1:8765
```

改用其他 port：

```powershell
$env:STOCK_DASHBOARD_QUOTE_PORT = "8876"
python scripts/quote_helper_server.py
```

看板網址加上：

```text
?quotePort=8876
```

Quote helper 失敗或沒有啟動時，公開版仍會顯示靜態 Yahoo chart 研究快照；如果資料缺失，會明白顯示報價暫無或營收估價不可用，不會把 unknown price 當成 current fact。

## GitHub Pages 公開版

公開網址：

```text
https://jaterc01.github.io/stock-llm-research-dashboard-demo/wiki/meta/research-dashboard/index.html
```

`.github/workflows/publish-research-dashboard-demo.yml` 會在本體 repo 更新 dashboard、company、ETF、claims 或測試後自動重新產生看板，通過資料邊界檢查後發布到公開 demo repo。

發布檢查會確認：

- `buy_now_gate_tickers == []`
- 所有股票都有 `market_position_snapshot`
- 所有股票的 `market_price.source == "public_market_data - yahoo chart"`
- 只有 `revenue_based_valuation.available == true` 時，才允許營收基礎估價欄位被視為可用
- 近一年價格區間不得貢獻 `valuation_attractiveness`
- 公司名稱、搜尋、detail panel 與公開文案不能誤導一般使用者

## 公開展示 caveats

- 這是研究看板，不是下單系統。
- 候選標的不是買進清單。
- 高分不等於買進。
- 「近一年偏低」只代表市場位置，不代表便宜。
- 營收基礎估價缺官方營收或股數時，不能用猜的補齊。
- 正式投資使用前仍需官方來源刷新、估值假設、停利停損、風險預算、部位規劃與正式 linter。
