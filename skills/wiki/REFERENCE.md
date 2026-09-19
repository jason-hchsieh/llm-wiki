# /wiki 參考:資料 repo 格式

`format_version: 1`。紀錄檔格式在 `skills/study/REFERENCE.md`,此處只列 wiki 層。

## 目錄

```
llm-wiki-data/
├── CLAUDE.md        schema 層,格式與規則,人與 AI 共同改
├── records/<topic>/NNNN-<slug>.md      原始層,唯讀(慣例,沒有工具在擋,靠你遵守)
├── records/<topic>/NNNN-<slug>/        產物
├── wiki/<topic>/<concept>.md           AI 維護
├── cards/<topic>.tsv                   Anki 可匯入
├── topics.yaml                         主題對應目錄
├── index.md                            所有 wiki 頁的目錄
└── log.md                              操作流水
```

## wiki 頁 `wiki/<topic>/<concept>.md`

```markdown
---
format_version: 1
topic: llvm
concept: instcombine-add-fold
sources: [llvm-0003, llvm-0007]
updated: 2026-10-03
---
# instcombine 對 add 的折疊

## 結論
- `add x, 0` 折成 `x`,在 visitAdd 開頭處理。[llvm-0003]
- 常數在右邊時才匹配,canonicalize 先把常數搬到右邊。[llvm-0007]

## 矛盾
- 矛盾:[llvm-0003] 說 -O0 也會跑,[llvm-0007] 說 -O0 不跑 instcombine。

## 相關頁
- [[canonicalization]]
```

規則:每句帶紀錄編號;矛盾兩句都留;沒有矛盾時「矛盾」段寫「(無)」。

## `index.md`

按主題分節,每頁一行:路徑加一句摘要。

```markdown
# index

## llvm
- wiki/llvm/instcombine-add-fold.md:add 與常數 0 的折疊規則與觸發條件
```

## `log.md`

一行一筆,前綴固定,方便 `grep`:

```
2026-10-03 ingest records/llvm/0007-instcombine-canon.md → wiki/llvm/instcombine-add-fold.md, wiki/llvm/canonicalization.md
2026-10-03 query "add 常數折疊在哪個 pass" → 2 頁
2026-10-05 lint 3 issues
```
