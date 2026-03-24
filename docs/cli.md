# CLI 參考

OpenSpec CLI（`openspec`）提供用於專案設定、驗證、狀態檢視和管理的終端機指令。這些指令補充了 [指令](commands.md) 中記錄的 AI 斜線指令（如 `/opsx:propose`）。

## 摘要

| 類別 | 指令 | 用途 |
|------|------|------|
| **設定** | `init`、`update` | 在你的專案中初始化和更新 OpenSpec |
| **瀏覽** | `list`、`view`、`show` | 探索變更和規格 |
| **驗證** | `validate` | 檢查變更和規格的問題 |
| **生命週期** | `archive` | 完成已完成的變更 |
| **工作流程** | `status`、`instructions`、`templates`、`schemas` | 產出物驅動的工作流程支援 |
| **結構描述** | `schema init`、`schema fork`、`schema validate`、`schema which` | 建立和管理自訂工作流程 |
| **設定** | `config` | 查看和修改設定 |
| **工具** | `feedback`、`completion` | 意見回饋和 shell 整合 |

---

## 人工 vs 代理指令

大多數 CLI 指令設計供**人工**在終端機中使用。部分指令也透過 JSON 輸出支援**代理/腳本使用**。

### 僅限人工的指令

這些指令是互動式的，設計供終端機使用：

| 指令 | 用途 |
|------|------|
| `openspec init` | 初始化專案（互動式提示） |
| `openspec view` | 互動式儀表板 |
| `openspec config edit` | 在編輯器中開啟設定 |
| `openspec feedback` | 透過 GitHub 提交意見回饋 |
| `openspec completion install` | 安裝 shell 補全 |

### 代理相容指令

這些指令支援 `--json` 輸出，供 AI 代理和腳本程式化使用：

| 指令 | 人工使用 | 代理使用 |
|------|---------|---------|
| `openspec list` | 瀏覽變更/規格 | `--json` 取得結構化資料 |
| `openspec show <item>` | 讀取內容 | `--json` 供解析 |
| `openspec validate` | 檢查問題 | `--all --json` 供大量驗證 |
| `openspec status` | 查看產出物進度 | `--json` 取得結構化狀態 |
| `openspec instructions` | 取得下一步 | `--json` 供代理指示 |
| `openspec templates` | 尋找範本路徑 | `--json` 供路徑解析 |
| `openspec schemas` | 列出可用結構描述 | `--json` 供結構描述探索 |

---

## 全域選項

這些選項適用於所有指令：

| 選項 | 說明 |
|------|------|
| `--version`、`-V` | 顯示版本號 |
| `--no-color` | 停用彩色輸出 |
| `--help`、`-h` | 顯示指令說明 |

---

## 設定指令

### `openspec init`

在你的專案中初始化 OpenSpec。建立資料夾結構並設定 AI 工具整合。

預設行為使用全域設定預設值：設定檔 `core`、傳遞方式 `both`、工作流程 `propose、explore、apply、archive`。

```
openspec init [path] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `path` | 否 | 目標目錄（預設：當前目錄） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--tools <list>` | 非互動式設定 AI 工具。使用 `all`、`none` 或逗號分隔的清單 |
| `--force` | 不提示自動清理舊版檔案 |
| `--profile <profile>` | 此次 init 覆寫全域設定檔（`core` 或 `custom`） |

`--profile custom` 使用全域設定中目前選擇的工作流程（`openspec config profile`）。

**支援的工具 ID（`--tools`）：** `amazon-q`、`antigravity`、`auggie`、`claude`、`cline`、`codex`、`codebuddy`、`continue`、`costrict`、`crush`、`cursor`、`factory`、`gemini`、`github-copilot`、`iflow`、`kilocode`、`kiro`、`opencode`、`pi`、`qoder`、`qwen`、`roocode`、`trae`、`windsurf`

**範例：**

```bash
# 互動式初始化
openspec init

# 在特定目錄中初始化
openspec init ./my-project

# 非互動式：為 Claude 和 Cursor 設定
openspec init --tools claude,cursor

# 為所有支援的工具設定
openspec init --tools all

# 此次執行覆寫設定檔
openspec init --profile core

# 跳過提示並自動清理舊版檔案
openspec init --force
```

**建立的內容：**

```
openspec/
├── specs/              # 你的規格（真相來源）
├── changes/            # 提議的變更
└── config.yaml         # 專案設定

.claude/skills/         # Claude Code 技能（若選擇了 claude）
.cursor/skills/         # Cursor 技能（若選擇了 cursor）
.cursor/commands/       # Cursor OPSX 指令（若傳遞方式包含指令）
... （其他工具設定）
```

---

### `openspec update`

升級 CLI 後更新 OpenSpec 指示檔案。使用你目前的全域設定檔、選定的工作流程和傳遞模式重新生成 AI 工具設定檔案。

```
openspec update [path] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `path` | 否 | 目標目錄（預設：當前目錄） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--force` | 即使檔案已是最新也強制更新 |

**範例：**

```bash
# npm 升級後更新指示檔案
npm update @fission-ai/openspec
openspec update
```

---

## 瀏覽指令

### `openspec list`

列出你專案中的變更或規格。

```
openspec list [options]
```

**選項：**

| 選項 | 說明 |
|------|------|
| `--specs` | 列出規格而非變更 |
| `--changes` | 列出變更（預設） |
| `--sort <order>` | 按 `recent`（預設）或 `name` 排序 |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 列出所有活躍的變更
openspec list

# 列出所有規格
openspec list --specs

# 供腳本使用的 JSON 輸出
openspec list --json
```

**輸出（文字）：**

```
活躍的變更：
  add-dark-mode     UI 主題切換支援
  fix-login-bug     Session 逾時處理
```

---

### `openspec view`

顯示探索規格和變更的互動式儀表板。

```
openspec view
```

開啟終端機介面，用於瀏覽你專案的規格和變更。

---

### `openspec show`

顯示變更或規格的詳情。

```
openspec show [item-name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `item-name` | 否 | 變更或規格的名稱（若省略則提示） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--type <type>` | 指定類型：`change` 或 `spec`（若不明確則自動偵測） |
| `--json` | 以 JSON 輸出 |
| `--no-interactive` | 停用提示 |

**變更特定選項：**

| 選項 | 說明 |
|------|------|
| `--deltas-only` | 只顯示差異規格（JSON 模式） |

**規格特定選項：**

| 選項 | 說明 |
|------|------|
| `--requirements` | 只顯示需求，排除情境（JSON 模式） |
| `--no-scenarios` | 排除情境內容（JSON 模式） |
| `-r, --requirement <id>` | 按 1-based 索引顯示特定需求（JSON 模式） |

**範例：**

```bash
# 互動式選擇
openspec show

# 顯示特定變更
openspec show add-dark-mode

# 顯示特定規格
openspec show auth --type spec

# 供解析的 JSON 輸出
openspec show add-dark-mode --json
```

---

## 驗證指令

### `openspec validate`

驗證變更和規格的結構問題。

```
openspec validate [item-name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `item-name` | 否 | 要驗證的特定項目（若省略則提示） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--all` | 驗證所有變更和規格 |
| `--changes` | 驗證所有變更 |
| `--specs` | 驗證所有規格 |
| `--type <type>` | 名稱不明確時指定類型：`change` 或 `spec` |
| `--strict` | 啟用嚴格驗證模式 |
| `--json` | 以 JSON 輸出 |
| `--concurrency <n>` | 最大並行驗證數（預設：6，或 `OPENSPEC_CONCURRENCY` 環境變數） |
| `--no-interactive` | 停用提示 |

**範例：**

```bash
# 互動式驗證
openspec validate

# 驗證特定變更
openspec validate add-dark-mode

# 驗證所有變更
openspec validate --changes

# 使用 JSON 輸出驗證所有內容（供 CI/腳本）
openspec validate --all --json

# 嚴格驗證並提高並行度
openspec validate --all --strict --concurrency 12
```

**輸出（文字）：**

```
正在驗證 add-dark-mode...
  ✓ proposal.md 有效
  ✓ specs/ui/spec.md 有效
  ⚠ design.md：缺少「技術方法」章節

發現 1 個警告
```

**輸出（JSON）：**

```json
{
  "version": "1.0.0",
  "results": {
    "changes": [
      {
        "name": "add-dark-mode",
        "valid": true,
        "warnings": ["design.md: missing 'Technical Approach' section"]
      }
    ]
  },
  "summary": {
    "total": 1,
    "valid": 1,
    "invalid": 0
  }
}
```

---

## 生命週期指令

### `openspec archive`

封存已完成的變更，並將差異規格合併至主要規格。

```
openspec archive [change-name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要封存的變更（若省略則提示） |

**選項：**

| 選項 | 說明 |
|------|------|
| `-y, --yes` | 跳過確認提示 |
| `--skip-specs` | 跳過規格更新（適用於基礎架構/工具/僅文件的變更） |
| `--no-validate` | 跳過驗證（需要確認） |

**範例：**

```bash
# 互動式封存
openspec archive

# 封存特定變更
openspec archive add-dark-mode

# 不提示封存（CI/腳本）
openspec archive add-dark-mode --yes

# 封存不影響規格的工具變更
openspec archive update-ci-config --skip-specs
```

**功能：**

1. 驗證變更（除非加了 `--no-validate`）
2. 提示確認（除非加了 `--yes`）
3. 將差異規格合併至 `openspec/specs/`
4. 將變更資料夾移至 `openspec/changes/archive/YYYY-MM-DD-<name>/`

---

## 工作流程指令

這些指令支援產出物驅動的 OPSX 工作流程。對人工查看進度和代理決定下一步都很有用。

### `openspec status`

顯示變更的產出物完成狀態。

```
openspec status [options]
```

**選項：**

| 選項 | 說明 |
|------|------|
| `--change <id>` | 變更名稱（非互動式模式下必要） |
| `--schema <name>` | 結構描述覆寫（從變更的設定自動偵測） |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 互動式狀態查看
openspec status

# 特定變更的狀態
openspec status --change add-dark-mode

# 供代理使用的 JSON
openspec status --change add-dark-mode --json
```

**輸出（文字）：**

```
變更：add-dark-mode
結構描述：spec-driven
進度：2/4 產出物完成

[x] proposal
[ ] design
[x] specs
[-] tasks（被阻擋：design）
```

**輸出（JSON）：**

```json
{
  "changeName": "add-dark-mode",
  "schemaName": "spec-driven",
  "isComplete": false,
  "applyRequires": ["tasks"],
  "artifacts": [
    {"id": "proposal", "outputPath": "proposal.md", "status": "done"},
    {"id": "design", "outputPath": "design.md", "status": "ready"},
    {"id": "specs", "outputPath": "specs/**/*.md", "status": "done"},
    {"id": "tasks", "outputPath": "tasks.md", "status": "blocked", "missingDeps": ["design"]}
  ]
}
```

---

### `openspec instructions`

取得建立產出物或套用任務的豐富指示。供 AI 代理了解下一步要建立什麼時使用。

```
openspec instructions [artifact] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `artifact` | 否 | 產出物 ID：`proposal`、`specs`、`design`、`tasks` 或 `apply` |

**選項：**

| 選項 | 說明 |
|------|------|
| `--change <id>` | 變更名稱（非互動式模式下必要） |
| `--schema <name>` | 結構描述覆寫 |
| `--json` | 以 JSON 輸出 |

**特殊情況：** 使用 `apply` 作為產出物以取得任務實作指示。

**範例：**

```bash
# 取得下一個產出物的指示
openspec instructions --change add-dark-mode

# 取得特定產出物的指示
openspec instructions design --change add-dark-mode

# 取得套用/實作指示
openspec instructions apply --change add-dark-mode

# 供代理使用的 JSON
openspec instructions design --change add-dark-mode --json
```

**輸出包含：**

- 產出物的範本內容
- 來自設定的專案背景資訊
- 依賴產出物的內容
- 來自設定的各產出物規則

---

### `openspec templates`

顯示結構描述中所有產出物的已解析範本路徑。

```
openspec templates [options]
```

**選項：**

| 選項 | 說明 |
|------|------|
| `--schema <name>` | 要檢視的結構描述（預設：`spec-driven`） |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 顯示預設結構描述的範本路徑
openspec templates

# 顯示自訂結構描述的範本
openspec templates --schema my-workflow

# 供程式化使用的 JSON
openspec templates --json
```

**輸出（文字）：**

```
結構描述：spec-driven

範本：
  proposal  → ~/.openspec/schemas/spec-driven/templates/proposal.md
  specs     → ~/.openspec/schemas/spec-driven/templates/specs.md
  design    → ~/.openspec/schemas/spec-driven/templates/design.md
  tasks     → ~/.openspec/schemas/spec-driven/templates/tasks.md
```

---

### `openspec schemas`

列出可用的工作流程結構描述及其說明和產出物流程。

```
openspec schemas [options]
```

**選項：**

| 選項 | 說明 |
|------|------|
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
openspec schemas
```

**輸出：**

```
可用的結構描述：

  spec-driven（套件）
    預設的規格驅動開發工作流程
    流程：proposal → specs → design → tasks

  my-custom（專案）
    此專案的自訂工作流程
    流程：research → proposal → tasks
```

---

## 結構描述指令

用於建立和管理自訂工作流程結構描述的指令。

### `openspec schema init`

建立新的專案本地結構描述。

```
openspec schema init <name> [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `name` | 是 | 結構描述名稱（kebab-case） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--description <text>` | 結構描述說明 |
| `--artifacts <list>` | 逗號分隔的產出物 ID（預設：`proposal,specs,design,tasks`） |
| `--default` | 設為專案預設結構描述 |
| `--no-default` | 不提示設為預設 |
| `--force` | 覆寫現有結構描述 |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 互動式建立結構描述
openspec schema init research-first

# 非互動式，指定特定產出物
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

**建立的內容：**

```
openspec/schemas/<name>/
├── schema.yaml           # 結構描述定義
└── templates/
    ├── proposal.md       # 每個產出物的範本
    ├── specs.md
    ├── design.md
    └── tasks.md
```

---

### `openspec schema fork`

將現有結構描述複製至你的專案以進行自訂。

```
openspec schema fork <source> [name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `source` | 是 | 要複製的結構描述 |
| `name` | 否 | 新結構描述名稱（預設：`<source>-custom`） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--force` | 覆寫現有的目標 |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 衍生內建的 spec-driven 結構描述
openspec schema fork spec-driven my-workflow
```

---

### `openspec schema validate`

驗證結構描述的結構和範本。

```
openspec schema validate [name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `name` | 否 | 要驗證的結構描述（若省略則驗證所有） |

**選項：**

| 選項 | 說明 |
|------|------|
| `--verbose` | 顯示詳細驗證步驟 |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 驗證特定結構描述
openspec schema validate my-workflow

# 驗證所有結構描述
openspec schema validate
```

---

### `openspec schema which`

顯示結構描述從哪裡解析（適合偵錯優先順序）。

```
openspec schema which [name] [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `name` | 否 | 結構描述名稱 |

**選項：**

| 選項 | 說明 |
|------|------|
| `--all` | 列出所有結構描述及其來源 |
| `--json` | 以 JSON 輸出 |

**範例：**

```bash
# 確認結構描述來自哪裡
openspec schema which spec-driven
```

**輸出：**

```
spec-driven 解析自：package
  來源：/usr/local/lib/node_modules/@fission-ai/openspec/schemas/spec-driven
```

**結構描述優先順序：**

1. 專案：`openspec/schemas/<name>/`
2. 使用者：`~/.local/share/openspec/schemas/<name>/`
3. 套件：內建結構描述

---

## 設定指令

### `openspec config`

查看和修改全域 OpenSpec 設定。

```
openspec config <subcommand> [options]
```

**子指令：**

| 子指令 | 說明 |
|--------|------|
| `path` | 顯示設定檔位置 |
| `list` | 顯示所有當前設定 |
| `get <key>` | 取得特定值 |
| `set <key> <value>` | 設定值 |
| `unset <key>` | 移除一個鍵 |
| `reset` | 重設為預設值 |
| `edit` | 在 `$EDITOR` 中開啟 |
| `profile [preset]` | 以互動方式或透過預設設定工作流程設定檔 |

**範例：**

```bash
# 顯示設定檔路徑
openspec config path

# 列出所有設定
openspec config list

# 取得特定值
openspec config get telemetry.enabled

# 設定值
openspec config set telemetry.enabled false

# 明確設定字串值
openspec config set user.name "My Name" --string

# 移除自訂設定
openspec config unset user.name

# 重設所有設定
openspec config reset --all --yes

# 在你的編輯器中編輯設定
openspec config edit

# 以行動式精靈設定設定檔
openspec config profile

# 快速預設：將工作流程切換至 core（保留傳遞模式）
openspec config profile core
```

`openspec config profile` 從目前狀態摘要開始，然後讓你選擇：
- 變更傳遞方式 + 工作流程
- 只變更傳遞方式
- 只變更工作流程
- 保留當前設定（退出）

若你保留當前設定，不會寫入任何變更，也不會顯示更新提示。
若設定沒有變更，但目前專案檔案與你的全域設定檔/傳遞方式不同步，OpenSpec 會顯示警告並建議執行 `openspec update`。
按 `Ctrl+C` 也會乾淨地取消流程（無堆疊追蹤）並以程式碼 `130` 退出。
在工作流程核取清單中，`[x]` 表示在全域設定中選擇了該工作流程。要將這些選擇套用至專案檔案，執行 `openspec update`（或在專案內部提示時選擇「立即套用變更至此專案？」）。

**互動式範例：**

```bash
# 只更新傳遞方式
openspec config profile
# 選擇：只變更傳遞方式
# 選擇傳遞方式：只有技能

# 只更新工作流程
openspec config profile
# 選擇：只變更工作流程
# 在核取清單中切換工作流程，然後確認
```

---

## 工具指令

### `openspec feedback`

提交關於 OpenSpec 的意見回饋。建立 GitHub issue。

```
openspec feedback <message> [options]
```

**參數：**

| 參數 | 必要 | 說明 |
|------|------|------|
| `message` | 是 | 意見回饋訊息 |

**選項：**

| 選項 | 說明 |
|------|------|
| `--body <text>` | 詳細說明 |

**需求：** 必須安裝並驗證 GitHub CLI（`gh`）。

**範例：**

```bash
openspec feedback "Add support for custom artifact types" \
  --body "I'd like to define my own artifact types beyond the built-in ones."
```

---

### `openspec completion`

管理 OpenSpec CLI 的 shell 補全。

```
openspec completion <subcommand> [shell]
```

**子指令：**

| 子指令 | 說明 |
|--------|------|
| `generate [shell]` | 將補全腳本輸出至 stdout |
| `install [shell]` | 為你的 shell 安裝補全 |
| `uninstall [shell]` | 移除已安裝的補全 |

**支援的 shell：** `bash`、`zsh`、`fish`、`powershell`

**範例：**

```bash
# 安裝補全（自動偵測 shell）
openspec completion install

# 為特定 shell 安裝
openspec completion install zsh

# 生成腳本供手動安裝
openspec completion generate bash > ~/.bash_completion.d/openspec

# 解除安裝
openspec completion uninstall
```

---

## 結束碼

| 碼 | 意義 |
|----|------|
| `0` | 成功 |
| `1` | 錯誤（驗證失敗、缺少檔案等） |

---

## 環境變數

| 變數 | 說明 |
|------|------|
| `OPENSPEC_CONCURRENCY` | 大量驗證的預設並行度（預設：6） |
| `EDITOR` 或 `VISUAL` | `openspec config edit` 的編輯器 |
| `NO_COLOR` | 設定時停用彩色輸出 |

---

## 相關文件

- [指令](commands.md) — AI 斜線指令（`/opsx:propose`、`/opsx:apply` 等）
- [工作流程](workflows.md) — 常見模式和各指令的使用時機
- [自訂](customization.md) — 建立自訂結構描述和範本
- [入門指南](getting-started.md) — 首次設定指南
