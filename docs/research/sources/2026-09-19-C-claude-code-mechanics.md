# Claude Code 機制調查：能不能做出「回答前先要求使用者寫下預測」的學習流程

調查日期：2026-09-19。全部內容來自實際抓取的官方文件，不依賴記憶。

## 0. 文件實際 URL（重要）

- 起點 `https://docs.anthropic.com/en/docs/claude-code/` 回傳 **301 Moved Permanently**，轉址到 `https://code.claude.com/docs/en/`。以下一律使用轉址後的網域。
- 文件地圖（docs map，官方頁面索引）：<https://code.claude.com/docs/en/claude_code_docs_map.md>；完整索引另有 <https://code.claude.com/docs/llms.txt>。
- **Hooks 有兩頁**：指南 <https://code.claude.com/docs/en/hooks-guide.md>，參考手冊（完整 schema）<https://code.claude.com/docs/en/hooks.md>。
- **`slash-commands.md` 已不是獨立頁**：`https://code.claude.com/docs/en/slash-commands.md` 與 `skills.md` 抓下來位元組數完全相同（115336 bytes），內容就是 Skills 頁。

**方法學提醒（法則 4，說出不一致）**：WebFetch 的摘要是模型產生的，本次調查抓到它**寫錯 hooks 的 JSON 巢狀結構**（見 §3.4）。以下所有 schema 以 `curl` 下來的原始 markdown 逐字為準。

---

## 1. Skills（技能）

> Skill＝一個資料夾裝一份 `SKILL.md`，裡面是「某個工作怎麼做」的指示，可被使用者用 `/名字` 叫出來，也可被 Claude 自動載入。

來源：<https://code.claude.com/docs/en/skills.md>

### 1.1 Frontmatter 欄位（frontmatter＝檔案開頭 `---` 之間的 YAML 設定區）

逐字摘自 skills.md 的 Frontmatter reference 表格：

| 欄位 | 文件原文重點 |
|---|---|
| `name` | 顯示名稱，預設為資料夾名 |
| `description` | 「What the skill does and when to use it. Claude uses this to decide when to apply the skill.」——**自動觸發的判斷依據** |
| `when_to_use` | 追加觸發語句／範例請求，附加在 `description` 後 |
| `argument-hint` | 「Hint shown during autocomplete to indicate expected arguments. Example: `[issue-number]`」 |
| `arguments` | 具名位置參數，供 `$name` 代換 |
| `disable-model-invocation` | 「Set to `true` to prevent Claude from automatically loading this skill. Use for workflows you want to trigger manually with `/name`.」預設 `false` |
| `user-invocable` | 「Set to `false` when only Claude should invoke the skill… Default: `true`.」 |
| `allowed-tools` | 「Tools Claude can use without asking permission during the turn that invokes this skill. The grant clears when you send your next message.」 |
| `disallowed-tools` | 「Tools removed from Claude's available pool while this skill is active. Use for autonomous skills that should never call certain tools, such as `AskUserQuestion` for a background loop.」同樣「下一則訊息就解除」 |
| `model` | 該 skill 生效期間使用的模型（只在本回合） |
| `effort` | 覆寫 session 的 effort level |
| `context` | 「Set to `fork` to run in a forked subagent context.」 |
| `agent` | `context: fork` 時使用哪一種 subagent |
| `background` | 只在 `context: fork` 時有效；`false`＝在本回合等結果。預設 `true`（v2.1.218+） |
| `hooks` | 「Hooks that Claude Code registers when the skill is invoked and keeps running for the rest of the session.」——**skill 可以自己註冊 hook** |
| `paths` | glob，限定「只有在處理符合的檔案時才自動載入」 |
| `shell` / `metadata` / `license` / `compatibility` | shell 選 bash/powershell；後三者 Claude Code 不作用 |

（skills.md 行 336–352）

### 1.2 載入時內容會進主對話 context 嗎？會。

> 「When you or Claude invoke a skill, the rendered `SKILL.md` content enters the conversation as a single message and stays there across later turns. This persistence applies to the skill's instructions, not its permissions… Claude Code does not re-read the skill file on later turns, so write guidance that should apply throughout a task as standing instructions rather than one-time steps.」（skills.md 行 514）

另外：平時只有 **description** 進 context，全文要被叫用才載入。

### 1.3 能被模型自動觸發嗎？能，且可關閉。

預設 Claude 可自動載入；`disable-model-invocation: true` 只留給使用者用 `/名字` 叫；`user-invocable: false` 只留給 Claude 叫。

### 1.4 能限制工具嗎？能（雙向）。

`allowed-tools`＝本回合免詢問；`disallowed-tools`＝本回合直接從工具池移除。兩者都在**使用者送出下一則訊息時失效**——這對「閘門」設計是關鍵限制：skill 的工具限制**撐不過一個回合**。

### 1.5 「獨立 context 執行」選項：`context: fork`

- 「Set to `fork` to run in a forked subagent context.」
- sub-agents.md 行 591 明確說：「With `context: fork` in a skill, the skill content is injected into the agent you specify. **In both cases the subagent starts without your conversation history.**」
- 注意術語衝突：skill 的 `context: fork` 產生的是**不繼承對話歷史**的 subagent；而 sub-agents.md 的「Fork the current conversation」（`/subtask`）是**繼承整段對話**。兩者名字都叫 fork，行為相反，見 §2.5。

### 1.6 與 slash commands 的關係：已合併

> 「**Custom commands have been merged into skills.** A file at `.claude/commands/deploy.md` and a skill at `.claude/skills/deploy/SKILL.md` both create `/deploy` and work the same way. Your existing `.claude/commands/` files keep working.」（skills.md 行 16）

`.claude/commands/` 是舊格式，「supports the same frontmatter except `name` and `paths`」（行 131）。命名規則：`.claude/skills/deploy-staging/SKILL.md` → `/deploy-staging`；plugin 的 → `/my-plugin:review`。

---

## 2. Sub-agents（子代理）

> Subagent＝一個獨立開的 Claude 實例，有自己的 context window、system prompt、工具集，做完把摘要交回主對話。

來源：<https://code.claude.com/docs/en/sub-agents.md>

### 2.1 Frontmatter 欄位

`name`（必填）、`description`（必填，決定何時委派）、`tools`（允許清單，省略則繼承）、`disallowedTools`（拒絕清單）、`model`（`sonnet`/`opus`/`haiku`/`fable`/完整 ID/`inherit`）、`permissionMode`（`default`/`acceptEdits`/`auto`/`dontAsk`/`bypassPermissions`/`plan`，以及 `manual` 作為 `default` 的別名，需 v2.1.200+，plugin subagent 會忽略此欄位；sub-agents.md 行 299）、`maxTurns`、`skills`（啟動時預載 skill 內容）、`mcpServers`、`hooks`、`memory`（`user`/`project`/`local`）、`background`、`omitClaudeMd`、`effort`、`isolation`（`worktree`）、`color`、`initialPrompt`、`experimental`。

### 2.2 是獨立 context 嗎？是。

> 「Each subagent starts with a fresh, isolated context window. It doesn't see your conversation history, the skills you've already invoked, or the files Claude has already read. Claude composes a delegation message that summarizes the task, and the subagent works from there. The exception is a fork…」（sub-agents.md 行 1035）

**對學習流程的意義**：subagent 天生「看不到使用者剛寫下的預測」，正好適合做**盲測**（出題者不知道學習者猜了什麼）。

### 2.3 能不能中途向使用者提問、來回對話？**不能**（非 fork 的 subagent）

文件最明確的一句，在「Available tools」段：

> 「The first filter removes these tools, even when listed in the `tools` field:
> * `Agent`, when the subagent is at the depth limit…
> * **`AskUserQuestion`**
> * `EndConversation`…
> * `EnterPlanMode`、`ExitPlanMode`（除非 permissionMode 是 plan）、`ScheduleWakeup`、`TaskOutput`、`WaitForMcpServers`」（sub-agents.md 行 409–418）

`AskUserQuestion` 就是 Claude 向使用者提多選題的工具（tools-reference.md 行 125–137：「Claude uses `AskUserQuestion` to ask you multiple-choice questions when it needs a decision or a clarification.」）。它被**無條件移除**，即使你在 `tools` 裡列出來也一樣。

> 【推論，非逐字】文件沒有一句話寫「subagent 不能跟使用者對話」，但「`AskUserQuestion` 被第一層過濾器移除」是文件層級最直接的證據。**唯一例外是 fork**（行 409：「Forks skip both filters and receive the main conversation's exact tool pool.」），因此 fork 應當保有 `AskUserQuestion`——但文件**沒有明說 fork 可以向使用者提問**，這點標【未證實】。另外權限提示：fork 的「Prompts surface in your terminal」（行 1156 表格）。

補充：背景執行的 subagent 還有第二層過濾（行 421），只保留 Read/Grep/Glob/Bash/Edit/Write/WebFetch/WebSearch/TodoWrite/Skill 等一組內建工具。

### 2.4 結果怎麼回主對話？只回摘要。

> 「the subagent does that work in its own context and returns only the summary」（行 9）
> 「the verbose output stays in the subagent's context while only the relevant summary returns to your main conversation」（行 940）

**可以續談**：`SendMessage` 工具可讓 Claude 對已完成的 subagent 再送訊息，「the subagent resumes in the background without a new `Agent` invocation」（行 1080）。但這是 **Claude 對 subagent**，不是 subagent 對使用者。

### 2.5 Fork（`/subtask`）與一般 subagent 的差別

sub-agents.md 行 1127、1156–1168：

| | Fork | 非 fork subagent |
|---|---|---|
| Context | 完整對話歷史 | 全新 context，只有傳入的 prompt |
| System prompt / 工具 | 與主 session 相同 | 來自定義檔，背景執行再過濾 |
| Model | 與主 session 相同 | 來自 `model` 欄位 |
| Prompt cache | 與主 session 共用 | 獨立 |

「A fork is a subagent that inherits the entire conversation so far instead of starting fresh… The fork's own tool calls still stay out of your conversation and only its final result comes back.」指令是 `/subtask`（v2.1.212+；v2.1.161–2.1.211 叫 `/fork`）。

### 2.6 能限制工具嗎？能。

`tools: Read, Grep, Glob, Bash` 白名單；`disallowedTools: Write, Edit` 黑名單；`mcp__<server>` 整台 MCP server 一次擋掉；`tools: Agent(worker, researcher)` 限制可生成哪些子代理。

---

## 3. Hooks（掛鉤）

> Hook＝在 Claude Code 生命週期的某個時點自動執行的東西（shell 指令／HTTP／或交給模型判斷），提供「不靠 LLM 自願」的確定性控制。

來源：指南 <https://code.claude.com/docs/en/hooks-guide.md>、參考 <https://code.claude.com/docs/en/hooks.md>

### 3.1 事件清單（hooks.md 原始表格逐行取出，33 個）

`SessionStart`、`Setup`、`UserPromptSubmit`、`UserPromptExpansion`、`PreToolUse`、`PermissionRequest`、`PermissionDenied`、`PostToolUse`、`PostToolUseFailure`、`PostToolBatch`、`Notification`、`MessageDisplay`、`SubagentStart`、`SubagentStop`、`TaskCreated`、`TaskCompleted`、`Stop`、`StopFailure`、`TeammateIdle`、`InstructionsLoaded`、`ConfigChange`、`CwdChanged`、`DirectoryAdded`、`FileChanged`、`WorktreeCreate`、`WorktreeRemove`、`PreCompact`、`PostCompact`、`PreModelSwitch`、`PostModelSwitch`、`Elicitation`、`ElicitationResult`、`SessionEnd`。

（此清單由 hooks.md 事件表格逐行擷取；WebFetch 的摘要漏了 `SessionEnd`。）

生命週期（hooks.md 行 22）：「per turn: `UserPromptSubmit`, `Stop`, and `StopFailure`」。

### 3.2 Exit code 語意（hooks-guide.md 行 602–611）

- **Exit 0**：不表示意見。對 `PreToolUse` 而言**不等於批准**，正常權限流程照跑。對 `UserPromptSubmit`、`UserPromptExpansion`、`SessionStart`、`PostModelSwitch`，**stdout 會被當作純文字加進 Claude 的 context**。
- **Exit 2**：阻擋。理由寫 stderr。「exit 2 blocks whether or not you print JSON: even a JSON `permissionDecision` of `"allow"` can't override it.」每事件效果不同（hooks.md 行 884：`UserPromptSubmit` 是「Blocks prompt processing and **erases the prompt**」）。有些事件不可擋（如 `SessionStart`，exit 2 只把 stderr 顯示給使用者然後繼續）。
- **其他 exit code**：若 stdout 是通過驗證的 JSON，就以 JSON 為準、忽略 exit code；否則視為 non-blocking error，動作照常進行。

### 3.3 文字注入 context

- `SessionStart`：`hookSpecificOutput.additionalContext`（hooks.md SessionStart 表）；另有 `initialUserMessage`（在 `-p` 模式下成為第一個 user turn）、`sessionTitle`。
- `UserPromptSubmit`：`additionalContext`「String added to Claude's context alongside the submitted prompt」。
- `PostToolUse`：`additionalContext`，並可用 `updatedToolOutput` **替換工具輸出**。
- 注入形式：「Plain stdout and the `additionalContext` value are each injected as a system reminder that starts with the hook's name; Claude reads both. **Neither channel produces a visible transcript entry.**」（hooks.md 行 1376 附近）
- 限制（hooks-guide 行 952）：「Command hooks communicate through stdout, stderr, and exit codes only. **They can't trigger `/` commands or tool calls.**」

### 3.4 逐事件 decision schema（**這裡 WebFetch 摘要出錯，以下為原文**）

hooks.md 行 1047 的總表原文：

> 「`UserPromptSubmit`, `UserPromptExpansion`, `PostToolUse`, `PostToolUseFailure`, `PostToolBatch`, `Stop`, `SubagentStop`, `ConfigChange`, `PreCompact` → **Top-level `decision`** → `decision: "block"`, `reason`. Stop and SubagentStop also accept `hookSpecificOutput.additionalContext`…」

也就是 `decision` / `reason` 是**最上層**欄位，只有 `additionalContext` 巢狀在 `hookSpecificOutput` 底下。（WebFetch 的摘要把 `decision` 錯放進 `hookSpecificOutput`，已更正。）

`PreToolUse` 則用 `hookSpecificOutput.permissionDecision`（`allow`/`deny`/`ask`）＋`permissionDecisionReason`（hooks-guide 行 621–643）。多個 hook 同時觸發時「the most restrictive answer applies, in the order `deny`, `defer`, `ask`, `allow`」（行 529）。

`PreToolUse` 的強度（hooks-guide 行 967）：「`PreToolUse` hooks fire before any permission-mode check, in every permission mode, including `dontAsk`. A hook that returns `permissionDecision: "deny"` blocks the tool even in `bypassPermissions` mode…」

### 3.5 `UserPromptSubmit` 能不能擋掉使用者的 prompt 並回訊息？**能，但訊息不進 context。**

hooks.md 行 1368–1385 逐字：

| 欄位 | 原文 |
|---|---|
| `decision` | 「`"block"` prevents the prompt from being processed and **erases it from context**. Omit to allow the prompt to proceed」 |
| `reason` | 「**Shown to the user** when `decision` is `"block"`. **Not added to context**」 |
| `additionalContext` | 「String added to Claude's context alongside the submitted prompt」 |
| `sessionTitle` | 設定 session 標題 |
| `suppressOriginalPrompt` | `block` 時省略顯示原 prompt 文字 |

JSON 形狀（原文範例）：

```json
{
  "decision": "block",
  "reason": "Explanation for decision",
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "My additional context here",
    "sessionTitle": "My session title"
  }
}
```

輸入端：「UserPromptSubmit hooks receive the `prompt` field containing the text the user submitted.」（行 1355）→ **hook 看得到使用者打了什麼字**，不是只能看檔案。

其他關鍵限制：
- 預設 timeout 只有 **30 秒**（行 1347），逾時「is canceled and its output, including any `additionalContext`, is discarded. **The prompt still reaches Claude without that context**」（行 1349）——**逾時是 fail-open**。
- 行 1067：「`UserPromptSubmit`: **can't replace the prompt**; it only injects `additionalContext` alongside it」。

### 3.6 `UserPromptExpansion`：擋住 `/指令` 的正確事件

hooks.md UserPromptExpansion 段原文：

> 「Runs when a user-typed command expands into a prompt before reaching Claude. Use this to **block specific commands from direct invocation**, inject context for a particular skill… For example, a hook matching `deploy` can **block `/deploy` unless an approval file is present**…
> This event covers the path `PreToolUse` doesn't: a `PreToolUse` hook matching the `Skill` tool fires only when Claude calls the tool, but **typing `/skillname` directly bypasses `PreToolUse`**. `UserPromptExpansion` fires on that direct path.」

→ 想擋「使用者直接打 `/answer`」，必須用 `UserPromptExpansion`，`PreToolUse` 擋不到。

### 3.7 `Stop`：可以不讓 Claude 結束回合

hooks.md 行 2610–2629：

| 欄位 | 原文 |
|---|---|
| `decision` | 「`"block"` prevents Claude from stopping. Omit to allow Claude to stop」 |
| `reason` | 「Required when `decision` is `"block"`. Tells Claude why it should continue」 |
| `hookSpecificOutput.additionalContext` | 「Non-error feedback for Claude. The conversation continues so Claude can act on it, but unlike `decision: "block"` it is shown in the transcript as **hook feedback rather than a hook error**」 |

防迴圈保護：`stop_hook_active` 輸入欄位 ＋ **連續 8 次續跑上限**（行 2629）。
限制（hooks-guide 行 962）：「`Stop` hooks fire whenever Claude finishes responding, **not only at task completion**. They don't fire on user interrupts.」

### 3.8 **判斷：「回答前先要求使用者寫下預測」這種閘門，hook 能做到什麼程度**

#### (a) `type: "command"`（shell script）——任務前提所說的那種

**能做到**：
1. 在 `UserPromptSubmit` 檢查「預測檔」是否存在／是否非空／是否符合格式（正規表達式、行數、是否含關鍵欄位）。不合格 → `decision: "block"` ＋ `reason`，prompt 被抹掉，使用者看到訊息。
2. 直接 regex 使用者這次打的字（`prompt` 欄位），例如偵測到「答案是什麼」而預測檔為空就擋。
3. 合格時用 `additionalContext` 把預測內容注入，讓 Claude 在同一回合看到。
4. 在 `UserPromptExpansion` 擋住 `/answer` 之類的直接指令（「unless an approval file is present」正是官方示範）。
5. 在 `Stop` 用 `decision: "block"` 逼 Claude 在沒寫紀錄檔前不准收工。

**做不到（明確限制，請寫進設計文件）**：
- **shell script 無法判斷「這個預測是否合理／是否真的是一個預測」**。它只能做確定性字串／檔案檢查：存在、非空、長度、格式、關鍵字。文件把 hook 定位為「deterministic control」，並明說要判斷就換別種 hook（hooks-guide 行 11）。
- 使用者可以寫 `預測：我不知道` 通過檢查——字面檢查無法分辨敷衍。
- **不能觸發 `/` 指令或工具呼叫**（行 952），所以 hook 不能「主動彈出一個提問框」。
- `UserPromptSubmit` 的 block **抹掉 prompt**，使用者要重打一次；`reason` **不進 context**，Claude 不知道發生過這件事。
- **30 秒逾時且 fail-open**：逾時後 prompt 照樣送達、`additionalContext` 被丟掉。
- **hook 的作用範圍要分清楚（先前誤記，已更正）**：settings 層的工具事件 hook **會**在 subagent 的工具呼叫上觸發。hooks-guide 行 961 原文：Background subagents can't show a prompt in non-interactive mode. **Claude Code still runs the hooks for their tool calls**, and if no hook returns a decision, it denies the call. 真正綁在主 session 的是 `UserPromptSubmit`（只對使用者送出的 prompt 觸發）；subagent 結束用的是另一個事件 `SubagentStop`，不是 `Stop`。subagent／skill 的 `hooks` 欄位是額外註冊，不是唯一途徑。

#### (b) `type: "prompt"` 與 `type: "agent"`——**任務前提的半個反例**

hooks-guide.md 行 845 起明確記載，**hook 不只有 shell script**：

> 「For decisions that require judgment rather than deterministic rules, use `type: "prompt"` hooks. Instead of running a shell command, Claude Code sends your prompt and the hook's input data to a Claude model, **Haiku by default**, to make the decision.」

模型只回 JSON：`ok` / `reason`。`type: "agent"` 更強：「spawn a subagent that can read files, search code, and use other tools to verify conditions before returning a decision」，預設 timeout 60 秒、最多 50 個 tool-use turn。

各事件 `ok: false` 的後果（行 851–854）：
- `Stop`/`SubagentStop`：`reason` 回饋給 Claude 讓它繼續做，除非同時回 `"impossible": true`。
- `PreToolUse`：拒絕該次工具呼叫；預設**結束回合**並在聊天顯示警告行，設 `continueOnBlock: true` 才把 `reason` 當工具錯誤回給 Claude 讓它調整。
- `PostToolBatch`、**`UserPromptSubmit`**、`UserPromptExpansion`：「the turn ends and the `reason` appears in the chat as a warning line」。

**結論（誠實版）**：「hook 只能做格式檢查、不能判斷預測是否合理」對 **command hook 正確**，但對 **prompt / agent hook 不正確**——官方文件確實提供由 Haiku（或子代理）做判斷的閘門。代價是：每則 prompt 多一次模型呼叫、`UserPromptSubmit` 上只有 30 秒、被擋時 prompt 被抹掉且理由不進 context、而且這個判斷本身是 LLM（可被說服、也會誤判）。**真正的硬限制不是「不能判斷」，而是「判斷者也是 LLM，且擋下來的代價是使用者的 prompt 消失」。**

---

## 4. Memory（記憶）

來源：<https://code.claude.com/docs/en/memory.md>

### 4.1 CLAUDE.md 層級（memory.md 行 60–62）

| 層級 | 路徑 | 用途 | 影響誰 |
|---|---|---|---|
| **User instructions** | `~/.claude/CLAUDE.md` | 所有專案的個人偏好 | 只有你（所有專案）|
| **Project instructions** | `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 團隊共享 | 團隊（透過版控）|
| **Local instructions** | `./CLAUDE.local.md` | 個人的專案偏好，要加進 `.gitignore` | 只有你（本專案）|

另有組織的 **managed CLAUDE.md**（行 340 提到「your organization's managed `CLAUDE.md`」，但**本頁未給出其路徑**→【未證實】，需查 managed-settings 頁）。

載入順序（行 130–134）：從工作目錄往上每一層的 `CLAUDE.md`＋`CLAUDE.local.md` 都載入並**串接**（不是覆蓋），由檔案系統根目錄往下排序，所以離你啟動處最近的最後被讀；同一層 `CLAUDE.local.md` 排在 `CLAUDE.md` 之後。子目錄的檔案**不在啟動時載入**，而是「included when Claude reads files in those subdirectories」。

建議大小（行 82）：「target under 200 lines per CLAUDE.md file. Longer files consume more context and reduce adherence.」

### 4.2 `@` import 語法（行 96–125）

- 「CLAUDE.md files can import additional files using `@path/to/import` syntax. Imported files are expanded and loaded into context **at launch**.」
- 相對與絕對路徑皆可；相對路徑相對於**含有該 import 的檔案**，不是工作目錄。
- 可遞迴 import，**最多 4 跳（four hops）**。
- 「Import parsing skips Markdown code spans and fenced code blocks.」寫反引號包住的 `` `@README` `` 不會被 import。
- **外部 import 要核准**：專案層檔案 import 到工作目錄之外時，第一次會跳核准對話框；拒絕後就永久停用且不再詢問。使用者層檔案（`~/.claude/CLAUDE.md`、`~/.claude/rules/`）則直接信任（Cowork session 除外）。

### 4.3 Auto memory（自動記憶）

- 「**Auto memory is on by default.**」以 `/memory` 內的開關切換，寫入 `~/.claude/settings.json` 的 `autoMemoryEnabled`；也可在專案 settings 設 `"autoMemoryEnabled": false`（行 459–463）。
- **目錄**：「Each project gets its own memory directory at `~/.claude/projects/<project>/memory/`. The `<project>` path is derived from the git repository, so all worktrees and subdirectories within the same repo share one auto memory directory. **Outside a git repo, the project root is used instead.**」（行 471）
- 可用 `autoMemoryDirectory` 改位置，任何 settings scope 皆可讀（行 475）。
- 「Auto memory is **machine-local**… Files are not shared across machines or cloud environments.」（行 499）
- 定位（行首對照表）：CLAUDE.md 與 auto memory「Both are loaded at the start of every conversation. **Claude treats them as context, not enforced configuration. To block an action regardless of what Claude decides, use a PreToolUse hook instead.**」——**這句話是整份調查對「學習閘門」最重要的一句：寫在 CLAUDE.md 的規則不是強制的。**

### 4.4 其他

- `.claude/rules/`：可用 `paths` 欄位做路徑限定的規則檔（path-specific rules）。
- `AGENTS.md`：若工作目錄或其上層沒有任何 `CLAUDE.md`/`CLAUDE.local.md`，Claude 會直接讀 `AGENTS.md`（v2.1.277+）；兩者都有時只讀 CLAUDE.md。可用 **Project instructions** 設為 `claude-md-and-agents-md` 讓兩者都載入。
- `/memory` 列出所有記憶檔位置並可開關 auto memory；`/context` 看本 session 實際載入了哪些。

---

## 5. Settings（設定）

來源：<https://code.claude.com/docs/en/settings.md>、<https://code.claude.com/docs/en/settings-reference.md>

### 5.1 hooks 設定放哪裡

hooks 寫在 **settings 檔的 `hooks` 區塊**（hooks-guide 行 21：「To create a hook, add a `hooks` block to a settings file」）。四個 scope（settings.md 行 405–408）：

| Scope | 檔案 | 影響 |
|---|---|---|
| User | `~/.claude/settings.json` | 你，本機所有專案 |
| Shared project | `.claude/settings.json` | 該資料夾所有人；「Team permissions, **hooks**, plugins…」——官方建議 hooks 放這裡並提交版控 |
| Project local | `.claude/settings.local.json` | 你，僅此專案；Claude Code 建檔時會自動加入 git global excludes |
| Managed | `managed-settings.json` / MDM / claude.ai console | 組織部署，**使用者無法覆蓋** |

優先序（settings.md 原文 In order, highest precedence first，共五層）：**1 Managed settings ＞ 2 Command line arguments（含 `--settings`）＞ 3 Project local `.claude/settings.local.json` ＞ 4 Shared project `.claude/settings.json` ＞ 5 User `~/.claude/settings.json`**。環境變數不是其中一層，逐對決定。

另外 hooks 也可以寫在 **skill 的 `hooks` frontmatter**（invoke 時註冊，之後整個 session 持續）與 **subagent 的 `hooks` 欄位**（只在該 subagent 內）。

### 5.2 permissions allow / ask / deny

`permissions` 物件的鍵（settings-reference.md）：`allow`、`ask`、`deny`、`additionalDirectories`、`blockReadsOutsideWorkingDirectories`、`defaultMode`、`disableBypassPermissionsMode`、`disableAutoMode`。

```json
{
  "permissions": {
    "allow": ["Bash(npm run *)"],
    "ask": ["Bash(git push *)"],
    "deny": ["Read(./.env)"],
    "defaultMode": "acceptEdits"
  }
}
```

`permissions.deny` 的效力與邊界（settings-reference.md `permissions.deny` 段）：對內建檔案工具、Claude Code 認得的 Bash 檔案指令（`cat`、`head`、`tail`、`sed`、`tee`）與重導向目標（`> file`、`< file`）生效；**對沒指名檔案的指令（如 `grep -r pattern .`）與任意子行程無效**，要 OS 層強制就得開 sandbox。

### 5.3 `disallowedTools` 能限制工具嗎？

**沒有叫 `disallowedTools` 的 settings 鍵**。限制工具的四個入口是：

1. `permissions.deny`（settings 檔，持久）
2. `--disallowedTools` CLI 參數：「adds deny rules for one session alongside this key」（settings-reference.md `permissions.deny` 的 Per-session overrides）；對應的 `--allowedTools` 則「adds allow rules for one session」
3. subagent frontmatter 的 `tools` / `disallowedTools`
4. skill frontmatter 的 `allowed-tools` / `disallowed-tools`（只撐一個回合）

補充：組織可把 managed settings 設成唯一的權限規則來源，此時使用者檔案的 allow/ask/deny 與 `--allowedTools` 都被忽略，但 **`--disallowedTools` 與 session 的 deny/ask 仍然有效**，因為「They only restrict, so they can't widen what the managed rules grant.」（settings-reference.md 行 1321–1325）

---

## 6. Output styles（輸出風格）與 `TODO(human)`

來源：<https://code.claude.com/docs/en/output-styles.md>

### 6.1 內建風格

Default 之外有四個內建風格，逐字：

- **Proactive**：立刻執行、自行做合理假設、偏好行動勝過規劃。
- **Concise**：先講結果、不鋪陳、預設簡短（v2.1.237+）。
- **Explanatory**：「Provides educational "Insights" in between helping you complete software engineering tasks. Helps you understand implementation choices and codebase patterns.」
- **Learning**：「**Collaborative, learn-by-doing mode where Claude will not only share "Insights" while coding, but also ask you to contribute small, strategic pieces of code yourself. Claude Code will add `TODO(human)` markers in your code for you to implement.**」

### 6.2 `TODO(human)` 機制怎麼運作？

**文件只有上面那一句。** 除了「Claude Code 會在你的程式碼裡插入 `TODO(human)` 標記讓你自己實作」以外，output-styles.md **沒有說明**：標記如何被偵測、Claude 之後會不會等待、會不會檢查你寫的內容、有沒有對應的 hook/工具。→ 機制細節標【未證實】。

這仍是「Anthropic 官方把思考留給人」的先例，但它的實作看來是**純 prompt 層**（見 6.3），沒有強制力。

### 6.3 Output style 怎麼運作、以及對本專案的關鍵限制

- 「Claude Code sends the active style's instructions with every request.」選非 Default 風格時還會「remind Claude of the style during the conversation」。
- 自訂風格預設**拿掉** Claude Code 內建的軟體工程指示，除非 `keep-coding-instructions: true`。
- 檔案位置三層：`~/.claude/output-styles`、`.claude/output-styles`、managed settings 目錄下的 `.claude/output-styles`。Frontmatter：`name`、`description`、`keep-coding-instructions`、`force-for-plugin`。
- 切換：`/output-style <style>`（v2.1.269+，headless 與 Agent SDK 也可用）、`/config` 選單、VS Code 命令選單、或直接寫 settings 的 `outputStyle`，一律存到 `.claude/settings.local.json`。
- **關鍵限制**：「Output styles apply to the main conversation and to a **fork**, which inherits the parent's full conversation and system prompt. **Other subagents run their own system prompt, so styles don't change how they respond.**」→ Learning 風格**不會傳染給 subagent**。
- 官方自己的對照表：Output styles＝改預設指示；CLAUDE.md＝system prompt 之後加一則 user message；Agents＝獨立 system prompt/model/tools 的子代理；Skills＝被叫用或相關時載入的任務指示。

---

## 7. Claude for Education / Learning mode

- Claude Code 文件索引（`llms.txt`）中 **grep 不到** "education"；`learning` 只出現在 output-styles 的 Learning 風格。
- claude.ai 文件索引 <https://claude.com/docs/llms.txt>（56 KB，內容正常）中同樣 **grep 不到** "education" 或 "learning"。
- →【未證實】本次調查在 Claude Code 與 claude.ai 兩份文件索引中都找不到 "Claude for Education" 或 "Learning mode" 頁面。唯一有文件根據的「學習模式」就是 §6 的 Learning output style。

---

## 8. 學習流程 → 機制對應表

| 步驟 | 最適合的機制 | 理由與文件根據 |
|---|---|---|
| **1. 寫下已知與問題** | **使用者觸發的 skill**（`disable-model-invocation: true`）＋ 寫入一個固定路徑的檔案（如 `.claude/scratch/session-<slug>.md`） | skill 內容「enters the conversation as a single message and stays there across later turns」（skills.md 514），適合當整段學習的常駐指示；`disable-model-invocation` 確保只有使用者能開啟一輪（skills.md 338）。檔案是後面所有 hook 唯一能確定性檢查的東西。 |
| **2. 提假設與預測** | **`UserPromptSubmit` command hook**（檢查預測檔存在／非空／格式）＋ 必要時 **`UserPromptExpansion`** 擋 `/answer` 之類直接指令；想要「判斷預測是否像樣」再加 **`type: "prompt"` hook** | `UserPromptSubmit` 可 `decision: "block"`＋`reason`（hooks.md 1368–1385），且收得到 `prompt` 原文（1355）。擋直接打的 `/指令` 只有 `UserPromptExpansion` 做得到（「a `PreToolUse` hook matching the `Skill` tool fires only when Claude calls the tool, but typing `/skillname` directly bypasses `PreToolUse`」）。判斷型閘門見 hooks-guide 845+。**限制**：command hook 只能做字串／檔案檢查；30s timeout 且 fail-open；block 會抹掉 prompt 且 `reason` 不進 context。 |
| **3. AI 建 testcase 跑實驗** | **Subagent**（`tools` 白名單，例如 `Read, Grep, Glob, Bash, Write`；`model: sonnet` 或依難度） | 「Each subagent starts with a fresh, isolated context window. It doesn't see your conversation history」（sub-agents.md 1035）——**出題／建 case 的代理看不到使用者的預測，天然盲測**。工具白名單見 §2.6。長輸出留在 subagent context（行 940）。 |
| **4. 比對結果** | **主對話**（讀 subagent 回傳的摘要）；若需要把工具輸出改寫成「只給證據不給結論」可加 **`PostToolUse` 的 `updatedToolOutput`** | 「returns only the summary」（行 9、940）。`PostToolUse` 可 `additionalContext` 或 `updatedToolOutput`「Replaces the tool's output with the provided value before it is sent to Claude」（hooks.md PostToolUse decision control）。 |
| **5. 使用者自述解釋** | **必須在主對話**：`AskUserQuestion` 工具 ＋ **`Stop` hook `decision: "block"`** 直到解釋檔寫入才放行 | `AskUserQuestion`「asks multiple-choice questions… Questions stay open until you answer them」（tools-reference 125–137），但它被**第一層過濾器從所有非 fork subagent 移除**（sub-agents.md 412）→ 這一步不能外包給 subagent。`Stop` 的 `decision: "block"` /「prevents Claude from stopping」＋必填 `reason`（hooks.md 2610–2627）；注意連續 8 次上限、且使用者按中斷時 Stop hook 不觸發（hooks-guide 962）。若要「判斷解釋是否成立」，用 `type: "agent"` 的 Stop hook（可讀檔驗證，60s／50 turns）。 **設計要點**：`Stop` hook 擋下來之後，Claude 必須自己呼叫 `AskUserQuestion` 才能把發言權交回使用者；因此 `reason` 文字一定要明白寫出「請用 AskUserQuestion 向使用者提問」，否則 Claude 只會自行重試，直到撞上連續 8 次的續跑上限。 |
| **6. AI 出遷移題** | **Skill**（出題規則，可 `context: fork` 丟去背景跑）＋ **`Stop` hook** 確保沒出題不准收工 | `context: fork` 會「start without your conversation history」（sub-agents.md 591）——遷移題出題者不看使用者原本的解釋，避免抄答案；`background: false` 可改成當回合等結果（skills.md 346）。 |
| **7. 記錄到檔案** | **`Stop` hook（command）**每回合把紀錄落檔 ＋ **`CLAUDE.local.md`** 放個人長期規則 ＋ **auto memory** 自動累積 | 「`Stop` hooks fire whenever Claude finishes responding」（hooks-guide 962）——每回合都有機會落檔，是最可靠的寫入點。auto memory 目錄 `~/.claude/projects/<project>/memory/`（memory.md 471），機器本機、不跨機同步（499）。`CLAUDE.local.md` 為個人不進版控的專案指示（memory.md 111）。**注意**：memory.md 開頭明說 CLAUDE.md 與 auto memory「Claude treats them as context, **not enforced configuration**」，要強制就得用 hook。 |

### 跨步驟的三個結構性限制

1. **強制力只在 hook**（memory.md「To block an action regardless of what Claude decides, use a PreToolUse hook instead.」）。CLAUDE.md、skill 文字、output style 全都是「指示」，模型可以不照做。
2. **Learning output style 不會傳到 subagent**（output-styles.md：「Other subagents run their own system prompt, so styles don't change how they respond.」）——每個 subagent 要靠自己的定義檔或 `skills` 預載。
3. **只有主對話（與 fork）能跟使用者互動**（`AskUserQuestion` 被非 fork subagent 過濾掉）。所有「要人回答」的步驟都必須留在主對話。

---

## 9. 沒查到或沒確認的事

1. **Claude for Education / Learning mode**：Claude Code 與 claude.ai 兩份文件索引都 grep 不到，判定不存在對應文件頁（§7）。
2. **`TODO(human)` 的實作機制**：output-styles.md 只有一句描述，沒有說明偵測、等待、驗證或對應工具（§6.2）。
3. **Fork 能否使用 `AskUserQuestion` 向使用者提問**：只能從「Forks skip both filters and receive the main conversation's exact tool pool」（sub-agents.md 409）推論為可以，文件**沒有明說**。
4. **組織 managed CLAUDE.md 的實際路徑**：memory.md 只提到它存在（行 340），路徑需另查 managed-settings 頁，本次未查。
5. **`type: "prompt"` / `type: "agent"` hook 的完整設定欄位**：只讀了 hooks-guide 的介紹段（行 845–909），未展開 hooks.md 的 `#prompt-based-hooks` / `#agent-based-hooks` 完整參考（含 `model`、`continueOnBlock`、`$ARGUMENTS` 等）。
6. **`permissions` 規則語法細節**：未讀 <https://code.claude.com/docs/en/permissions.md> 的 rule syntax 全文，只取 settings-reference 的摘要與範例。
7. **Plugin / plugin eval / `/skill-doctor`**：本次任務未涵蓋，未查。
8. **本檔未經獨立讀回驗證**（全域法則 2「Never self-verify」）：所有引文都來自 `curl` 下來的原始 markdown 而非記憶，但**沒有第二個新鮮 context 的代理讀過這份檔案並比對原文**。若要作為設計決策依據，建議另派一個 agent 對 §3.4、§3.5、§2.3 三處逐字核對。
