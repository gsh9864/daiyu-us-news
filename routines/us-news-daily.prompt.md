【排程：美股盤前日報】請照以下完整規格重新做一次（不要沿用之前的新聞內容）。

## 準備
```bash
export LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 PYTHONIOENCODING=utf-8
DATE=$(date +%Y-%m-%d); WEEKDAY=$(LANG=C date +%A | sed 's/Monday/週一/;s/Tuesday/週二/;s/Wednesday/週三/;s/Thursday/週四/;s/Friday/週五/;s/Saturday/週六/;s/Sunday/週日/'); echo $DATE $WEEKDAY
git fetch origin main && git checkout -B main origin/main
cat data/watchlist.json; python3 -c "import json;d=json.load(open('data/trusted-news.json'));print(len(d['news']),'則已收錄')"
```

## 內容（過去 24 小時）
1. **川普動態**——只抓他親自發布：Truth Social、X（realDonaldTrump、@POTUS）、Facebook、行政命令/Memorandum/Proclamation、親口直接引語。不抓「他做了什麼/見了誰」、幕僚代言、分析師解讀、推測語。多管道同一則只列一次；無 → 「過去 24 小時無新發言 / 新行政命令」
2. **聯準會**：FOMC、官員發言、利率、CPI/PCE/非農。無 → 「過去 24 小時無重大動態」
3. **國際重大** 5-8 條：美中、中東、歐洲為主，東南亞次要，每條附「對美股潛在影響」一句話
4. **黃金市場**（必抓）：XAU/USD 現貨、GC=F/GLD（±%）、台銀牌告（如能取得）；7 天/30 天漲跌 % + 趨勢、支撐/壓力；推升/壓制因素。來源 Kitco、Bloomberg、Reuters、rate.bot.com.tw/gold
5. **投資人追蹤**（每項都列，無動態一行帶過）：
   - 🐋 Buffett/Berkshire（無動態時附最近 13F 季度與重倉 TOP 3）、Burry/Scion（做空 → 🚨）、Ackman/Pershing Square、Druckenmiller/Duquesne、Tepper/Appaloosa、Dalio/Bridgewater
   - 📊 國會交易（Pelosi 等；capitoltrades 等）、Mag 7（NVDA/AAPL/MSFT/GOOGL/META/AMZN/TSLA 每個一行；CEO/CFO 大筆賣出 → ⚠️）、13F 其他基金（Tiger Global、Greenlight、Third Point、Citadel；無 → 「目前無新 13F 揭露 — 下次 deadline：…」，揭露日 5/15、8/14、11/14、2/14）
6. **持股新聞**（大又的追蹤清單）：讀 `data/watchlist.json`，對每一檔（目前 NVDA、TSM、SPCX）找過去 24 小時的新聞，依 `focus` 欄位優先。
   - **只收可信度 91% 以上的來源**，評分標準照 `data/trusted-news.json` 的「評分標準」：99＝SEC.gov 申報、公司 IR 正式公告；95＝Reuters、AP、Bloomberg、WSJ、FT、CNBC、中央社；92＝Washington Post、NYT、CNN、Fortune、TechCrunch、Axios、Barron's。Yahoo Finance、Investing.com、MarketBeat、Motley Fool、Benzinga、Seeking Alpha、Space.com、部落格、聚合站、預測市場、維基一律不收。
   - 每檔 1-3 則，每則一行：重點（繁體中文）＋來源名稱＋可信度％＋連結。只有低可信度來源報導的傳聞，不列入；若很重要，寫「⚠️ 未經可靠來源證實：…」且不附連結。無 → 「過去 24 小時無可信新聞」。
   - 不寫任何持股數量、成本、損益（repo 是公開的）。
   - 把本次收錄的新聞**附加**到 `data/trusted-news.json` 的 `news` 陣列（欄位：symbol、date、title、source、credibility、url、verified=今天），URL 已存在就跳過，並更新 `updated`。
   - 若有重大消息（財報、執行長異動、解禁、監管處分），在第 2 行「今日重點」優先寫它。

來源優先 Reuters/AP/Bloomberg/WSJ/FT/Axios/Politico/CNBC/Barron's/SEC.gov/capitoltrades；避免中國/香港媒體、維基、內容農場；無具體 URL 不附連結。

## 格式
```
# 美股盤前新聞報 - YYYY-MM-DD（週X）

> 今日重點：HEADLINE
> 自動整理自 Reuters / AP / Bloomberg / WSJ / FT / Axios / Politico / CNBC / SEC / Kitco
> 距離美股開盤約 2 小時

## 持股新聞
### NVDA
### TSM
### SPCX
## 川普動態（他親自發布的）
## 聯準會
## 國際重大
## 黃金市場
## 投資人追蹤
### 🐋 機構大鯨魚
### 📊 其他追蹤

---
*本頁由 Claude routine 自動生成於 YYYY-MM-DD HH:MM (UTC)*
```
**第 2 行 `> 今日重點：HEADLINE` 必須有**（repo 的 GitHub Action notify-telegram 會抓這行發 Telegram）。HEADLINE 30 中文字內，持股重大消息與重大投資人訊號優先。「持股新聞」的小標依 watchlist.json 的順序與代號產生。

## 寫入 GitHub（只做一次）
日報 `docs/daily/${DATE}.md` 和更新後的 `data/trusted-news.json` 放在**同一個 commit**：git 寫兩個檔、`git add docs/daily/${DATE}.md data/trusted-news.json`、commit "Add daily news ${DATE}"、`git push origin main`。被拒時才改用 `mcp__github__create_or_update_file`（gsh9864/daiyu-us-news, branch main；已存在就先取 sha），日報先寫、連結庫後寫。

## Telegram
寫入成功 → **不要推**（GitHub Action 會代發）。
寫入失敗 → 用 python3 + JSON body 推（token 從環境變數 TELEGRAM_BOT_TOKEN / TELEGRAM_CHAT_ID 讀，.strip()，不可印出）：
「⚠️ 美股日報寫入 GitHub 失敗（DATE）\n原因：[30 字內]\n👉 開 Claude 說「美股日報壞了」」

## 回報
```
RESULT: GH_OK=<0|1> via=<mcp|git|none> TG=<ok|fail|skip>
GH_DETAIL: <commit sha 或錯誤前 200 字>
NEWS_DB: 新增 <N> 則
```
失敗就老實寫失敗。
