# 遷移至 OPSX

本指南幫助你從舊版 OpenSpec 工作流程過渡至 OPSX。遷移過程設計得盡量順暢 — 你現有的工作會被保留，新系統提供更多彈性。

## 有什麼改變？

OPSX 以流暢的行動式方法取代了舊版的階段鎖定工作流程。以下是主要轉變：

| 方面 | 舊版 | OPSX |
|------|------|------|
| **指令** | `/openspec:proposal`、`/openspec:apply`、`/openspec:archive` | 預設：`/opsx:propose`、`/opsx:apply`、`/opsx:archive`（擴充工作流程指令選用） |
| **工作流程** | 一次建立所有產出物 | 逐步或一次建立 — 由你選擇 |
| **回頭修改** | 尷尬的階段閘門 | 自然 — 隨時更新任何產出物 |
| **自訂** | 固定結構 | 結構描述驅動，完全可調整 |
| **設定** | 有標記的 `CLAUDE.md` + `project.md` | 乾淨的 `openspec/config.yaml` 設定 |

**理念轉變：** 工作不是線性的。OPSX 停止假裝它是。

---

## 開始前

### 你現有的工作是安全的

遷移過程以保護為設計前提：

- **`openspec/changes/` 中的活躍變更** — 完全保留。你可以繼續使用 OPSX 指令處理它們。
- **已封存的變更** — 不受影響。你的歷史記錄保持完整。
- **`openspec/specs/` 中的主要規格** — 不受影響。這些是你的真相來源。
- **你在 CLAUDE.md、AGENTS.md 等中的內容** — 已保留。只有 OpenSpec 標記區塊被移除；你撰寫的所有內容都保留。

### 哪些內容會被移除

只有被取代的 OpenSpec 管理檔案：

| 內容 | 原因 |
|------|------|
| 舊版斜線指令目錄/檔案 | 由新的技能系統取代 |
| `openspec/AGENTS.md` | 已過時的工作流程觸發器 |
| `CLAUDE.md`、`AGENTS.md` 等中的 OpenSpec 標記 | 不再需要 |

**各工具的舊版指令位置**（範例 — 你的工具可能不同）：

- Claude Code：`.claude/commands/openspec/`
- Cursor：`.cursor/commands/openspec-*.md`
- Windsurf：`.windsurf/workflows/openspec-*.md`
- Cline：`.clinerules/workflows/openspec-*.md`
- Roo：`.roo/commands/openspec-*.md`
- GitHub Copilot：`.github/prompts/openspec-*.prompt.md`（僅 IDE 擴充套件；Copilot CLI 不支援）
- 以及其他（Augment、Continue、Amazon Q 等）

遷移會偵測你已設定的工具並清理它們的舊版檔案。

移除清單可能看起來很長，但這些都是 OpenSpec 原先建立的檔案。你自己的內容永遠不會被刪除。

### 需要你注意的事

一個檔案需要手動遷移：

**`openspec/project.md`** — 這個檔案不會自動刪除，因為它可能包含你撰寫的專案背景資訊。你需要：

1. 審查其內容
2. 將有用的背景資訊移至 `openspec/config.yaml`（見下方指引）
3. 準備好後刪除該檔案

**為何我們做此改變：**

舊版 `project.md` 是被動的 — AI 可能讀它，也可能不讀，可能忘記讀過的內容。我們發現可靠性不一致。

新的 `config.yaml` 背景資訊是**主動注入每個 OpenSpec 規劃請求的**。這意味著你的專案慣例、技術堆疊和規則在 AI 建立產出物時總是存在。可靠性更高。

**取捨：**

因為背景資訊會注入每個請求，你需要保持簡潔。專注於真正重要的事：
- 技術堆疊和關鍵慣例
- AI 需要知道的非顯而易見限制
- 以前經常被忽略的規則

不用擔心做到完美。我們仍在學習什麼最有效，隨著實驗，我們會改善背景資訊注入的方式。

---

## 執行遷移

`openspec init` 和 `openspec update` 都會偵測舊版檔案並引導你完成相同的清理流程。選擇適合你情況的：

- 新安裝預設使用 `core` 設定檔（`propose`、`explore`、`apply`、`archive`）。
- 遷移的安裝會在需要時撰寫 `custom` 設定檔，以保留你之前安裝的工作流程。

### 使用 `openspec init`

如果你想加入新工具或重新設定已設定的工具，執行此指令：

```bash
openspec init
```

init 指令會偵測舊版檔案並引導你完成清理：

```
升級至新版 OpenSpec

OpenSpec 現在使用代理技能，這是各種程式碼代理的新興標準。
這簡化了你的設定，同時保持一切如常運作。

要移除的檔案
沒有需要保留的使用者內容：
  • .claude/commands/openspec/
  • openspec/AGENTS.md

要更新的檔案
OpenSpec 標記將被移除，你的內容會被保留：
  • CLAUDE.md
  • AGENTS.md

需要你注意的事
  • openspec/project.md
    我們不會刪除這個檔案。它可能包含有用的專案背景資訊。

    新的 openspec/config.yaml 有一個用於規劃背景資訊的「context:」章節。
    這會包含在每個 OpenSpec 請求中，比舊版 project.md 方法更可靠。

    審查 project.md，將有用的內容移至 config.yaml 的 context 章節，
    然後在準備好後刪除該檔案。

? 升級並清理舊版檔案？ (Y/n)
```

**當你說是時發生的事：**

1. 移除舊版斜線指令目錄
2. 從 `CLAUDE.md`、`AGENTS.md` 等移除 OpenSpec 標記（你的內容保留）
3. 刪除 `openspec/AGENTS.md`
4. 在 `.claude/skills/` 中安裝新技能
5. 建立附預設結構描述的 `openspec/config.yaml`

### 使用 `openspec update`

如果你只想遷移並將現有工具更新至最新版本，執行此指令：

```bash
openspec update
```

update 指令也會偵測並清理舊版產出物，然後重新整理生成的技能/指令以符合你當前的設定檔和傳遞設定。

### 非互動式 / CI 環境

用於腳本化遷移：

```bash
openspec init --force --tools claude
```

`--force` 旗標跳過提示並自動接受清理。

---

## 將 project.md 遷移至 config.yaml

舊版 `openspec/project.md` 是用於專案背景資訊的自由形式 Markdown 檔案。新的 `openspec/config.yaml` 是結構化的，而且關鍵是 — **注入每個規劃請求**，讓你的慣例在 AI 工作時總是存在。

### 之前（project.md）

```markdown
# 專案背景資訊

這是使用 React 和 Node.js 的 TypeScript monorepo。
我們使用 Jest 進行測試，並遵循嚴格的 ESLint 規則。
我們的 API 是 RESTful 並記錄在 docs/api.md 中。

## 慣例

- 所有公開 API 必須保持向後相容性
- 新功能應包含測試
- 使用 Given/When/Then 格式撰寫規格
```

### 之後（config.yaml）

```yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  Testing: Jest with React Testing Library
  API: RESTful, documented in docs/api.md
  We maintain backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan for risky changes
  specs:
    - Use Given/When/Then format for scenarios
    - Reference existing patterns before inventing new ones
  design:
    - Include sequence diagrams for complex flows
```

### 主要差異

| project.md | config.yaml |
|------------|-------------|
| 自由形式 Markdown | 結構化 YAML |
| 一大段文字 | 分開的背景資訊和各產出物規則 |
| 不清楚何時使用 | 背景資訊出現在所有產出物中；規則只出現在對應的產出物中 |
| 無結構描述選擇 | 明確的 `schema:` 欄位設定預設工作流程 |

### 保留什麼，捨棄什麼

遷移時，要有選擇性。問自己：「AI 每次規劃請求都需要這個嗎？」

**適合放入 `context:` 的：**
- 技術堆疊（語言、框架、資料庫）
- 關鍵架構模式（monorepo、微服務等）
- 非顯而易見的限制（「我們不能使用函式庫 X，因為...」）
- 經常被忽略的重要慣例

**改放 `rules:` 的：**
- 特定產出物的格式（「在規格中使用 Given/When/Then」）
- 審查標準（「提案必須包含回滾計劃」）
- 這些只出現在對應的產出物中，讓其他請求更精簡

**完全略去的：**
- AI 已知的一般最佳實踐
- 可以摘要的冗長說明
- 不影響當前工作的歷史背景

### 遷移步驟

1. **建立 config.yaml**（如果 init 尚未建立）：
   ```yaml
   schema: spec-driven
   ```

2. **加入你的背景資訊**（保持簡潔 — 這會進入每個請求）：
   ```yaml
   context: |
     你的專案背景資訊放這裡。
     專注於 AI 真正需要知道的事情。
   ```

3. **加入各產出物規則**（選用）：
   ```yaml
   rules:
     proposal:
       - 你的提案特定指引
     specs:
       - 你的規格撰寫規則
   ```

4. 移完有用的內容後，**刪除 project.md**。

**不要想太多。** 從基本必需品開始，迭代。若你發現 AI 遺漏了重要事項，就加入。若背景資訊感覺臃腫，就修剪。這是一份活文件。

### 需要幫助？使用此提示

如果你不確定如何提煉你的 project.md，可以詢問你的 AI 助理：

```
我正在將 OpenSpec 的舊版 project.md 遷移至新的 config.yaml 格式。

以下是我目前的 project.md：
[貼上你的 project.md 內容]

請幫我建立一個 config.yaml，包含：
1. 簡潔的 `context:` 章節（這會注入每個規劃請求，所以保持精簡 — 專注於技術堆疊、關鍵限制和經常被忽略的慣例）
2. 若有任何內容是特定於某產出物的，則加入 `rules:`（例如「使用 Given/When/Then」應屬於規格規則，而非全域背景資訊）

略去 AI 模型已知的任何通用內容。對簡潔度要毫不留情。
```

AI 會幫助你識別什麼是必要的，什麼可以精簡。

---

## 新指令

指令的可用性取決於設定檔：

**預設（`core` 設定檔）：**

| 指令 | 用途 |
|------|------|
| `/opsx:propose` | 一步建立變更和規劃產出物 |
| `/opsx:explore` | 無結構地思考想法 |
| `/opsx:apply` | 實作 tasks.md 中的任務 |
| `/opsx:archive` | 完成並封存變更 |

**擴充工作流程（自訂選擇）：**

| 指令 | 用途 |
|------|------|
| `/opsx:new` | 開始新的變更架構 |
| `/opsx:continue` | 建立下一個產出物（一次一個） |
| `/opsx:ff` | 快進 — 一次建立規劃產出物 |
| `/opsx:verify` | 驗證實作符合規格 |
| `/opsx:sync` | 預覽/規格合併而不封存 |
| `/opsx:bulk-archive` | 一次封存多個變更 |
| `/opsx:onboard` | 引導式端對端上手工作流程 |

使用 `openspec config profile` 啟用擴充指令，然後執行 `openspec update`。

### 舊版指令對應

| 舊版 | OPSX 對應 |
|------|----------|
| `/openspec:proposal` | `/opsx:propose`（預設）或 `/opsx:new` 再 `/opsx:ff`（擴充） |
| `/openspec:apply` | `/opsx:apply` |
| `/openspec:archive` | `/opsx:archive` |

### 新功能

這些功能是擴充工作流程指令集的一部分。

**細粒度產出物建立：**
```
/opsx:continue
```
根據依賴關係一次建立一個產出物。想要逐步審查時使用。

**探索模式：**
```
/opsx:explore
```
在承諾進行變更之前，與合作夥伴一同思考想法。

---

## 了解新架構

### 從階段鎖定到流暢

舊版工作流程強制線性進程：

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   計劃       │ ───► │   實作       │ ───► │   封存       │
│   階段       │      │   階段       │      │   階段       │
└──────────────┘      └──────────────┘      └──────────────┘

如果你在實作中意識到設計是錯誤的？
太糟糕了。階段閘門不讓你輕易回頭。
```

OPSX 使用行動，而非階段：

```
         ┌───────────────────────────────────────────────┐
         │           行動（非階段）                       │
         │                                               │
         │     new ◄──► continue ◄──► apply ◄──► archive │
         │      │          │           │             │   │
         │      └──────────┴───────────┴─────────────┘   │
         │                    任意順序                   │
         └───────────────────────────────────────────────┘
```

### 依賴圖

產出物形成有向圖。依賴關係是啟用者，而非閘門：

```
                        proposal
                       （根節點）
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
           specs                       design
        （requires:                  （requires:
         proposal）                   proposal）
              │                           │
              └─────────────┬─────────────┘
                            │
                            ▼
                         tasks
                     （requires:
                     specs, design）
```

執行 `/opsx:continue` 時，它會檢查什麼準備好了並提供下一個產出物。你也可以以任意順序建立多個準備好的產出物。

### 技能 vs 指令

舊版系統使用工具特定的指令檔案：

```
.claude/commands/openspec/
├── proposal.md
├── apply.md
└── archive.md
```

OPSX 使用新興的**技能**標準：

```
.claude/skills/
├── openspec-explore/SKILL.md
├── openspec-new-change/SKILL.md
├── openspec-continue-change/SKILL.md
├── openspec-apply-change/SKILL.md
└── ...
```

技能可跨多個 AI 程式碼工具識別，並提供更豐富的元資料。

---

## 繼續現有的變更

你進行中的變更與 OPSX 指令無縫協作。

**有來自舊版工作流程的活躍變更？**

```
/opsx:apply add-my-feature
```

OPSX 讀取現有的產出物並從你離開的地方繼續。

**想為現有變更加入更多產出物？**

```
/opsx:continue add-my-feature
```

顯示根據現有內容準備好建立的產出物。

**需要查看狀態？**

```bash
openspec status --change add-my-feature
```

---

## 新設定系統

### config.yaml 結構

```yaml
# 必要：新變更的預設結構描述
schema: spec-driven

# 選用：專案背景資訊（最大 50KB）
# 注入至所有產出物指示中
context: |
  你的專案背景資訊、技術堆疊、
  慣例和限制。

# 選用：各產出物規則
# 只注入至對應的產出物中
rules:
  proposal:
    - Include rollback plan
  specs:
    - Use Given/When/Then format
  design:
    - Document fallback strategies
  tasks:
    - Break into 2-hour maximum chunks
```

### 結構描述解析

確定使用哪個結構描述時，OPSX 按順序檢查：

1. **CLI 旗標**：`--schema <name>`（最高優先）
2. **變更元資料**：變更目錄中的 `.openspec.yaml`
3. **專案設定**：`openspec/config.yaml`
4. **預設**：`spec-driven`

### 可用結構描述

| 結構描述 | 產出物 | 最適合 |
|---------|--------|--------|
| `spec-driven` | proposal → specs → design → tasks | 大多數專案 |

列出所有可用結構描述：

```bash
openspec schemas
```

### 自訂結構描述

建立你自己的工作流程：

```bash
openspec schema init my-workflow
```

或從現有的衍生：

```bash
openspec schema fork spec-driven my-workflow
```

詳情請參閱 [自訂](customization.md)。

---

## 疑難排解

### 「非互動式模式中偵測到舊版檔案」

你在 CI 或非互動式環境中執行。使用：

```bash
openspec init --force
```

### 遷移後指令未出現

重新啟動你的 IDE。技能在啟動時偵測。

### 「規則中有未知的產出物 ID」

確認你的 `rules:` 鍵與你結構描述的產出物 ID 相符：

- **spec-driven**：`proposal`、`specs`、`design`、`tasks`

執行以下指令查看有效的產出物 ID：

```bash
openspec schemas --json
```

### 設定未被套用

1. 確認檔案在 `openspec/config.yaml`（不是 `.yml`）
2. 驗證 YAML 語法
3. 設定變更立即生效 — 不需要重新啟動

### project.md 未遷移

系統故意保留 `project.md`，因為它可能包含你的自訂內容。手動審查它，將有用的部分移至 `config.yaml`，然後刪除它。

### 想查看什麼會被清理？

執行 init 並拒絕清理提示 — 你會看到完整的偵測摘要，而不會進行任何變更。

---

## 快速參考

### 遷移後的檔案

```
project/
├── openspec/
│   ├── specs/                    # 不變
│   ├── changes/                  # 不變
│   │   └── archive/              # 不變
│   └── config.yaml               # 新增：專案設定
├── .claude/
│   └── skills/                   # 新增：OPSX 技能
│       ├── openspec-propose/     # 預設 core 設定檔
│       ├── openspec-explore/
│       ├── openspec-apply-change/
│       └── ...                   # 擴充設定檔新增 new/continue/ff 等
├── CLAUDE.md                     # OpenSpec 標記已移除，你的內容保留
└── AGENTS.md                     # OpenSpec 標記已移除，你的內容保留
```

### 已移除的內容

- `.claude/commands/openspec/` — 由 `.claude/skills/` 取代
- `openspec/AGENTS.md` — 已過時
- `openspec/project.md` — 遷移至 `config.yaml`，然後刪除
- `CLAUDE.md`、`AGENTS.md` 等中的 OpenSpec 標記區塊

### 指令速查表

```text
/opsx:propose      快速開始（預設 core 設定檔）
/opsx:apply        實作任務
/opsx:archive      完成並封存

# 擴充工作流程（若已啟用）：
/opsx:new          建立變更架構
/opsx:continue     建立下一個產出物
/opsx:ff           建立規劃產出物
```

---

## 取得協助

- **Discord**：[discord.gg/YctCnvvshC](https://discord.gg/YctCnvvshC)
- **GitHub Issues**：[github.com/Fission-AI/OpenSpec/issues](https://github.com/Fission-AI/OpenSpec/issues)
- **文件**：[docs/opsx.md](opsx.md) 查看完整 OPSX 參考
