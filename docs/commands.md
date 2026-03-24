# 指令

這是 OpenSpec 斜線指令的參考文件。這些指令在你的 AI 程式碼助理聊天介面（例如 Claude Code、Cursor、Windsurf）中呼叫。

工作流程模式和各指令的使用時機請參閱 [工作流程](workflows.md)。CLI 指令請參閱 [CLI](cli.md)。

## 快速參考

### 預設快速路徑（`core` 設定檔）

| 指令 | 用途 |
|------|------|
| `/opsx:propose` | 一步建立變更和規劃產出物 |
| `/opsx:explore` | 在承諾進行變更前思考想法 |
| `/opsx:apply` | 實作變更中的任務 |
| `/opsx:archive` | 封存已完成的變更 |

### 擴充工作流程指令（自訂工作流程選擇）

| 指令 | 用途 |
|------|------|
| `/opsx:new` | 開始新的變更架構 |
| `/opsx:continue` | 根據依賴關係建立下一個產出物 |
| `/opsx:ff` | 快進：一次建立所有規劃產出物 |
| `/opsx:verify` | 驗證實作符合產出物 |
| `/opsx:sync` | 將差異規格合併至主要規格 |
| `/opsx:bulk-archive` | 一次封存多個變更 |
| `/opsx:onboard` | 完整工作流程的引導教學 |

預設全域設定檔為 `core`。要啟用擴充工作流程指令，執行 `openspec config profile`，選擇工作流程，然後在你的專案中執行 `openspec update`。

---

## 指令參考

### `/opsx:propose`

一步建立新的變更和規劃產出物。這是 `core` 設定檔中的預設開始指令。

**語法：**
```text
/opsx:propose [change-name-or-description]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name-or-description` | 否 | kebab-case 名稱或純語言的變更描述 |

**功能：**
- 建立 `openspec/changes/<change-name>/`
- 生成實作前所需的產出物（`spec-driven` 的：提案、規格、設計、任務）
- 當變更準備好執行 `/opsx:apply` 時停止

**範例：**
```text
你：/opsx:propose add-dark-mode

AI：已建立 openspec/changes/add-dark-mode/
     ✓ proposal.md
     ✓ specs/ui/spec.md
     ✓ design.md
     ✓ tasks.md
     準備好實作。執行 /opsx:apply。
```

**提示：**
- 用於最快的端對端路徑
- 若想逐步控制產出物，啟用擴充工作流程並使用 `/opsx:new` + `/opsx:continue`

---

### `/opsx:explore`

在承諾進行變更之前，思考想法、調查問題並釐清需求。

**語法：**
```
/opsx:explore [topic]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `topic` | 否 | 你想探索或調查的內容 |

**功能：**
- 開啟不需要任何結構的探索性對話
- 調查程式碼庫以回答問題
- 比較選項和方法
- 建立視覺圖表來釐清思路
- 當想法結晶時，可轉換至 `/opsx:propose`（預設）或 `/opsx:new`（擴充工作流程）

**範例：**
```text
你：/opsx:explore

AI：你想探索什麼？

你：我們應如何處理行動應用程式的驗證？

AI：讓我調查你目前的驗證設定...
     [分析程式碼庫]

     我看到你的 Web 應用程式使用基於 session 的驗證。
     對於行動裝置，我們有三個主要選項：

     1. JWT token（無狀態，可離線運作）
     2. OAuth2（若需要第三方登入）
     3. 擴展現有 session（更簡單，但需要連線）

     你的 API 已設定 CORS。哪個方向你感興趣？

你：我們來用 JWT。可以開始一個變更嗎？

AI：準備好了。執行 /opsx:propose add-jwt-auth 來開始。
```

**提示：**
- 在需求不明確或需要調查時使用
- 探索期間不會建立任何產出物
- 適合在決定前比較多種方法
- 可讀取檔案和搜尋程式碼庫

---

### `/opsx:new`

開始新的變更架構。建立變更資料夾，等待你用 `/opsx:continue` 或 `/opsx:ff` 生成產出物。

此指令是擴充工作流程集的一部分（預設 `core` 設定檔不包含）。

**語法：**
```
/opsx:new [change-name] [--schema <schema-name>]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 變更資料夾的名稱（若未提供則提示） |
| `--schema` | 否 | 要使用的工作流程結構描述（預設：來自設定或 `spec-driven`） |

**功能：**
- 建立 `openspec/changes/<change-name>/` 目錄
- 在變更資料夾中建立 `.openspec.yaml` 元資料檔案
- 顯示準備建立的第一個產出物範本
- 若未提供則提示輸入變更名稱和結構描述

**建立的內容：**
```
openspec/changes/<change-name>/
└── .openspec.yaml    # 變更元資料（結構描述、建立日期）
```

**範例：**
```
你：/opsx:new add-dark-mode

AI：已建立 openspec/changes/add-dark-mode/
     結構描述：spec-driven

     準備建立：proposal
     使用 /opsx:continue 建立，或用 /opsx:ff 建立所有產出物。
```

**提示：**
- 使用描述性名稱：`add-feature`、`fix-bug`、`refactor-module`
- 避免通用名稱如 `update`、`changes`、`wip`
- 結構描述也可在專案設定（`openspec/config.yaml`）中設定

---

### `/opsx:continue`

在依賴關係鏈中建立下一個產出物。每次建立一個產出物，逐步推進。

**語法：**
```
/opsx:continue [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要繼續的變更（若未提供則從背景資訊推斷） |

**功能：**
- 查詢產出物依賴圖
- 顯示哪些產出物準備好了，哪些被阻擋
- 建立第一個準備好的產出物
- 讀取依賴檔案作為背景資訊
- 顯示建立後什麼變為可用

**範例：**
```
你：/opsx:continue

AI：變更：add-dark-mode

     產出物狀態：
     ✓ proposal    （完成）
     ◆ specs       （準備好）
     ◆ design      （準備好）
     ○ tasks       （被阻擋 - 需要：specs）

     正在建立 specs...

     [讀取 proposal.md，根據需求建立規格]

     ✓ 已建立 openspec/changes/add-dark-mode/specs/ui/spec.md

     現在可用：tasks
     執行 /opsx:continue 建立下一個產出物。
```

**提示：**
- 想在繼續前審查每個產出物時使用
- 適合想要控制的複雜變更
- 多個產出物可能同時準備好
- 建立後可以編輯產出物再繼續

---

### `/opsx:ff`

快進產出物建立。一次建立所有規劃產出物。

**語法：**
```
/opsx:ff [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要快進的變更（若未提供則從背景資訊推斷） |

**功能：**
- 按依賴順序建立所有產出物
- 透過待辦清單追蹤進度
- 所有 `apply-required` 產出物完成時停止
- 建立下一個產出物前讀取每個依賴

**範例：**
```
你：/opsx:ff add-dark-mode

AI：快進 add-dark-mode...

     ✓ 建立 proposal.md
     ✓ 建立 specs/ui/spec.md
     ✓ 建立 design.md
     ✓ 建立 tasks.md

     所有規劃產出物完成！
     準備好實作。執行 /opsx:apply 開始。
```

**提示：**
- 當你對要建構的內容有清晰認識時使用
- 比 `/opsx:continue` 更快，適合直接的變更
- 之後仍然可以編輯產出物
- 適合小至中型功能

---

### `/opsx:apply`

實作變更中的任務。逐項處理任務清單，撰寫程式碼並勾選完成的項目。

**語法：**
```
/opsx:apply [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要實作的變更（若未提供則從背景資訊推斷） |

**功能：**
- 讀取 `tasks.md` 並識別未完成的任務
- 逐一處理任務
- 依需要撰寫程式碼、建立檔案、執行測試
- 用核取方塊 `[x]` 標記已完成的任務

**範例：**
```
你：/opsx:apply

AI：正在實作 add-dark-mode...

     讀取 tasks.md：
     - [ ] 1.1 建立 ThemeContext
     - [ ] 1.2 加入 CSS 自訂屬性
     - [ ] 1.3 實作 localStorage 持久化

     正在處理 1.1：建立 ThemeContext...
     [建立 src/context/ThemeContext.tsx]

     ✓ 1.1 完成

     正在處理 1.2：加入 CSS 自訂屬性...
     [更新 src/styles/globals.css]

     ✓ 1.2 完成
     ...
```

**提示：**
- 若中斷可以從上次離開的地方繼續
- 指定變更名稱可用於並行變更
- 完成狀態透過 `tasks.md` 核取方塊追蹤

---

### `/opsx:verify`

驗證實作符合你的變更產出物。從完整性、正確性和一致性三個維度檢查。

**語法：**
```
/opsx:verify [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要驗證的變更（若未提供則從背景資訊推斷） |

**功能：**
- 從三個維度檢查實作品質
- 在程式碼庫中搜尋實作證據
- 將問題分類為 CRITICAL（嚴重）、WARNING（警告）或 SUGGESTION（建議）
- 不阻止封存，但呈現問題

**驗證維度：**

| 維度 | 驗證的內容 |
|------|-----------|
| **完整性** | 所有任務完成、所有需求已實作、情境已涵蓋 |
| **正確性** | 實作符合規格意圖、邊緣情況已處理 |
| **一致性** | 設計決策反映在程式碼中、模式一致 |

**範例：**
```
你：/opsx:verify

AI：正在驗證 add-dark-mode...

     完整性
     ✓ tasks.md 中的所有 8 個任務都已勾選
     ✓ 規格中的所有需求都有對應的程式碼
     ⚠ 情境「系統偏好偵測」沒有測試覆蓋

     正確性
     ✓ 實作符合規格意圖
     ✓ 情境中的邊緣情況已處理
     ✓ 錯誤狀態符合規格定義

     一致性
     ✓ 設計決策反映在程式碼結構中
     ✓ 命名慣例與 design.md 一致
     ⚠ 設計提到「CSS 變數」但實作使用 Tailwind classes

     摘要
     ─────────────────────────────
     嚴重問題：0
     警告：2
     準備封存：是（有警告）

     建議：
     1. 加入系統偏好偵測的測試
     2. 更新 design.md 以反映 Tailwind 的使用，或重構為使用 CSS 變數
```

**提示：**
- 封存前執行以及早發現不匹配
- 警告不阻止封存，但表示潛在問題
- 適合在提交前審查 AI 的工作
- 可以發現產出物與實作之間的偏差

---

### `/opsx:sync`

**選用指令。** 將變更中的差異規格合併至主要規格。封存會在需要時提示同步，所以通常不需要手動執行此指令。

**語法：**
```
/opsx:sync [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要同步的變更（若未提供則從背景資訊推斷） |

**功能：**
- 從變更資料夾讀取差異規格
- 解析 ADDED/MODIFIED/REMOVED/RENAMED 章節
- 將變更合併至主要 `openspec/specs/` 目錄
- 保留差異中未提及的現有內容
- 不封存變更（保持活躍）

**範例：**
```text
你：/opsx:sync

AI：正在同步 add-dark-mode 差異規格...

     讀取：openspec/changes/add-dark-mode/specs/ui/spec.md
     目標：openspec/specs/ui/spec.md

     要套用的變更：
     ✓ ADDED：主題選擇需求（2 個情境）
     ✓ ADDED：無障礙性需求（1 個情境）

     合併中...
     ✓ openspec/specs/ui/spec.md 已更新

     變更保持活躍。準備完成時執行 /opsx:archive。
```

**手動使用時機：**

| 情境 | 使用同步？ |
|------|----------|
| 長期執行的變更，希望在封存前將規格加入主要規格 | 是 |
| 多個並行變更需要更新後的基礎規格 | 是 |
| 想分開預覽/審查合併 | 是 |
| 快速變更，直接進行封存 | 否（封存會處理） |

**提示：**
- 同步是智能的，不是複製貼上
- 可以在不重複的情況下將情境加入現有需求
- 同步後變更保持活躍（未封存）
- 大多數使用者永遠不需要直接呼叫此指令 — 封存會在需要時提示

---

### `/opsx:archive`

封存已完成的變更。完成變更並將其移至封存資料夾。

**語法：**
```
/opsx:archive [change-name]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-name` | 否 | 要封存的變更（若未提供則從背景資訊推斷） |

**功能：**
- 檢查產出物完成狀態
- 檢查任務完成情況（若未完成則警告）
- 提供同步差異規格（若尚未同步）
- 將變更資料夾移至 `openspec/changes/archive/YYYY-MM-DD-<name>/`
- 保留所有產出物作為稽核記錄

**範例：**
```text
你：/opsx:archive

AI：正在封存 add-dark-mode...

     產出物狀態：
     ✓ proposal.md 存在
     ✓ specs/ 存在
     ✓ design.md 存在
     ✓ tasks.md 存在（8/8 任務完成）

     差異規格：尚未同步
     → 現在同步？（建議）

你：是

AI：✓ 已同步規格至 openspec/specs/ui/spec.md
     ✓ 已移至 openspec/changes/archive/2025-01-24-add-dark-mode/

     變更封存成功。
```

**提示：**
- 封存不會因任務未完成而阻止，但會警告
- 差異規格可在封存時或之前同步
- 已封存的變更保留供歷史查閱
- 先使用 `/opsx:verify` 以及早發現問題

---

### `/opsx:bulk-archive`

一次封存多個已完成的變更。處理變更之間的規格衝突。

**語法：**
```
/opsx:bulk-archive [change-names...]
```

**參數：**
| 參數 | 必要 | 說明 |
|------|------|------|
| `change-names` | 否 | 要封存的特定變更（若未提供則提示選擇） |

**功能：**
- 列出所有已完成的變更
- 封存前驗證每個變更
- 偵測變更之間的規格衝突
- 透過檢查實際實作來解決衝突
- 按時間順序封存

**範例：**
```
你：/opsx:bulk-archive

AI：找到 3 個已完成的變更：
     - add-dark-mode（8/8 任務完成）
     - fix-login-bug（3/3 任務完成）
     - update-footer（2/2 任務完成）

     正在檢查規格衝突...
     ⚠ add-dark-mode 和 update-footer 都觸及 specs/ui/

     正在檢查程式碼庫以解決...
     兩個變更都已實作。將按時間順序合併：
     1. add-dark-mode（1 月 20 日建立）
     2. update-footer（1 月 22 日建立）

     封存全部 3 個變更？

你：是

AI：✓ 已封存 add-dark-mode
     ✓ 已封存 fix-login-bug
     ✓ 已封存 update-footer
     規格已合併：add-dark-mode → update-footer
```

**提示：**
- 適合並行工作流
- 衝突解決是自主的（檢查程式碼庫）
- 變更按建立順序封存
- 覆寫規格內容前提示確認

---

### `/opsx:onboard`

引導完整 OpenSpec 工作流程的上手教學。使用你實際程式碼庫的互動式教學。

**語法：**
```
/opsx:onboard
```

**功能：**
- 帶著旁白引導完整工作流程週期
- 掃描你的程式碼庫尋找真實的改善機會
- 建立具有真實產出物的實際變更
- 實作實際工作（小型、安全的變更）
- 封存已完成的變更
- 執行時說明每個步驟

**階段：**
1. 歡迎和程式碼庫分析
2. 尋找改善機會
3. 建立變更（`/opsx:new`）
4. 撰寫提案
5. 建立規格
6. 撰寫設計
7. 建立任務
8. 實作任務（`/opsx:apply`）
9. 驗證實作
10. 封存變更
11. 摘要和後續步驟

**範例：**
```
你：/opsx:onboard

AI：歡迎使用 OpenSpec！

     我將使用你的實際程式碼庫引導你完成完整工作流程。
     我們會找到一些小東西來改善，為其建立適當的變更，
     實作它，並封存它。

     讓我掃描你的程式碼庫尋找機會...

     [分析程式碼庫]

     我找到了幾個我們可以處理的事情：
     1. 在聯絡表單加入輸入驗證
     2. 改善驗證流程中的錯誤訊息
     3. 在非同步按鈕加入載入狀態

     哪個讓你感興趣？（或提出其他建議）
```

**提示：**
- 最適合學習工作流程的新使用者
- 使用真實程式碼，而非玩具範例
- 建立可保留或捨棄的真實變更
- 需要 15-30 分鐘完成

---

## 各 AI 工具的指令語法

不同的 AI 工具使用略有不同的指令語法。使用符合你工具的格式：

| 工具 | 語法範例 |
|------|---------|
| Claude Code | `/opsx:propose`、`/opsx:apply` |
| Cursor | `/opsx-propose`、`/opsx-apply` |
| Windsurf | `/opsx-propose`、`/opsx-apply` |
| Copilot（IDE） | `/opsx-propose`、`/opsx-apply` |
| Trae | 基於技能的呼叫，如 `/openspec-propose`、`/openspec-apply-change`（不生成 `opsx-*` 指令檔案） |

各工具的意圖相同，但指令的呈現方式可能因整合而異。

> **注意：** GitHub Copilot 指令（`.github/prompts/*.prompt.md`）只在 IDE 擴充套件（VS Code、JetBrains、Visual Studio）中可用。GitHub Copilot CLI 目前不支援自訂提示檔案 — 詳情和解決方法請參閱 [支援的工具](supported-tools.md)。

---

## 舊版指令

這些指令使用較舊的「一次全部」工作流程。它們仍然有效，但建議使用 OPSX 指令。

| 指令 | 功能 |
|------|------|
| `/openspec:proposal` | 一次建立所有產出物（提案、規格、設計、任務） |
| `/openspec:apply` | 實作變更 |
| `/openspec:archive` | 封存變更 |

**何時使用舊版指令：**
- 使用舊工作流程的現有專案
- 不需要逐步建立產出物的簡單變更
- 偏好「全有或全無」方法

**遷移至 OPSX：**
舊版變更可以繼續使用 OPSX 指令。產出物結構是相容的。

---

## 疑難排解

### 「找不到變更」

指令無法識別要處理哪個變更。

**解決方案：**
- 明確指定變更名稱：`/opsx:apply add-dark-mode`
- 確認變更資料夾存在：`openspec list`
- 確認你在正確的專案目錄中

### 「沒有準備好的產出物」

所有產出物要麼已完成，要麼因缺少依賴關係而被阻擋。

**解決方案：**
- 執行 `openspec status --change <name>` 查看阻擋原因
- 確認所需的產出物是否存在
- 先建立缺少的依賴產出物

### 「找不到結構描述」

指定的結構描述不存在。

**解決方案：**
- 列出可用的結構描述：`openspec schemas`
- 確認結構描述名稱的拼寫
- 若是自訂結構描述，先建立它：`openspec schema init <name>`

### 指令未被識別

AI 工具無法識別 OpenSpec 指令。

**解決方案：**
- 確認 OpenSpec 已初始化：`openspec init`
- 重新生成技能：`openspec update`
- 確認 `.claude/skills/` 目錄存在（對於 Claude Code）
- 重新啟動你的 AI 工具以讀取新技能

### 產出物未正確生成

AI 建立了不完整或不正確的產出物。

**解決方案：**
- 在 `openspec/config.yaml` 中加入專案背景資訊
- 為特定指引加入各產出物規則
- 在你的變更描述中提供更多細節
- 使用 `/opsx:continue` 而非 `/opsx:ff` 以獲得更多控制

---

## 下一步

- [工作流程](workflows.md) — 常見模式和各指令的使用時機
- [CLI](cli.md) — 管理和驗證的終端機指令
- [自訂](customization.md) — 建立自訂結構描述和工作流程
