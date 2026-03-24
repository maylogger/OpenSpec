# 工作流程

本指南涵蓋 OpenSpec 的常見工作流程模式及各指令的使用時機。基本設定請參閱 [入門指南](getting-started.md)，指令參考請參閱 [指令](commands.md)。

## 理念：行動，而非階段

傳統工作流程強迫你走過各個階段：計劃、然後實作、然後完成。但真實工作無法整齊地套入框框。

OPSX 採用不同方法：

```text
傳統（階段鎖定）：

  計劃 ────────► 實作 ────────► 完成
      │                │
      │   「無法回頭」  │
      └────────────────┘

OPSX（流暢行動）：

  proposal ──► specs ──► design ──► tasks ──► implement
```

**核心原則：**

- **行動，而非階段** — 指令是你可以做的事情，而非你被困住的階段
- **依賴關係是啟用者** — 它們顯示可能的事情，而非必須的下一步

> **自訂：** OPSX 工作流程由定義產出物序列的結構描述驅動。詳情請參閱 [自訂](customization.md)。

## 兩種模式

### 預設快速路徑（`core` 設定檔）

新安裝預設使用 `core`，提供：
- `/opsx:propose`
- `/opsx:explore`
- `/opsx:apply`
- `/opsx:archive`

典型流程：

```text
/opsx:propose ──► /opsx:apply ──► /opsx:archive
```

### 擴充/完整工作流程（自訂選擇）

如果你想要明確的搭建-建構指令（`/opsx:new`、`/opsx:continue`、`/opsx:ff`、`/opsx:verify`、`/opsx:sync`、`/opsx:bulk-archive`、`/opsx:onboard`），可用以下方式啟用：

```bash
openspec config profile
openspec update
```

## 工作流程模式（擴充模式）

### 快速功能

當你知道要建構什麼，只需要執行時：

```text
/opsx:new ──► /opsx:ff ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

**對話範例：**

```text
你：/opsx:new add-logout-button

AI：已建立 openspec/changes/add-logout-button/
     準備建立：proposal

你：/opsx:ff

AI：正在建立所有規劃產出物...
     ✓ proposal.md
     ✓ specs/
     ✓ design.md
     ✓ tasks.md
     準備好進行實作！

你：/opsx:apply

AI：正在處理任務...
     ✓ 已在 header 加入登出按鈕
     ✓ 已實作 session 清除
     ✓ 已加入確認對話框
     所有任務完成！

你：/opsx:archive

AI：✓ 已合併規格
     ✓ 已封存變更
```

**最適合：** 小至中型功能、bug 修復、直接的變更。

### 探索性

當需求不明確或需要先調查時：

```text
/opsx:explore ──► /opsx:new ──► /opsx:continue ──► ... ──► /opsx:apply
```

**對話範例：**

```text
你：/opsx:explore

AI：你想探索什麼？

你：我想改善頁面載入效能，但不確定瓶頸在哪裡。

AI：讓我幫忙調查...
     [分析套件大小、識別慢速查詢、審查元件渲染模式]

     我發現了三個主要瓶頸：
     1. 未優化的大圖片
     2. ProductList 中的同步資料抓取
     3. Context 變更導致的重新渲染

     你想先處理哪個？

你：我們來解決資料抓取問題。

你：/opsx:new optimize-product-list-fetching

AI：已建立變更。準備建立：proposal

你：/opsx:continue

AI：根據我們的探索建立 proposal...
     [撰寫包含調查洞察的提案]

     準備建立：specs、design
     你想要先建立哪個？

你：/opsx:continue

AI：正在建立 specs...
```

**最適合：** 效能優化、偵錯、架構決策、不明確的需求。

### 並行變更

同時進行多個變更：

```text
變更 A：/opsx:new ──► /opsx:ff ──► /opsx:apply（進行中）
                                       │
                                  切換上下文
                                       │
變更 B：/opsx:new ──► /opsx:ff ──────► /opsx:apply
```

**對話範例：**

```text
你：我正在進行 add-dark-mode，但被拉去修 bug

你：/opsx:new fix-login-redirect

AI：已建立 openspec/changes/fix-login-redirect/

你：/opsx:ff

AI：✓ 已建立規劃產出物

你：/opsx:apply

AI：正在處理 fix-login-redirect...
     [實作修復]
     所有任務完成！

你：/opsx:archive

AI：✓ 已封存 fix-login-redirect

你：回到深色模式吧

你：/opsx:apply add-dark-mode

AI：繼續 add-dark-mode...
     從任務 2.3：更新 Header 繼續...
```

**最適合：** 並行工作流、緊急中斷、團隊協作。

當你有多個已完成的變更時，使用 `/opsx:bulk-archive`：

```text
你：/opsx:bulk-archive

AI：找到 3 個已完成的變更：
     - add-dark-mode（所有任務完成）
     - fix-login-redirect（所有任務完成）
     - update-footer（所有任務完成）

     正在檢查規格衝突...
     ⚠ add-dark-mode 和 update-footer 都觸及 specs/ui/

     正在檢查程式碼庫以解決...
     兩個變更都已實作。將按時間順序套用。

     封存全部 3 個變更？

你：是

AI：✓ 已封存 add-dark-mode
     ✓ 已封存 fix-login-redirect
     ✓ 已封存 update-footer
     規格依順序合併：add-dark-mode → update-footer
```

批量封存會偵測多個變更觸及相同規格的情況，並透過檢查實際實作內容來解決衝突。

### 完成變更

建議的完成流程：

```text
/opsx:apply ──► /opsx:verify ──► /opsx:archive
                    │                 │
              驗證           提示同步
              實作           若需要
```

#### 驗證：檢查你的工作

`/opsx:verify` 從三個維度驗證實作是否符合你的產出物：

```text
你：/opsx:verify

AI：正在驗證 add-auth...

     完整性
     ✓ tasks.md 中的所有 12 個任務都已勾選
     ✓ 規格中的所有需求都有對應的程式碼
     ⚠ 情境「閒置後 session 逾時」未經測試

     正確性
     ✓ 實作符合規格意圖
     ✓ 情境中的邊緣情況已處理
     ✓ 錯誤狀態符合規格定義

     一致性
     ✓ 設計決策反映在程式碼結構中
     ✓ 命名慣例與 design.md 一致
     ⚠ 設計提到「事件驅動」但實作使用輪詢

     摘要
     ─────────────────────────────
     嚴重問題：0
     警告：2
     準備封存：是（有警告）

     建議：
     1. 為 session 逾時情境加入測試
     2. 考慮重構為如設計所述的事件驅動，或更新 design.md
```

**驗證檢查的內容：**

| 維度 | 驗證的內容 |
|------|-----------|
| 完整性 | 所有任務完成、所有需求已實作、情境已涵蓋 |
| 正確性 | 實作符合規格意圖、邊緣情況已處理 |
| 一致性 | 設計決策反映在程式碼中、模式一致 |

驗證不會阻止封存，但會呈現你可能想先處理的問題。

#### 封存：完成變更

`/opsx:archive` 完成變更並將其移至封存：

```text
你：/opsx:archive

AI：正在封存 add-auth...

     產出物狀態：
     ✓ proposal.md 存在
     ✓ specs/ 存在
     ✓ design.md 存在
     ✓ tasks.md 存在（12/12 任務完成）

     差異規格：尚未同步至主要規格
     → 現在同步？

你：是

AI：✓ 已將規格同步至 openspec/specs/auth/spec.md
     ✓ 已移至 openspec/changes/archive/2025-01-24-add-auth/

     變更封存成功。
```

若規格未同步，封存會提示。不會因任務未完成而阻止封存，但會發出警告。

## 何時使用什麼

### `/opsx:ff` vs `/opsx:continue`

| 情況 | 使用 |
|------|------|
| 需求明確，準備好建構 | `/opsx:ff` |
| 正在探索，想逐步審查 | `/opsx:continue` |
| 想在建立規格前迭代提案 | `/opsx:continue` |
| 時間緊迫，需要快速推進 | `/opsx:ff` |
| 複雜變更，需要控制 | `/opsx:continue` |

**經驗法則：** 如果你能預先描述完整範圍，使用 `/opsx:ff`。如果你是邊做邊摸索，使用 `/opsx:continue`。

### 何時更新 vs 重新開始

一個常見問題：何時更新現有變更是合適的，何時應該開始新的變更？

**在以下情況更新現有變更：**

- 相同意圖，精煉執行方式
- 範圍縮小（先推出 MVP，後來再做其餘部分）
- 學習驅動的修正（程式碼庫不如預期）
- 基於實作發現的設計調整

**在以下情況開始新的變更：**

- 意圖根本性地改變了
- 範圍爆炸為完全不同的工作
- 原始變更可以單獨標記為「完成」
- 修補會比澄清更加混亂

```text
                     ┌─────────────────────────────────────┐
                     │     這是相同的工作嗎？               │
                     └──────────────┬──────────────────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
          相同意圖？        >50% 重疊？         沒有這些變更
          相同問題？        相同範圍？          原始可以「完成」嗎？
                 │                  │                  │
       ┌────────┴────────┐  ┌──────┴──────┐   ┌───────┴───────┐
       │                 │  │             │   │               │
      是               否  是            否  否              是
       │                 │  │             │   │               │
       ▼                 ▼  ▼             ▼   ▼               ▼
     更新             新增  更新         新增  更新           新增
```

**範例：「加入深色模式」**

- 「需要同時支援自訂主題」→ 新的變更（範圍爆炸）
- 「系統偏好偵測比預期困難」→ 更新（相同意圖）
- 「先推出切換器，之後再加入偏好設定」→ 更新後封存，然後新的變更

## 最佳實踐

### 保持變更聚焦

每個變更一個邏輯工作單元。如果你在「加入功能 X 同時重構 Y」，考慮兩個獨立的變更。

**為何重要：**
- 更易於審查和理解
- 更清晰的封存歷史
- 可以獨立交付
- 若需要回滾，更簡單

### 對不明確的需求使用 `/opsx:explore`

在承諾進行變更之前，先探索問題空間：

```text
你：/opsx:explore

AI：你想探索什麼？

你：我們應如何處理 API 的速率限制？

AI：讓我調查你目前的設定和選項...
     [研究程式碼庫，考慮模式]

     我看到我們可以採取三種方法：
     1. 基於中介軟體（簡單，按端點）
     2. Redis 支援（可擴展，共享狀態）
     3. 每使用者 token 桶（公平，複雜）

     你目前的技術堆疊建議 #1 或 #2。你的規模是多少？
```

探索在你建立產出物之前澄清思路。

### 封存前驗證

使用 `/opsx:verify` 確認實作符合產出物：

```text
你：/opsx:verify

AI：正在驗證 add-dark-mode...

     ✓ tasks.md 中的所有任務都已勾選
     ✓ 規格中的需求都有對應的程式碼
     ✓ 設計決策反映在實作中

     準備封存！
```

在關閉變更之前捕捉不匹配。

### 清楚命名變更

好的名稱讓 `openspec list` 更有用：

```text
好的命名：                     避免：
add-dark-mode                  feature-1
fix-login-redirect             update
optimize-product-query         changes
implement-2fa                  wip
```

## 指令快速參考

完整指令詳情和選項請參閱 [指令](commands.md)。

| 指令 | 用途 | 使用時機 |
|------|------|---------|
| `/opsx:propose` | 建立變更 + 規劃產出物 | 快速預設路徑（`core` 設定檔） |
| `/opsx:explore` | 思考想法 | 需求不明確、調查 |
| `/opsx:new` | 開始變更架構 | 擴充模式，明確的產出物控制 |
| `/opsx:continue` | 建立下一個產出物 | 擴充模式，逐步建立產出物 |
| `/opsx:ff` | 建立所有規劃產出物 | 擴充模式，範圍明確 |
| `/opsx:apply` | 實作任務 | 準備好撰寫程式碼 |
| `/opsx:verify` | 驗證實作 | 擴充模式，封存前 |
| `/opsx:sync` | 合併差異規格 | 擴充模式，選用 |
| `/opsx:archive` | 完成變更 | 所有工作完成 |
| `/opsx:bulk-archive` | 封存多個變更 | 擴充模式，並行工作 |

## 下一步

- [指令](commands.md) — 附選項的完整指令參考
- [概念](concepts.md) — 深入了解規格、產出物和結構描述
- [自訂](customization.md) — 建立自訂工作流程
