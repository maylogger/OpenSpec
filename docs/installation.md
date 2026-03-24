# 安裝

## 前置需求

- **Node.js 20.19.0 或更高版本** — 確認你的版本：`node --version`

## 套件管理工具

### npm

```bash
npm install -g @fission-ai/openspec@latest
```

### pnpm

```bash
pnpm add -g @fission-ai/openspec@latest
```

### yarn

```bash
yarn global add @fission-ai/openspec@latest
```

### bun

```bash
bun add -g @fission-ai/openspec@latest
```

## Nix

不需安裝，直接執行 OpenSpec：

```bash
nix run github:Fission-AI/OpenSpec -- init
```

或安裝至你的設定檔：

```bash
nix profile install github:Fission-AI/OpenSpec
```

或在 `flake.nix` 中加入開發環境：

```nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    openspec.url = "github:Fission-AI/OpenSpec";
  };

  outputs = { nixpkgs, openspec, ... }: {
    devShells.x86_64-linux.default = nixpkgs.legacyPackages.x86_64-linux.mkShell {
      buildInputs = [ openspec.packages.x86_64-linux.default ];
    };
  };
}
```

## 驗證安裝

```bash
openspec --version
```

## 下一步

安裝完成後，在你的專案中初始化 OpenSpec：

```bash
cd your-project
openspec init
```

詳細操作流程請參閱 [入門指南](getting-started.md)。
