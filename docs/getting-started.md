# 入門指南

本指南說明安裝與初始化完成後，OpenSpec 的使用方式。安裝說明請參閱 [主要 README](../README.md#quick-start)。

## 運作原理

OpenSpec 幫助你和 AI 程式碼助理在撰寫任何程式碼之前，就對要建構的內容達成共識。

**預設快速路徑（core 設定檔）：**

```text
/opsx:propose ──► /opsx:apply ──► /opsx:archive
```

**完整路徑（自訂工作流程選擇）：**

```text
/opsx:new ──► /opsx:ff 或 /opsx:continue ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

預設全域設定檔為 `core`，包含 `propose`、`explore`、`apply` 和 `archive`。可透過 `openspec config profile` 再執行 `openspec update` 來啟用擴充工作流程指令。

## OpenSpec 建立的內容

執行 `openspec init` 後，你的專案會有以下結構：

```
openspec/
├── specs/              # 真相來源（系統的行為）
│   └── <domain>/
│       └── spec.md
├── changes/            # 提議的更新（每個變更一個資料夾）
│   └── <change-name>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/      # 差異規格（變更的內容）
│           └── <domain>/
│               └── spec.md
└── config.yaml         # 專案設定（選用）
```

**兩個關鍵目錄：**

- **`specs/`** — 真相來源。這些規格描述系統目前的行為方式，按領域組織（例如 `specs/auth/`、`specs/payments/`）。

- **`changes/`** — 提議的修改。每個變更有自己的資料夾，包含所有相關產出物。變更完成後，其規格會合併至主要 `specs/` 目錄。

## 了解產出物

每個變更資料夾包含引導工作的產出物：

| 產出物 | 用途 |
|--------|------|
| `proposal.md` | 「為何」與「做什麼」— 記錄意圖、範圍和方法 |
| `specs/` | 顯示 ADDED/MODIFIED/REMOVED 需求的差異規格 |
| `design.md` | 「如何做」— 技術方法與架構決策 |
| `tasks.md` | 附核取方塊的實作清單 |

**產出物相互建立：**

```
proposal ──► specs ──► design ──► tasks ──► implement
   ▲           ▲          ▲                    │
   └───────────┴──────────┴────────────────────┘
            隨學習持續更新
```

在實作過程中學習到更多資訊後，可以隨時回頭修改早期的產出物。

## 差異規格的運作方式

差異規格是 OpenSpec 的核心概念。它們顯示相對於當前規格的變更內容。

### 格式

差異規格使用章節來表示變更類型：

```markdown
# Auth 的差異

## ADDED 需求

### 需求：雙因素驗證
系統「必須」在登入時要求第二個驗證因素。

#### 情境：需要 OTP
- GIVEN 已啟用 2FA 的使用者
- WHEN 使用者提交有效憑證
- THEN 顯示 OTP 挑戰

## MODIFIED 需求

### 需求：Session 逾時
系統「應」在閒置 30 分鐘後使 session 失效。
（原先為：60 分鐘）

#### 情境：閒置逾時
- GIVEN 已驗證的 session
- WHEN 30 分鐘無操作
- THEN session 被撤銷

## REMOVED 需求

### 需求：記住我
（已廢棄，改以 2FA 取代）
```

### 封存時發生的事

封存變更時：

1. **ADDED** 需求附加至主要規格
2. **MODIFIED** 需求取代現有版本
3. **REMOVED** 需求從主要規格中刪除

變更資料夾移至 `openspec/changes/archive/` 以保留稽核記錄。

## 範例：你的第一個變更

讓我們實際操作，為應用程式加入深色模式。

### 1. 開始變更（預設）

```text
你：/opsx:propose add-dark-mode

AI：已建立 openspec/changes/add-dark-mode/
     ✓ proposal.md — 說明原因與變更內容
     ✓ specs/       — 需求與情境
     ✓ design.md    — 技術方法
     ✓ tasks.md     — 實作清單
     準備好進行實作！
```

如果你已啟用擴充工作流程設定檔，也可以分兩步完成：先 `/opsx:new`，再 `/opsx:ff`（或逐步執行 `/opsx:continue`）。

### 2. 建立的內容

**proposal.md** — 記錄意圖：

```markdown
# 提案：加入深色模式

## 意圖
使用者要求提供深色模式選項，以降低夜間使用時的眼睛疲勞。

## 範圍
- 在設定中加入主題切換
- 支援系統偏好偵測
- 將偏好設定持久化至 localStorage

## 方法
使用 CSS 自訂屬性進行主題設定，搭配 React Context 進行狀態管理。
```

**specs/ui/spec.md** — 顯示新需求的差異：

```markdown
# UI 的差異

## ADDED 需求

### 需求：主題選擇
系統「應」允許使用者選擇淺色或深色主題。

#### 情境：手動切換
- GIVEN 任何頁面上的使用者
- WHEN 使用者點擊主題切換按鈕
- THEN 主題立即切換
- AND 偏好設定跨 session 持久保存

#### 情境：系統偏好
- GIVEN 沒有已儲存偏好的使用者
- WHEN 應用程式載入
- THEN 使用系統偏好的色彩配置
```

**tasks.md** — 實作清單：

```markdown
# 任務

## 1. 主題基礎架構
- [ ] 1.1 建立具有淺色/深色狀態的 ThemeContext
- [ ] 1.2 加入顏色的 CSS 自訂屬性
- [ ] 1.3 實作 localStorage 持久化

## 2. UI 元件
- [ ] 2.1 建立 ThemeToggle 元件
- [ ] 2.2 在設定頁面加入切換器
- [ ] 2.3 更新 Header 以包含快速切換

## 3. 樣式
- [ ] 3.1 定義深色主題色彩調色盤
- [ ] 3.2 更新元件使用 CSS 變數
```

### 3. 實作

```
你：/opsx:apply

AI：正在處理任務...
     ✓ 1.1 已建立具有淺色/深色狀態的 ThemeContext
     ✓ 1.2 已將 CSS 自訂屬性加入 globals.css
     ✓ 1.3 已實作 localStorage 持久化
     ✓ 2.1 已建立 ThemeToggle 元件
     ...
     所有任務完成！
```

實作過程中若發現設計需要調整，只需更新產出物後繼續即可。

### 4. 封存

```
你：/opsx:archive

AI：正在封存 add-dark-mode...
     ✓ 已將規格合併至 openspec/specs/ui/spec.md
     ✓ 已移至 openspec/changes/archive/2025-01-24-add-dark-mode/
     完成！準備好進行下一個功能。
```

你的差異規格現在已成為主要規格的一部分，記錄了系統的運作方式。

## 驗證與審查

使用 CLI 查看變更狀態：

```bash
# 列出活躍中的變更
openspec list

# 查看變更詳情
openspec show add-dark-mode

# 驗證規格格式
openspec validate add-dark-mode

# 互動式儀表板
openspec view
```

## 下一步

- [工作流程](workflows.md) — 常見模式與各指令的使用時機
- [指令](commands.md) — 所有斜線指令的完整參考
- [概念](concepts.md) — 深入了解規格、變更與結構描述
- [自訂](customization.md) — 依你的方式使用 OpenSpec
