# 每日盤前新聞報 — 產文指令

> 先讀 repo 根目錄的 `CLAUDE.md`（pipeline、時間、限制、來源策略）。本檔只講「怎麼寫今天這一篇」。

## 任務

產生**今天**的美股盤前新聞報，寫到 `docs/daily/YYYY-MM-DD.md`，commit（訊息 `Add daily news YYYY-MM-DD`）並 push 到 `main`。

- 日期用今天的日期（台灣時間），週末與美股休市日不產。
- 涵蓋範圍：過去 24 小時內的動態。
- 研究工具：**以 WebSearch 為主**（WebFetch 會 403）。每個區塊各下 1～3 個查詢，抓當期數字與來源連結。

## 研究清單（建議的 WebSearch 查詢方向）

1. `Trump Truth Social latest post [今天日期]`、`Trump executive order this week`
2. `Fed FOMC Warsh statement / speech [本週]`、`US CPI PCE nonfarm payrolls latest`
3. 國際：`Iran Israel US strike oil price [今天]`、`US China trade tariff [本週]`、`Taiwan strait / Russia Ukraine [本週]`、`ECB / Europe markets [本週]`
4. 金價：`spot gold XAU/USD price today [日期] GLD ETF`
5. 投資人：`Buffett Berkshire / Michael Burry Scion / Ackman Pershing / Druckenmiller / Tepper Appaloosa / Dalio Bridgewater latest [本週]`
6. 國會/內部人：`Nancy Pelosi congressional stock trades [本月]`、`Mag 7 insider selling SEC Form 4 [本週]`、`13F filing latest`

## 版型（嚴格照抄骨架，內容換成當天）

````markdown
# 美股盤前新聞報 - YYYY-MM-DD（週X）

> 今日重點：<重點一>；<重點二>；<重點三>
> 自動整理自 Reuters / AP / Bloomberg / WSJ / FT / Axios / Politico / CNBC / SEC / Kitco
> 距離美股開盤約 2 小時

---

## 川普動態（他親自發布的）

- **[Truth Social 貼文 — 日期]** <他親發的內容，英文原話保留>（來源：xxx）
- **[行政命令 — 日期]** <內容>（來源：whitehouse.gov）

## 聯準會（Fed）

> 今日無新發言或 FOMC 會議時，於此註明並彙整最新動態。

- <利率決議 / 官員發言 / CPI・PCE・非農 數據，附具體數字>

## 國際重大

- **[美國/伊朗]** <內容> — [來源](url)
  - 對美股潛在影響：<一句>
- **[油價]** ...
- **[美中貿易]** ...
（5–8 條，每條附「對美股潛在影響」）

## 黃金市場

**今日金價（YYYY-MM-DD）**
- XAU/USD 現貨：約 **$X/oz**（相對昨日 ±X%）
- GLD ETF：約 $X
- 台銀牌告：<有就寫，沒有就註明查 rate.bot.com.tw/gold>

**走勢摘要**
- 7 天 / 30 天 / 關鍵支撐壓力

**驅動因素（過去 24 小時）**
- 壓制：...
- 推升：...

## 投資人追蹤

### 機構大鯨魚

#### Buffett / Berkshire Hathaway
#### Burry / Scion Asset Management
#### Ackman / Pershing Square Capital Management
#### Druckenmiller / Duquesne Family Office
#### Tepper / Appaloosa Management
#### Dalio / Bridgewater Associates

（每位：最新動態、最近 13F、過去 24 小時有無新動作；沒有就寫「過去 24 小時：無新動態」）

### 其他追蹤

#### 國會交易（Pelosi 等）
#### Mag 7 內部人動態
（NVDA / AAPL / MSFT / GOOGL / META / AMZN / TSLA，逐一列，異常用 ⚠️ 標注）
#### 13F 其他知名基金
（Tiger Global / Greenlight / Third Point / Citadel 等；註明下次 13F deadline）

---

*本頁由 Claude routine 自動生成於 YYYY-MM-DD HH:MM (UTC)*
````

## 收尾

1. 確認開頭有 `> 今日重點：`（Telegram 靠它抓 🔍 摘要）。
2. `git add docs/daily/YYYY-MM-DD.md`
3. `git commit -m "Add daily news YYYY-MM-DD"`
4. `git push origin main`（push 後 deploy 與 Telegram 自動完成，不用再做別的）
