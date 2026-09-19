# kind: experiment(第 3 到 4 步)

## 第 3 步:拿證據

呼叫 `study-experimenter` agent(以 plugin 安裝時名稱是 `llm-wiki:study-experimenter`)。

給它:
- 主題,以及第 1 步定好的編號與 slug(它用來決定產物目錄 `$LLM_WIKI_DIR/records/<topic>/NNNN-<slug>/`)
- repo 路徑與 commit
- 第 1 步的「問題」原文
- 要變動的條件:例如「同一段 IR 在 -O0 與 -O2 各跑一次 `opt -passes=instcombine`,存前後 IR」

留在你這邊,不給它:
- 使用者的假設與預測
- 你自己對結果的猜想
- wiki 內容

agent 回來的東西只有三種:執行過的指令、產物路徑、產物摘錄。它若寫了結論,丟掉結論那段,只留事實。

完成條件:每個要變動的條件都有對應產物路徑。

## 第 4 步:揭曉

把每條預測與對應產物摘錄並排。差異用「預測:… / 實際:…」寫,實際那欄引產物的具體行。產物超過 200 行照節錄規則抄。
