# 多語言指南

設定 OpenSpec 以英語以外的語言生成產出物。

## 快速設定

在 `openspec/config.yaml` 中加入語言指示：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.

  # 你的其他專案背景資訊...
  Tech stack: TypeScript, React, Node.js
```

完成。所有生成的產出物現在都會以葡萄牙語撰寫。

## 語言範例

### 葡萄牙語（巴西）

```yaml
context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.
```

### 西班牙語

```yaml
context: |
  Idioma: Español
  Todos los artefactos deben escribirse en español.
```

### 中文（簡體）

```yaml
context: |
  语言：中文（简体）
  所有产出物必须用简体中文撰写。
```

### 日語

```yaml
context: |
  言語：日本語
  すべての成果物は日本語で作成してください。
```

### 法語

```yaml
context: |
  Langue : Français
  Tous les artefacts doivent être rédigés en français.
```

### 德語

```yaml
context: |
  Sprache: Deutsch
  Alle Artefakte müssen auf Deutsch verfasst werden.
```

## 使用技巧

### 處理技術術語

決定如何處理技術術語：

```yaml
context: |
  Language: Japanese
  Write in Japanese, but:
  - Keep technical terms like "API", "REST", "GraphQL" in English
  - Code examples and file paths remain in English
```

### 結合其他背景資訊

語言設定可與其他專案背景資訊一同使用：

```yaml
schema: spec-driven

context: |
  Language: Portuguese (pt-BR)
  All artifacts must be written in Brazilian Portuguese.

  Tech stack: TypeScript, React 18, Node.js 20
  Database: PostgreSQL with Prisma ORM
```

## 驗證

確認語言設定是否生效：

```bash
# 查看指示內容 — 應包含你的語言背景設定
openspec instructions proposal --change my-change

# 輸出內容將包含你的語言背景設定
```

## 相關文件

- [自訂指南](./customization.md) — 專案設定選項
- [工作流程指南](./workflows.md) — 完整工作流程說明
