---
title: "Stock Research Dashboard"
type: dashboard_readme
as_of: 2026-05-12
status: active
---

# Stock Research Dashboard

這個 dashboard 是 Stock-LLM_Wiki 公司研究頁的瀏覽器看板，用來公開展示研究覆蓋、資料新鮮度、資料缺口與下一步 deepsearch 任務。它不是下單系統，也不會產生買進建議。

## 1. Generate Dashboard

在 repo 根目錄執行：

```powershell
python scripts/research_dashboard_builder.py
```

成功時會產生：

- `wiki/meta/research-dashboard/dashboard-data.json`
- `wiki/meta/research-dashboard/index.html`

輸出會包含公司數與 `needs_refresh` 數。

## 2. Start Quote Helper

另開一個終端機：

```powershell
python scripts/quote_helper_server.py
```

預設 URL 是：

```text
http://127.0.0.1:8765
```

如果 `8765` 被占用，改用其他 port：

```powershell
$env:STOCK_DASHBOARD_QUOTE_PORT = "8876"
python scripts/quote_helper_server.py
```

打開 dashboard 時加上：

```text
?quotePort=8876
```

## 3. Open Dashboard

可以直接用瀏覽器開：

```text
wiki/meta/research-dashboard/index.html
```

也可以用靜態 server 開：

```powershell
python -m http.server 8899
```

再前往：

```text
http://127.0.0.1:8899/wiki/meta/research-dashboard/index.html
```

## Data Boundary

- `dashboard-data.json` is generated from Wiki and claims files.
- Quote helper prices are browser overlays only.
- Quote helper prices do not update `claims/`, company pages, holdings, or source facts.
- A high score is not a buy recommendation.
- `needs_refresh_before_decision` means official-source refresh, valuation, sell plan, risk budget, technical/chip checks, actionability gate, and linter evidence are still required before investment use.
- `live_price_unavailable` means the quote helper did not return a usable browser-time quote; it does not make stale or unknown Wiki prices current.

## Deepsearch Use

Use the right-side deepsearch tasks to gather missing official filings, IR material, market snapshots, valuation evidence, technical snapshots, chip/flow data, and sell-plan inputs. Ingest refreshed evidence through the normal Stock-LLM_Wiki workflows before treating it as point-in-time fact.

## Public Demo Caveats

- The dashboard is safe to demo as a research quality and refresh-priority view.
- Do not describe score, relationship graph exposure, event signals, theme narratives, or quote overlays as `buy_now`.
- If source markdown text is mojibake, the dashboard may show a display-layer fallback and a warning; do not rewrite company pages just to beautify the demo.
- Real holdings and broker data are out of scope for this dashboard.
