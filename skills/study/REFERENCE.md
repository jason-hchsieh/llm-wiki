# /study 參考:檔案格式

三個檔案格式的單一來源。`llm-wiki-data/CLAUDE.md`(由 template 產生)複製同一份定義,改這裡要一起改。

## 紀錄檔 `records/<topic>/NNNN-<slug>.md`

- `NNNN`:該主題目錄內最大編號加一,四位數補零。
- `<slug>`:小寫英文與連字號,描述題目,例如 `instcombine-add-fold`。
- 產物放同名資料夾 `records/<topic>/NNNN-<slug>/`。超過 200 行的原檔放 `records/<topic>/NNNN-<slug>/artifacts-full/`,那個資料夾被 `.gitignore` 擋掉。
- session 結束後不再改。

標題順序固定,一個都不省略;沒內容寫「(無)」。標「原話」的段落逐字抄使用者的字。

```markdown
---
format_version: 1
topic: llvm
date: 2026-09-26
kind: experiment
skipped_prediction: false
source: llvm-project@abc1234 或 書名 p.123 或 URL
---
## 開場抽問
題目:…
作答(原話):…
## 範圍
(原話)
## 原文
(reading 題:段落逐字抄。其他 kind 寫「(無)」)
## 已知
(原話)
## wiki 對照
- 以前記過但這次沒寫到:… [0003]
## 問題
(原話)
## 假設
(原話)
## 預測
- 預測 1(信心:高)
- 預測 2(信心:低)
## 實驗(AI)
指令、產物路徑、節錄
## 揭曉:差異
- 預測 1:不同。預測 X / 實際 Y(產物第 N 行)
- 預測 2:相符
## 你的解釋
(原話)
## Claude 的解釋
…
## 白紙重建
(原話)
### Claude 指出的漏洞
- …
## 心得
(原話,可空)
## 遷移題與你的作答
題目:…
作答(原話):…
對照:…
## 下次抽問
一句話
```

## `topics.yaml`

主題對應工作目錄。第 0 步用目前目錄比對 `paths`(前綴比對即可),命中就不問。

```yaml
llvm:
  paths:
    - /Users/me/src/llvm-project
  keywords: [llvm, mlir]
japanese:
  paths: []
  keywords: [日文, jlpt]
```

## `cards/<topic>.tsv`

Anki 可直接匯入的 tab 分隔檔,無標題列,三欄:正面、背面、標籤。標籤放紀錄編號,例如 `llvm-0003`。一張卡一行,欄內換行改成 `<br>`。

```
instcombine 對 add x, 0 做什麼	折成 x;見 InstCombineAddSub.cpp visitAdd	llvm-0003
```

進卡片的三個入口:高信心且錯的預測、每份紀錄的「下次抽問」、使用者明說「這個要記」。
