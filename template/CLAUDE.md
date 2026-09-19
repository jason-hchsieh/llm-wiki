# llm-wiki-data

format_version: 1

這個 repo 是 `/study` 與 `/wiki` 的資料層。分工:使用者負責找來源、探索、提問、寫下自己的假設與解釋;AI 負責摘要、交叉引用、歸檔、記帳。

## 目錄

- `records/<topic>/NNNN-<slug>.md`:原始層。一題一份,使用者的段落逐字抄,AI 的段落標明。session 結束後唯讀。這是慣例,沒有工具在擋,靠你遵守。
- `records/<topic>/NNNN-<slug>/`:該題的實驗產物。超過 200 行的原檔放其下 `artifacts-full/`,那個資料夾不進 git。
- `wiki/<topic>/<concept>.md`:AI 維護的概念頁,每句帶紀錄編號 `[<topic>-NNNN]`。
- `cards/<topic>.tsv`:Anki 可匯入的卡片,tab 分隔三欄:正面、背面、標籤(紀錄編號)。
- `topics.yaml`:主題對應工作目錄,`/study` 用來判主題。
- `index.md`:所有 wiki 頁的目錄,每頁一行摘要。
- `log.md`:操作流水,前綴 `ingest` / `query` / `lint`。

## 紀錄檔標題順序(固定)

frontmatter:`format_version, topic, date, kind, skipped_prediction, source`。
標題:開場抽問 / 範圍 / 原文 / 已知 / wiki 對照 / 問題 / 假設 / 預測 / 實驗(AI) / 揭曉:差異 / 你的解釋 / Claude 的解釋 / 白紙重建(含子標題 Claude 指出的漏洞) / 心得 / 遷移題與你的作答 / 下次抽問。
沒內容的段寫「(無)」。

## wiki 頁

frontmatter:`format_version, topic, concept, sources, updated`。段落:結論 / 矛盾 / 相關頁。每句帶紀錄編號。

## 什麼算矛盾

兩句話對同一概念、同一條件,說出相反的結果或機制。矛盾時兩句都留,加「矛盾:[編號] 與 [編號]」,由使用者下次 `/study` 用實驗裁決。

## 格式改版

改版時把這裡的 `format_version` 加一,舊檔不回頭改,`/wiki lint` 對舊版只警告。
