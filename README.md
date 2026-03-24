<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec">
    <picture>
      <source srcset="assets/openspec_bg.png">
      <img src="assets/openspec_bg.png" alt="OpenSpec 標誌">
    </picture>
  </a>
</p>

<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml"><img alt="CI" src="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml/badge.svg" /></a>
  <a href="https://www.npmjs.com/package/@fission-ai/openspec"><img alt="npm 版本" src="https://img.shields.io/npm/v/@fission-ai/openspec?style=flat-square" /></a>
  <a href="./LICENSE"><img alt="授權條款：MIT" src="https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square" /></a>
  <a href="https://discord.gg/YctCnvvshC"><img alt="Discord" src="https://img.shields.io/discord/1411657095639601154?style=flat-square&logo=discord&logoColor=white&label=Discord&suffix=%20online" /></a>
</p>

<details>
<summary><strong>最受喜愛的規格框架。</strong></summary>

[![Stars](https://img.shields.io/github/stars/Fission-AI/OpenSpec?style=flat-square&label=Stars)](https://github.com/Fission-AI/OpenSpec/stargazers)
[![Downloads](https://img.shields.io/npm/dm/@fission-ai/openspec?style=flat-square&label=Downloads/mo)](https://www.npmjs.com/package/@fission-ai/openspec)
[![Contributors](https://img.shields.io/github/contributors/Fission-AI/OpenSpec?style=flat-square&label=Contributors)](https://github.com/Fission-AI/OpenSpec/graphs/contributors)

</details>
<p></p>
我們的理念：

```text
→ 流動而非僵化
→ 迭代而非瀑布式
→ 簡單而非複雜
→ 為棕地專案而建，不只是綠地專案
→ 從個人專案到企業級皆可擴展
```

> [!TIP]
> **全新工作流程現已上線！** 我們以全新的製品導向工作流程重建了 OpenSpec。
>
> 執行 `/opsx:propose "你的想法"` 即可開始。→ [了解更多](docs/opsx.md)

<p align="center">
  追蹤 <a href="https://x.com/0xTab">X 上的 @0xTab</a> 以獲取更新 · 加入 <a href="https://discord.gg/YctCnvvshC">OpenSpec Discord</a> 尋求協助與討論。
</p>

### 團隊使用

在團隊中使用 OpenSpec？[寄送 Email](mailto:teams@openspec.dev) 以取得我們 Slack 頻道的使用權限。

<!-- TODO: Add GIF demo of /opsx:propose → /opsx:archive workflow -->

## 實際操作示範

```text
你：/opsx:propose add-dark-mode
AI：已建立 openspec/changes/add-dark-mode/
     ✓ proposal.md — 說明原因與變更內容
     ✓ specs/       — 需求與情境說明
     ✓ design.md    — 技術實作方式
     ✓ tasks.md     — 實作清單
     準備好開始實作！

你：/opsx:apply
AI：正在執行任務...
     ✓ 1.1 新增佈景主題 context provider
     ✓ 1.2 建立切換元件
     ✓ 2.1 新增 CSS 變數
     ✓ 2.2 串接 localStorage
     所有任務完成！

你：/opsx:archive
AI：已封存至 openspec/changes/archive/2025-01-23-add-dark-mode/
     規格已更新。準備好進行下一個功能。
```

<details>
<summary><strong>OpenSpec 儀表板</strong></summary>

<p align="center">
  <img src="assets/openspec_dashboard.png" alt="OpenSpec 儀表板預覽" width="90%">
</p>

</details>

## 快速開始

**需要 Node.js 20.19.0 或更高版本。**

全域安裝 OpenSpec：

```bash
npm install -g @fission-ai/openspec@latest
```

接著切換到你的專案目錄並初始化：

```bash
cd your-project
openspec init
```

現在告訴你的 AI：`/opsx:propose <你想建構的內容>`

如果你想使用完整工作流程（`/opsx:new`、`/opsx:continue`、`/opsx:ff`、`/opsx:verify`、`/opsx:sync`、`/opsx:bulk-archive`、`/opsx:onboard`），請使用 `openspec config profile` 選擇，並以 `openspec update` 套用。

> [!NOTE]
> 不確定你的工具是否支援？[查看完整清單](docs/supported-tools.md) — 我們支援 20+ 種工具且持續增加中。
>
> 也支援 pnpm、yarn、bun 和 nix。[查看安裝選項](docs/installation.md)。

## 文件

→ **[入門指南](docs/getting-started.md)**：第一步<br>
→ **[工作流程](docs/workflows.md)**：組合與模式<br>
→ **[指令](docs/commands.md)**：斜線指令與技能<br>
→ **[CLI](docs/cli.md)**：終端機參考<br>
→ **[支援工具](docs/supported-tools.md)**：工具整合與安裝路徑<br>
→ **[概念](docs/concepts.md)**：整體架構說明<br>
→ **[多語言](docs/multi-language.md)**：多語言支援<br>
→ **[自訂化](docs/customization.md)**：個人化設定


## 為什麼選擇 OpenSpec？

AI 程式碼助理功能強大，但當需求只存在於聊天記錄中時，結果往往難以預測。OpenSpec 加入了一層輕量的規格層，讓你在撰寫任何程式碼之前，先就要建構的內容達成共識。

- **先達成共識，再開始建構** — 人類與 AI 在撰寫程式碼前先對齊規格
- **保持有條理** — 每個變更都有自己的資料夾，包含提案、規格、設計與任務
- **流暢地工作** — 隨時更新任何製品，沒有僵化的階段關卡
- **使用你的工具** — 透過斜線指令支援 20+ 種 AI 助理

### 與競品比較

**vs. [Spec Kit](https://github.com/github/spec-kit)**（GitHub）— 完整但厚重。僵化的階段關卡、大量 Markdown、需要 Python 設定。OpenSpec 更輕量，讓你自由迭代。

**vs. [Kiro](https://kiro.dev)**（AWS）— 功能強大，但被鎖定在他們的 IDE 中，且僅限於 Claude 模型。OpenSpec 與你現有的工具搭配使用。

**vs. 什麼都不用** — 沒有規格的 AI 程式開發意味著模糊的提示與難以預測的結果。OpenSpec 帶來可預測性，同時不增加繁瑣的流程。

## 更新 OpenSpec

**升級套件**

```bash
npm install -g @fission-ai/openspec@latest
```

**更新 AI 指令**

在每個專案中執行以下指令，重新產生 AI 指引並確保最新的斜線指令已啟用：

```bash
openspec update
```

## 使用注意事項

**模型選擇**：OpenSpec 在高推理能力的模型上表現最佳。我們建議在規劃與實作中使用 Opus 4.5 和 GPT 5.2。

**上下文管理**：OpenSpec 在乾淨的上下文視窗下效益最佳。在開始實作前請清除上下文，並在整個工作期間維持良好的上下文管理習慣。

## 貢獻

**小型修正** — 錯誤修復、錯字更正及小幅改善可直接提交為 PR。

**較大的變更** — 對於新功能、重大重構或架構變更，請先提交 OpenSpec 變更提案，讓我們在實作開始前就意圖與目標達成共識。

撰寫提案時，請牢記 OpenSpec 的理念：我們服務各種不同的使用者，涵蓋不同的程式碼代理、模型與使用情境。變更應對所有人都能良好運作。

**歡迎 AI 生成的程式碼** — 只要經過測試與驗證即可。包含 AI 生成程式碼的 PR 應說明所使用的程式碼代理與模型（例如：「使用 Claude Code 的 claude-opus-4-5-20251101 生成」）。

### 開發

- 安裝相依套件：`pnpm install`
- 建置：`pnpm run build`
- 測試：`pnpm test`
- 本機開發 CLI：`pnpm run dev` 或 `pnpm run dev:cli`
- 慣例提交（單行）：`type(scope): subject`

## 其他

<details>
<summary><strong>遙測</strong></summary>

OpenSpec 收集匿名使用統計資料。

我們僅收集指令名稱與版本以了解使用模式。不收集任何引數、路徑、內容或個人識別資訊。在 CI 環境中自動停用。

**停用遙測：** `export OPENSPEC_TELEMETRY=0` 或 `export DO_NOT_TRACK=1`

</details>

<details>
<summary><strong>維護者與顧問</strong></summary>

請參閱 [MAINTAINERS.md](MAINTAINERS.md) 以查看協助引導專案方向的核心維護者與顧問名單。

</details>



## 授權條款

MIT
