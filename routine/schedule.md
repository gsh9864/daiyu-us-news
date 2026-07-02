# 排程設定（Claude Code 網頁 Cloud scheduled task）

用 Claude Code on the web 的 **Cloud scheduled task**（跑在 Anthropic 雲端、不需自己電腦開機）來定時產文。
設定位置：Claude Code 網頁 → 排程 / Schedule（Automations）→ 新增 Cloud task，指向這個 repo。

參考：<https://code.claude.com/docs/en/scheduled-tasks>

## 兩個排程

| 名稱 | 時間（台灣） | cron（台灣時區） | cron（若介面要 UTC） |
|---|---|---|---|
| 每日盤前 | 週一～週五 19:30 | `30 19 * * 1-5` | `30 11 * * 1-5` |
| 每週摘要 | 週日 21:00 | `0 21 * * 0` | `0 13 * * 0` |

> 設定時先確認介面顯示的時區：是台灣時間就用左欄 cron，是 UTC 就用右欄。

## 每日盤前 — 貼這段 prompt

```
讀這個 repo 的 routine/daily-prompt.md 與 CLAUDE.md，依照規格產生「今天」（台灣時間）的美股盤前新聞報。
用 WebSearch 研究過去 24 小時的：川普動態、聯準會、國際重大、黃金市場、投資人追蹤。
寫到 docs/daily/YYYY-MM-DD.md（開頭務必含「> 今日重點：…」那行），
commit 訊息用「Add daily news YYYY-MM-DD」，push 到 main。
若今天是美股休市日（週末或假日），不要產文，直接結束。
發布與 Telegram 通知會由 GitHub Actions 自動完成，你不用碰。
```

## 每週摘要 — 貼這段 prompt

```
讀這個 repo 的 routine/weekly-prompt.md 與 CLAUDE.md，依照規格產生「本週」（過去 7 天）的美股新聞摘要。
用 WebSearch 整合本週的川普動態、聯準會、國際地緣三大類重點，並給下週基調。
寫到 docs/weekly/YYYY-Www.md（ISO 週數，開頭務必含「> 本週重點：…」那行），
commit 訊息用「Add weekly summary YYYY-Www」，push 到 main。
發布與 Telegram 通知會由 GitHub Actions 自動完成，你不用碰。
```
