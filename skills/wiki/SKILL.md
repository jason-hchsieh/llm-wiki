---
name: wiki
description: 維護 llm-wiki-data 這個 Karpathy 式知識庫:init 建資料 repo、ingest 把一份學習紀錄編進 wiki、query 撈相關頁、lint 健檢。只在使用者手動輸入 /wiki 或 /study 流程內部需要時執行。
argument-hint: "init <path> | ingest [record] | query <question> | lint"
---

# /wiki $ARGUMENTS

資料 repo 的路徑來自環境變數 `LLM_WIKI_DIR`。除了 `init`,其餘子指令在變數未設或目錄不存在時停止並告知。
頁面、index、log 的格式在 [REFERENCE.md](REFERENCE.md)。資料 repo 自己的 `CLAUDE.md` 是同一份格式的副本,以 `format_version` 對齊。

分工(Karpathy):使用者負責找來源、探索、問問題;你負責摘要、交叉引用、歸檔、記帳。`records/` 是原始層,唯讀。這是慣例,沒有工具在擋,靠你遵守。

## init <path>

1. `<path>` 已存在且非空 → 停止,告知。
2. 把 template 整個複製到 `<path>`。template 位置:plugin 安裝時是 `${CLAUDE_PLUGIN_ROOT}/template`;以 symlink 裝成一般 skill 時是 `${CLAUDE_SKILL_DIR}/../../template`。兩個都試,先找到的用。`${CLAUDE_PLUGIN_ROOT}` 只在以 plugin 安裝時會被替換;symlink 成一般 skill 時只有 `${CLAUDE_SKILL_DIR}/../../template` 那條有效。
3. 在 `<path>` 執行 `git init`,提交一次「init llm-wiki-data」。
4. 告訴使用者在 shell 設 `export LLM_WIKI_DIR=<path>`,多台機器則 clone 同一個 repo 到相同路徑。

完成條件:`<path>/CLAUDE.md`、`records/`、`wiki/`、`cards/`、`index.md`、`log.md`、`topics.yaml` 都在,且有一個 commit。

## ingest [record]

`record` 省略時取 `records/**/` 下 `log.md` 尚未記錄的紀錄,由舊到新逐份做。

每份紀錄:
1. 讀完整份。抽出「Claude 的解釋」「揭曉:差異」「白紙重建」「遷移題」裡的結論,每條結論標紀錄編號 `[<topic>-NNNN]`。
2. 決定它屬於哪些概念頁 `wiki/<topic>/<concept>.md`,一到三頁。已有的頁就更新,沒有就新建。概念名用小寫連字號英文。
3. 寫入時每一句都帶紀錄編號。與頁上既有句子矛盾時,兩句都留,加上「矛盾:[編號] 與 [編號]」一行,不擅自裁決。
4. 更新 `index.md` 該頁的一行摘要;新頁加一行。
5. `log.md` 追加一行 `ingest`。

完成條件:紀錄裡每個結論都能在某頁找到,並帶編號。

## query <question>

只做兩件事:在 `wiki/<topic>/` 與 `index.md` 找相關頁,列出相關句子與其紀錄編號。不合成新答案、不補充 wiki 沒有的內容。
在 `/study` 第 1b 步被呼叫時,輸出限制為「使用者這次已知裡沒寫到的項目」。
`log.md` 追加一行 `query`。

## lint

檢查並輸出清單,不自動修:
- 紀錄檔與 wiki 頁 frontmatter 缺欄位、標題順序與 REFERENCE.md 不符。`format_version` 比目前小的只警告。
- 孤頁:`index.md` 沒列的 wiki 頁;死鏈:index 列了但檔案不在。
- 無出處句:wiki 頁中沒帶 `[<topic>-NNNN]` 的句子。
- 矛盾:頁內標了「矛盾」的行,以及同一概念在兩頁說法相反的句子。
- 每個矛盾建議一句「下次抽問」,附到輸出末尾供使用者挑。

`log.md` 追加一行 `lint`。
