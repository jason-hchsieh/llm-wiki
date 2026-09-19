# llm-wiki 設計文件

日期:2026-09-19。來源:ChatGPT 對話紀錄的四個 take-away、`docs/research/2026-09-19-ai-colearning.md`、grill 問答十題。

## 一句話

一個手動啟動的 Claude Code skill `/study`,帶你走「寫已知 → 提假設與預測 → AI 拿證據 → 揭曉比對 → 你解釋 → 白紙重建 → 遷移題 → 存檔」八步。
主體是 skill,因為只有主對話(與 fork)能向你提問,非 fork 的 subagent 拿不到提問工具。兩個環節交給看不到你預測的 subagent,做盲測。

## 已定的決策(grill 十題)

| # | 決策 | 選擇 | 理由 |
|---|---|---|---|
| 1 | 學習對象 | 兩種都做:能跑實驗的程式題、讀書段落題 | 使用者選擇 |
| 2 | 讀書段落的揭曉內容 | AI 先逐點比對你的預測、只列差異;你先解釋;AI 再給完整解釋 | Bisra 2018:先解釋再講解 |
| 3 | 預測閘門硬度 | 只寫在 skill 裡提醒,不用 hook | 使用者選擇,接受「一句直接講就能繞過」 |
| 4 | 未預測就要答案 | 直接回答,紀錄檔標 `skipped_prediction: true`,下次優先抽問 | 不強迫,但留痕跡 |
| 5 | 啟動方式 | `disable-model-invocation: true`,只能手動 `/study` | 工作時不被插手 |
| 6 | 紀錄位置 | 獨立資料 repo `llm-wiki-data`,預設 clone 到 `~/llm-wiki`,路徑由環境變數 `LLM_WIKI_DIR` 決定;紀錄檔 `records/<topic>/NNNN-<slug>.md` | 多台機器靠 git 同步;紀錄與 skill 生命週期不同;不污染別人的 repo |
| 7 | 你的文字怎麼進檔 | 你在對話框打,Claude 逐字抄,不改寫潤飾(寫在 skill 裡,軟約束) | 保留思考痕跡 |
| 8 | 實驗 subagent 看到什麼 | 問題與要變動的條件;不給預測 | 盲測 |
| 9 | 開場抽問 | 每次一題,答完就過,不評分;上次跳過預測的優先 | 分散練習 |
| 10 | 提不出假設 | 反問縮小範圍的問題,最多三次,再給一個提示 | 保留生成效應 |
| 12 | 主題判斷 | 你說的 > 目前目錄對 `topics.yaml` > 問你並回寫 `topics.yaml`;第 0 步只掃該主題 | 同時學多個主題,不能跨主題抽問 |
| 13 | 非程式內容 | 單一 `/study` 入口,`kind` 分 experiment / reading / practice,第 3 到 4 步的差異放 `skills/study/flows/<kind>.md`,判完 kind 才讀 | 規則只寫一次;SKILL.md 控在 150 行內 |
| 14 | 記憶類與間隔複習 | 第一版不做 `/drill`、不做排程。三個入口(你說要記、高信心答錯、下次抽問)收成 Anki 可匯入的 tab 分隔檔 `cards/<topic>.tsv`(正面、背面、標籤=紀錄編號),排程交 Anki | 排程演算法 Anki 已有(SM-2 / FSRS,未核對);第 0 步不依賴卡片檔 |
| 15 | 文件格式 | frontmatter 放機器欄位,內文固定標題順序;格式定義與版本號寫在資料 repo 的 `CLAUDE.md`;`/wiki lint` 檢查缺欄位與標題順序,舊版只警告不改 | Karpathy 的 schema 層;agent 處理固定格式較可靠 |
| 11 | 白紙重建 | 揭曉與解釋結束後獨立一步:你從頭把這題講給一個不懂的人聽,Claude 只指出漏洞不補完 | Roediger 2006 提取練習;Koriat & Bjork 2005 答案在螢幕上時自評高估 |

## 我自己定的小決定

- 每個預測附信心(高/中/低)。高信心且錯的項目在揭曉後優先講。依據:超額修正(hypercorrection)。
- 實驗 subagent 工具:`Read, Grep, Glob, Bash, Write`,不給 `Edit`。「只寫到實驗目錄、不動被研究 repo 的原始碼」寫在 subagent 的 prompt 裡,是軟約束:`tools` 白名單不能限制路徑,而且有 `Bash` 就能用 `sed -i` 改檔。要硬擋得加 `PreToolUse` hook,或 settings 的 permissions deny 規則【後者未查官方文件】,第一版不做。回傳只有指令、產物路徑、產物摘錄,不寫結論(同樣寫在 prompt 裡,軟約束)。
- 程式題的揭曉:Claude 把產物擺在你的預測旁邊,只標「哪裡不同」的事實,不解釋。你先解釋為什麼不同,Claude 才給它的解釋。
- 遷移題 subagent 拿到:主題、正確答案、你的原始問題。不拿你的解釋。出一題開放式問答,不出選擇題(Bisra:選擇題 g = .24 不顯著)。
- 一個 session 做一題,約 30 到 60 分鐘。遷移題答完、紀錄檔寫完「下次抽問」那一句,session 結束。
- 「下次抽問」由 Claude 提議一句,你可以改。
- skill 與 agent 原始碼放 plugin repo `llm-wiki`(本目錄改名而來),以 Claude Code plugin 或 symlink 裝到 `~/.claude/`,因為 `/study` 要在別的 repo 裡跑。Claude Code 會不會載入 symlink 的 skill 目錄【未證實】,建好後跑一次 `/study` 就知道。
- 不延伸本機已有的 `teach` 或 `learning-companion`:兩者都沒有「預測鎖定後揭曉」與「跑實驗拿證據」這兩步,而且 `teach` 以 HTML 課程為主要產出,方向不同。沿用的是 `teach` 的紀錄檔編號慣例。
- 不做 cron、不做提醒。一週一次靠你自己。
- `LLM_WIKI_DIR` 沒設或目錄不存在時,skill 第一步就停下來告知,不寫到別處。
- 實驗產物超過 200 行只存前後各 50 行加指令,原檔留本機不進 git。`llm-wiki-data` 的 `.gitignore` 擋大檔。
- 兩個 subagent 定義檔同樣放本 repo `agents/`,symlink 到 `~/.claude/agents/`,跟 skill 一樣【載入方式未證實】。
- 開場抽問的來源:主題定了之後,掃 `$LLM_WIKI_DIR/records/<topic>/` 找 `skipped_prediction: true` 的;沒有就取該主題最新一份的「下次抽問」。

## 紀錄檔格式

```markdown
---
format_version: 1
topic: llvm
date: 2026-09-26
kind: experiment | reading | practice
skipped_prediction: false
source: (讀書題填書名與頁碼或 URL;程式題填 repo 與 commit)
---
## 開場抽問
題目;你的原話作答
## 範圍
(你的原話)
## 原文(讀書題)
(段落逐字抄,方便回頭查 Claude 的解釋對不對)
## 已知
(你的原話)
## wiki 對照
(AI 列以前記過的概念與紀錄編號)
## 問題
(你的原話)
## 假設
(你的原話)
## 預測
- 預測 1(信心:高)
## 實驗(AI)
指令、產物路徑
## 揭曉:差異
(Claude 只列事實)
## 你的解釋
(你的原話)
## Claude 的解釋
## 白紙重建
(你的原話,從頭講給不懂的人聽)
### Claude 指出的漏洞
## 心得
(你的原話,可空)
## 遷移題與你的作答
## 下次抽問
一句話
```

## 儲存層:Karpathy 的 LLM Wiki 模式

來源:https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f 。分工原文:「You're in charge of sourcing, exploration, and asking the right questions. The LLM does all the grunt work」,與 take-away 2 一致。

`llm-wiki-data` 的結構:

| 層 | 路徑 | 誰寫 | 規則 |
|---|---|---|---|
| raw | `records/<topic>/NNNN-<slug>.md` 與同名產物資料夾 | 你的段落逐字抄,AI 段落標明 | session 結束後不改 |
| cards | `cards/<topic>.tsv`,Anki 可匯入 | AI | 正面、背面、標籤=紀錄編號 |
| topics | `topics.yaml`,主題對應 repo 路徑或關鍵字 | AI 回寫 | 判主題用 |
| wiki | `wiki/<topic>/<concept>.md` | AI | 每句標出處紀錄編號 |
| schema | `CLAUDE.md` | 你與 AI 共同改 | 頁面格式、命名、矛盾的定義 |
| index / log | `index.md`、`log.md` | AI | 目錄與操作流水 |

三個操作接在流程的位置:
- ingest:`/study` 第 9 步存檔之後,AI 把這份紀錄寫進或更新一到三頁 wiki,更新 index 與 log。也可單獨 `/wiki ingest`。
- query:接在第 1 步「寫已知」**之後**,AI 從 wiki 撈你以前記過的相關內容,差集就是你忘的。寫完已知之前不給 wiki 內容,寫完預測之前不給結論句。
- lint:手動 `/wiki lint`,找頁面矛盾與孤頁;矛盾當「下次抽問」候選。

兩條防「看整理好的答案」的規則:1b 只列以前記過的概念名與紀錄編號,不列結論句;wiki 每句可追回紀錄原話。

## 兩個 repo

| repo | 內容 | 公開 |
|---|---|---|
| `llm-wiki`(plugin,本目錄) | `skills/study`、`skills/wiki`(init / ingest / query / lint)、`agents/`、`template/`(init 用的資料 repo 骨架)、`docs/` | 可 |
| `llm-wiki-data` | 由 `/wiki init` 產生,git 管,多台機器 clone | 私人 |

template 不獨立成 repo:內容不到百行,且格式跟 skill 綁死,分開一定漂掉。

## 流程八步與機制對照

| 步驟 | 機制 | 誰打字 |
|---|---|---|
| 0 判主題 + 開場抽問 | 主對話定主題;只掃 `records/<topic>/`,先找跳過預測的,再找最新一份的「下次抽問」;該主題無紀錄則略過 | 你答 |
| 1 範圍、已知、問題 | 主對話,AskUserQuestion 或自由打字 | 你 |
| 2 假設與預測(附信心) | 主對話;卡住時反問,最多三次 | 你 |
| 3 拿證據 | experiment:subagent 跑實驗;reading:無,跳 4;practice:你動手做,AI 只標事實 | 依 kind |
| 4 揭曉比對 | 主對話,只列差異 | AI |
| 5 你解釋 | 主對話 | 你 |
| 6 Claude 解釋 | 主對話 | AI |
| 7 白紙重建 | 主對話,Claude 不重複前文內容,只指漏洞 | 你 |
| 8 遷移題 | subagent 出題,主對話收答 | AI 出,你答 |
| 9 存檔 | 主對話寫 records | AI |

## 明知的弱點

- 沒有 hook,閘門和最後存檔都靠 Claude 自律。Bastani(SSRN 2024 / PNAS 2025)的失敗案例就是這種。用 `skipped_prediction` 統計次數,超過一定比例再考慮加 hook。
- 實驗 subagent 的「不動原始碼」「只寫實驗目錄」同樣是軟約束,見上節。
- 讀書題的「正解」來自 Claude,可能錯。紀錄檔要留段落原文與出處,方便回頭查。
- 「一週一次」「最多三次反問」都是拍腦袋的數字,沒有研究支持。

## 下一步

1. 本目錄改名 `~/personal/llm-wiki`,git init。寫 `skills/study/SKILL.md` 與 `skills/study/flows/{experiment,reading,practice}.md`、`skills/wiki/SKILL.md`、`agents/study-experimenter.md`、`agents/study-quizzer.md`、`template/`。
2. 用一個真實 LLVM 問題跑一輪,看八步有沒有哪一步太累。
3. 跑三次以後回頭看 `skipped_prediction` 的比例。
