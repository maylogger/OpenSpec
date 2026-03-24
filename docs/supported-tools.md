# 支援的工具

OpenSpec 可與多種 AI 程式碼助理搭配使用。執行 `openspec init` 時，OpenSpec 會根據你選擇的設定檔/工作流程及傳遞模式來設定所選工具。

## 運作方式

對於每個選定的工具，OpenSpec 可安裝：

1. **技能（Skills）**（若傳遞方式包含技能）：`.../skills/openspec-*/SKILL.md`
2. **指令（Commands）**（若傳遞方式包含指令）：工具特定的 `opsx-*` 指令檔案

預設情況下，OpenSpec 使用 `core` 設定檔，包含：
- `propose`
- `explore`
- `apply`
- `archive`

可透過 `openspec config profile` 啟用擴充工作流程（`new`、`continue`、`ff`、`verify`、`sync`、`bulk-archive`、`onboard`），然後執行 `openspec update`。

## 工具目錄參考

| 工具（ID）| 技能路徑模式 | 指令路徑模式 |
|-----------|-------------|-------------|
| Amazon Q Developer（`amazon-q`） | `.amazonq/skills/openspec-*/SKILL.md` | `.amazonq/prompts/opsx-<id>.md` |
| Antigravity（`antigravity`） | `.agent/skills/openspec-*/SKILL.md` | `.agent/workflows/opsx-<id>.md` |
| Auggie（`auggie`） | `.augment/skills/openspec-*/SKILL.md` | `.augment/commands/opsx-<id>.md` |
| Claude Code（`claude`） | `.claude/skills/openspec-*/SKILL.md` | `.claude/commands/opsx/<id>.md` |
| Cline（`cline`） | `.cline/skills/openspec-*/SKILL.md` | `.clinerules/workflows/opsx-<id>.md` |
| CodeBuddy（`codebuddy`） | `.codebuddy/skills/openspec-*/SKILL.md` | `.codebuddy/commands/opsx/<id>.md` |
| Codex（`codex`） | `.codex/skills/openspec-*/SKILL.md` | `$CODEX_HOME/prompts/opsx-<id>.md`\* |
| Continue（`continue`） | `.continue/skills/openspec-*/SKILL.md` | `.continue/prompts/opsx-<id>.prompt` |
| CoStrict（`costrict`） | `.cospec/skills/openspec-*/SKILL.md` | `.cospec/openspec/commands/opsx-<id>.md` |
| Crush（`crush`） | `.crush/skills/openspec-*/SKILL.md` | `.crush/commands/opsx/<id>.md` |
| Cursor（`cursor`） | `.cursor/skills/openspec-*/SKILL.md` | `.cursor/commands/opsx-<id>.md` |
| Factory Droid（`factory`） | `.factory/skills/openspec-*/SKILL.md` | `.factory/commands/opsx-<id>.md` |
| Gemini CLI（`gemini`） | `.gemini/skills/openspec-*/SKILL.md` | `.gemini/commands/opsx/<id>.toml` |
| GitHub Copilot（`github-copilot`） | `.github/skills/openspec-*/SKILL.md` | `.github/prompts/opsx-<id>.prompt.md`\*\* |
| iFlow（`iflow`） | `.iflow/skills/openspec-*/SKILL.md` | `.iflow/commands/opsx-<id>.md` |
| Kilo Code（`kilocode`） | `.kilocode/skills/openspec-*/SKILL.md` | `.kilocode/workflows/opsx-<id>.md` |
| Kiro（`kiro`） | `.kiro/skills/openspec-*/SKILL.md` | `.kiro/prompts/opsx-<id>.prompt.md` |
| OpenCode（`opencode`） | `.opencode/skills/openspec-*/SKILL.md` | `.opencode/commands/opsx-<id>.md` |
| Pi（`pi`） | `.pi/skills/openspec-*/SKILL.md` | `.pi/prompts/opsx-<id>.md` |
| Qoder（`qoder`） | `.qoder/skills/openspec-*/SKILL.md` | `.qoder/commands/opsx/<id>.md` |
| Qwen Code（`qwen`） | `.qwen/skills/openspec-*/SKILL.md` | `.qwen/commands/opsx-<id>.toml` |
| RooCode（`roocode`） | `.roo/skills/openspec-*/SKILL.md` | `.roo/commands/opsx-<id>.md` |
| Trae（`trae`） | `.trae/skills/openspec-*/SKILL.md` | 不生成（無指令轉接器；使用基於技能的 `/openspec-*` 呼叫） |
| Windsurf（`windsurf`） | `.windsurf/skills/openspec-*/SKILL.md` | `.windsurf/workflows/opsx-<id>.md` |

\* Codex 指令安裝於全域 Codex 目錄（若設定了 `$CODEX_HOME` 則為 `$CODEX_HOME/prompts/`，否則為 `~/.codex/prompts/`），而非你的專案目錄。

\*\* GitHub Copilot 提示檔案可在 IDE 擴充套件（VS Code、JetBrains、Visual Studio）中識別為自訂斜線指令。Copilot CLI 目前不直接使用 `.github/prompts/*.prompt.md`。

## 非互動式設定

用於 CI/CD 或腳本化設定，使用 `--tools`（可選加 `--profile`）：

```bash
# 設定特定工具
openspec init --tools claude,cursor

# 設定所有支援的工具
openspec init --tools all

# 跳過工具設定
openspec init --tools none

# 此次初始化覆寫設定檔
openspec init --profile core
```

**可用工具 ID（`--tools`）：** `amazon-q`、`antigravity`、`auggie`、`claude`、`cline`、`codex`、`codebuddy`、`continue`、`costrict`、`crush`、`cursor`、`factory`、`gemini`、`github-copilot`、`iflow`、`kilocode`、`kiro`、`opencode`、`pi`、`qoder`、`qwen`、`roocode`、`trae`、`windsurf`

## 依工作流程的安裝

OpenSpec 根據選定的工作流程安裝產出物：

- **Core 設定檔（預設）：** `propose`、`explore`、`apply`、`archive`
- **自訂選擇：** 所有工作流程 ID 的任意子集：
  `propose`、`explore`、`new`、`continue`、`apply`、`ff`、`sync`、`archive`、`bulk-archive`、`verify`、`onboard`

換言之，技能/指令數量取決於設定檔和傳遞方式，並非固定不變。

## 生成的技能名稱

根據設定檔/工作流程設定選擇後，OpenSpec 會生成以下技能：

- `openspec-propose`
- `openspec-explore`
- `openspec-new-change`
- `openspec-continue-change`
- `openspec-apply-change`
- `openspec-ff-change`
- `openspec-sync-specs`
- `openspec-archive-change`
- `openspec-bulk-archive-change`
- `openspec-verify-change`
- `openspec-onboard`

指令行為請參閱 [指令](commands.md)，`init`/`update` 選項請參閱 [CLI](cli.md)。

## 相關資源

- [CLI 參考](cli.md) — 終端機指令
- [指令](commands.md) — 斜線指令與技能
- [入門指南](getting-started.md) — 首次設定
