# 自訂

OpenSpec 提供三個層級的自訂：

| 層級 | 功能 | 最適合 |
|------|------|--------|
| **專案設定** | 設定預設值、注入背景資訊/規則 | 大多數團隊 |
| **自訂結構描述** | 定義你自己的工作流程產出物 | 有獨特流程的團隊 |
| **全域覆寫** | 在所有專案間共用結構描述 | 進階使用者 |

---

## 專案設定

`openspec/config.yaml` 檔案是為你的團隊自訂 OpenSpec 最簡單的方式。它讓你可以：

- **設定預設結構描述** — 每次指令都不需加上 `--schema`
- **注入專案背景資訊** — AI 可看到你的技術堆疊、慣例等
- **加入各產出物規則** — 針對特定產出物的自訂規則

### 快速設定

```bash
openspec init
```

這會引導你以互動方式建立設定。或手動建立：

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js, PostgreSQL
  API style: RESTful, documented in docs/api.md
  Testing: Jest + React Testing Library
  We value backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format
    - Reference existing patterns before inventing new ones
```

### 運作方式

**預設結構描述：**

```bash
# 沒有設定時
openspec new change my-feature --schema spec-driven

# 有設定時 — 結構描述自動套用
openspec new change my-feature
```

**背景資訊與規則注入：**

生成任何產出物時，你的背景資訊和規則會注入至 AI 提示中：

```xml
<context>
Tech stack: TypeScript, React, Node.js, PostgreSQL
...
</context>

<rules>
- Include rollback plan
- Identify affected teams
</rules>

<template>
[結構描述的內建範本]
</template>
```

- **背景資訊**出現在所有產出物中
- **規則**僅出現在對應的產出物中

### 結構描述解析順序

OpenSpec 需要結構描述時，按以下順序檢查：

1. CLI 旗標：`--schema <name>`
2. 變更元資料（變更資料夾中的 `.openspec.yaml`）
3. 專案設定（`openspec/config.yaml`）
4. 預設（`spec-driven`）

---

## 自訂結構描述

當專案設定不夠用時，可建立具有完全自訂工作流程的結構描述。自訂結構描述存放在你專案的 `openspec/schemas/` 目錄中，並與程式碼一同進行版本控制。

```text
your-project/
├── openspec/
│   ├── config.yaml        # 專案設定
│   ├── schemas/           # 自訂結構描述存放於此
│   │   └── my-workflow/
│   │       ├── schema.yaml
│   │       └── templates/
│   └── changes/           # 你的變更
└── src/
```

### 衍生現有結構描述

最快的自訂方式是衍生內建結構描述：

```bash
openspec schema fork spec-driven my-workflow
```

這會將完整的 `spec-driven` 結構描述複製到 `openspec/schemas/my-workflow/`，你可以自由編輯。

**你得到的內容：**

```text
openspec/schemas/my-workflow/
├── schema.yaml           # 工作流程定義
└── templates/
    ├── proposal.md       # 提案產出物的範本
    ├── spec.md           # 規格範本
    ├── design.md         # 設計範本
    └── tasks.md          # 任務範本
```

現在編輯 `schema.yaml` 來變更工作流程，或編輯範本來變更 AI 生成的內容。

### 從頭建立結構描述

對於全新的工作流程：

```bash
# 互動式
openspec schema init research-first

# 非互動式
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

### 結構描述結構

結構描述定義工作流程中的產出物及其相互依賴關係：

```yaml
# openspec/schemas/my-workflow/schema.yaml
name: my-workflow
version: 1
description: My team's custom workflow

artifacts:
  - id: proposal
    generates: proposal.md
    description: Initial proposal document
    template: proposal.md
    instruction: |
      Create a proposal that explains WHY this change is needed.
      Focus on the problem, not the solution.
    requires: []

  - id: design
    generates: design.md
    description: Technical design
    template: design.md
    instruction: |
      Create a design document explaining HOW to implement.
    requires:
      - proposal    # 提案存在前無法建立設計

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires:
      - design

apply:
  requires: [tasks]
  tracks: tasks.md
```

**關鍵欄位：**

| 欄位 | 用途 |
|------|------|
| `id` | 唯一識別碼，用於指令和規則 |
| `generates` | 輸出檔案名稱（支援如 `specs/**/*.md` 的 glob） |
| `template` | `templates/` 目錄中的範本檔案 |
| `instruction` | AI 建立此產出物的指示 |
| `requires` | 依賴關係 — 哪些產出物必須先存在 |

### 範本

範本是引導 AI 的 Markdown 檔案，建立該產出物時會注入至提示中。

```markdown
<!-- templates/proposal.md -->
## 為何

<!-- 說明此變更的動機。這解決了什麼問題？ -->

## 變更內容

<!-- 描述將會改變什麼。具體說明新功能或修改。 -->

## 影響

<!-- 受影響的程式碼、API、依賴項、系統 -->
```

範本可包含：
- AI 應填入的章節標題
- 為 AI 提供引導的 HTML 注釋
- 顯示預期結構的範例格式

### 驗證你的結構描述

使用自訂結構描述前，先驗證它：

```bash
openspec schema validate my-workflow
```

這會檢查：
- `schema.yaml` 語法是否正確
- 所有引用的範本是否存在
- 是否有循環依賴
- 產出物 ID 是否有效

### 使用你的自訂結構描述

建立後，使用以下方式使用你的結構描述：

```bash
# 在指令中指定
openspec new change feature --schema my-workflow

# 或在 config.yaml 中設為預設
schema: my-workflow
```

### 偵錯結構描述解析

不確定使用哪個結構描述？用以下方式查看：

```bash
# 查看特定結構描述從哪裡解析
openspec schema which my-workflow

# 列出所有可用的結構描述
openspec schema which --all
```

輸出顯示它來自你的專案、使用者目錄還是套件：

```text
Schema: my-workflow
Source: project
Path: /path/to/project/openspec/schemas/my-workflow
```

---

> **注意：** OpenSpec 也支援在 `~/.local/share/openspec/schemas/` 的使用者層級結構描述，用於跨專案共用，但建議使用 `openspec/schemas/` 中的專案層級結構描述，因為它們與程式碼一同進行版本控制。

---

## 範例

### 快速迭代工作流程

快速迭代的最小工作流程：

```yaml
# openspec/schemas/rapid/schema.yaml
name: rapid
version: 1
description: Fast iteration with minimal overhead

artifacts:
  - id: proposal
    generates: proposal.md
    description: Quick proposal
    template: proposal.md
    instruction: |
      Create a brief proposal for this change.
      Focus on what and why, skip detailed specs.
    requires: []

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires: [proposal]

apply:
  requires: [tasks]
  tracks: tasks.md
```

### 加入審查產出物

衍生預設並加入審查步驟：

```bash
openspec schema fork spec-driven with-review
```

然後編輯 `schema.yaml` 加入：

```yaml
  - id: review
    generates: review.md
    description: Pre-implementation review checklist
    template: review.md
    instruction: |
      Create a review checklist based on the design.
      Include security, performance, and testing considerations.
    requires:
      - design

  - id: tasks
    # ... 現有的任務設定 ...
    requires:
      - specs
      - design
      - review    # 現在任務也需要審查
```

---

## 另請參閱

- [CLI 參考：結構描述指令](cli.md#schema-commands) — 完整指令文件
