# CLAUDE.md — 大又的美股新聞秘書（routine 交接規格）

這個 repo 每天自動產生「美股盤前新聞報」與「每週摘要」，發布到 GitHub Pages，並由 GitHub Actions 代發 Telegram 通知。

本檔是給**接手產文的 Claude（雲端 / 本機皆可）**看的操作規格。實際的產文指令在 `routine/` 底下：

- 每日盤前新聞 → 依照 [`routine/daily-prompt.md`](routine/daily-prompt.md)
- 每週摘要 → 依照 [`routine/weekly-prompt.md`](routine/weekly-prompt.md)

---

## 整條 pipeline 怎麼運作

```
routine（Claude）              GitHub Actions（自動，不用你碰）
─────────────────             ─────────────────────────────
1. 上網研究（WebSearch）
2. 寫 markdown 到 docs/         push 到 main
   daily/ 或 weekly/      ──►  ├─ deploy.yml       → 建 MkDocs 站、發布 GitHub Pages
3. commit + push 到 main        └─ notify-telegram.yml → 發 Telegram 通知（標題 + 重點 + 連結）
```

**你只負責前三步（研究 → 寫檔 → push）。** 發布與 Telegram 通知在檔案 push 到 `main` 後全自動完成。

## 產文時間（台灣時間）

| 類型 | 時間 | UTC | 說明 |
|---|---|---|---|
| 每日盤前 | 週一～週五 19:30 | 平日 11:30 | 美股盤前約 2 小時 |
| 每週摘要 | 週日 21:00 | 週日 13:00 | 涵蓋過去 7 天 |

- 美股假日（如 Juneteenth、國慶日等）休市日不產每日新聞。
- 檔案末尾的時間戳寫「自動生成於 YYYY-MM-DD HH:MM (UTC)」，用**當下 UTC 時間**。

## 檔案位置與命名（務必精確，Actions 靠這個觸發）

- 每日：`docs/daily/YYYY-MM-DD.md`
- 每週：`docs/weekly/YYYY-Www.md`（ISO 週數，例如 `2026-W27.md`）
- commit 訊息：`Add daily news YYYY-MM-DD` 或 `Add weekly summary YYYY-Www`
- push 目標分支：`main`

## 這個雲端環境的關鍵限制（重要，實測結果）

- ✅ **WebSearch 可用，是研究主力。** 回傳內容當期、來源正確，足以覆蓋文章所有區塊（川普、Fed、國際、金價、投資人追蹤）。
- ❌ **WebFetch / curl 幾乎全被擋（403）。** 新聞站有反爬蟲、proxy 也擋原始 curl，連 .gov 站直接抓都 403。**不要依賴 WebFetch 抓內文**，改用 WebSearch 取得摘要與數據；需要引用連結時，用 WebSearch 結果裡回傳的 URL。
- ❌ **雲端連不到 `api.telegram.org`。** 別想自己發 Telegram——push 上去後由 `notify-telegram.yml` 代發即可。
- ✅ **git push 到 `main` 可用。**

## 來源策略（沿用 `docs/index.md`）

- **優先**：Reuters、AP、Bloomberg、WSJ、FT、Axios、Politico、CNBC
- **官方/數據**：SEC（13F、Form 4）、whitehouse.gov、Federal Register、Kitco（金價）、capitoltrades / quiverquant / unusualwhales（國會交易、內部人）
- **避免**：中國/香港媒體、維基百科、內容農場

## Telegram 通知怎麼抓內容（決定你 md 的寫法）

`notify-telegram.yml` 會從 md 檔抓兩樣東西：

1. **標題** = 第一個 `# ` 標題行
2. **重點** = 符合 `今日重點：…` 或 `本週重點：…` 的那一行（`：` 半形冒號亦可）

所以**每篇日報開頭一定要有 `> 今日重點：…`、每篇週報一定要有 `> 本週重點：…`**，否則 Telegram 只會顯示標題與連結、少了那條 🔍 摘要。

## 品質原則

- 川普動態只列「他親自發布/公開發言」的，多管道同一消息只列一則。
- 某類別過去 24 小時無重大動態時，明確寫「過去 24 小時無重大動態」，**不灌水**。
- 數字（利率、CPI/PCE、金價、持倉金額）要具體；引用盡量附來源連結。
- 全文繁體中文；川普原話保留英文原文再視需要補充。
