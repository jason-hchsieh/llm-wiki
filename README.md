# llm-wiki

一個 Claude Code plugin,兩個 skill:

- `/study <主題>`:一次一題的深度學習流程。你先寫已知、假設、預測,AI 才去拿證據;揭曉只列差異,你先解釋,AI 再解釋;然後白紙重建、遷移題、存檔。
- `/wiki init | ingest | query | lint`:用 Karpathy 的 LLM Wiki 模式維護紀錄。原始紀錄唯讀,AI 把結論編進概念頁,每句帶出處。

設計依據與研究證據在 `docs/design.md` 與 `docs/research/`。

## 安裝

三條路,選一條。

**M. 從 marketplace 裝(不用手動 clone)**

```bash
claude plugin marketplace add jason-hchsieh/llm-wiki
claude plugin install llm-wiki@llm-wiki
```

第一行把這個 repo 登記成 marketplace(放 plugin 清單的地方),第二行從裡面裝 `llm-wiki`。名字和 A 路一樣,帶 `llm-wiki:` 前綴。repo 需要是你連得到的(私有 repo 要有 GitHub 存取權)。

**A. 當 plugin 用(skill 名字會帶前綴)**

```bash
claude --plugin-dir ~/personal/llm-wiki
```

skill 會叫 `/llm-wiki:study` 與 `/llm-wiki:wiki`,agent 叫 `llm-wiki:study-experimenter`。不想每次加參數,把整個 repo 放進(或 symlink 到)`~/.claude/skills/llm-wiki/`,下次啟動會自動當 plugin 載入,名字是 `llm-wiki@skills-dir`。symlink 這條官方文件沒明說,試一次 `/help` 看有沒有列出來就知道。

**B. 當一般 skill 用(名字不帶前綴)**

```bash
ln -s ~/personal/llm-wiki/skills/study ~/.claude/skills/study
ln -s ~/personal/llm-wiki/skills/wiki  ~/.claude/skills/wiki
ln -s ~/personal/llm-wiki/agents/study-experimenter.md ~/.claude/agents/
ln -s ~/personal/llm-wiki/agents/study-quizzer.md      ~/.claude/agents/
```

skill 目錄可以是 symlink,官方文件有寫。agent 檔的 symlink 沒寫,試了才知道。

## 測過與沒測過的(2026-09-19)

測過:`--plugin-dir` 載入、`/wiki init`、`/study` 第 0 步(含 `topics.yaml` 回寫)、`/wiki ingest` / `lint` / `query` 用一份假紀錄。
沒測過:B 路 symlink 安裝、`~/.claude/skills/llm-wiki/` 自動載入、兩個 subagent 在真實題目上跑、`.gitignore` 擋大產物。
模型:`/study` 用 opus 以上。haiku 跑的時候會漏掉 `topics.yaml` 回寫。

## 第一次使用

1. 建資料 repo:`/wiki init ~/llm-wiki git@github.com:<你>/llm-wiki-data.git`。它把 `template/` 複製過去、`git init`、接上 remote 並推第一個 commit。已有 remote 的話,其他機器直接 `git clone` 到 `~/llm-wiki`,不要再 init。
2. 在 shell 設定,例如 `.zshrc`:`export LLM_WIKI_DIR=~/llm-wiki`。多台機器把同一個 repo clone 到相同路徑。
3. 到你要研究的 repo 裡開 Claude Code,打 `/llm-wiki:study llvm`(A 路裝成 plugin 時的名字;沒有同名衝突時裸名 `/study` 也可用。B 路 symlink 裝的就是 `/study llvm`)。第一次它會問「目前目錄對應哪個主題」,答完寫進 `topics.yaml`,之後在同一個目錄就不問。
4. 每題結束會自動 ingest。想健檢時打 `/wiki lint`。

## 目錄

```
.claude-plugin/plugin.json
skills/study/SKILL.md          流程主體
skills/study/flows/            experiment / reading / practice 的第 3 到 4 步
skills/study/REFERENCE.md      紀錄檔、topics.yaml、cards.tsv 格式
skills/wiki/SKILL.md           init / ingest / query / lint
skills/wiki/REFERENCE.md       wiki 頁、index、log 格式
agents/study-experimenter.md   跑實驗,看不到你的預測
agents/study-quizzer.md        出遷移題,看不到你的解釋
template/                      /wiki init 複製的資料 repo 骨架
docs/                          設計與研究
```

## 名詞

- plugin:Claude Code 可整包載入的資料夾,裡面有 skill、agent 等。
- skill:一份 `SKILL.md`,寫「某件事怎麼做」,用 `/名字` 啟動。
- subagent:另開的 Claude 實例,有自己的 context,看不到主對話。這裡用它做盲測。
- ingest:把一份紀錄的結論編進 wiki 概念頁。
