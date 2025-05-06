---
title: "TypeScript × Vite × GitHub Pages 超入門"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript","vite","githubpages","githubactions","frontend"]
published: true
---

# TypeScript × Vite × GitHub Pages 超入門

## 目的
* 就活で色んな求人でTypeScriptを良く見るので試せる環境作りたい
* 公開までするならGitHub Pagesが良さそうなので試したい
* Viteというのが良いらしいので試したい
* ついでにGitHub Actionsも試したい

## 進め方
* ChatGPT o3で手順作ってもらう→うまくいかなかったら聞くの繰り返し

---

## 環境

| ツール           | 推奨バージョン   | 確認コマンド              |
| ------------- | --------- | ------------------- |
| Node.js + npm | 18 LTS 以上 | `node -v && npm -v` |
| Git           | 2.x       | `git --version`     |
| Git Bash (Windows) | Git for Windows 同梱 | – |
| VS Code (任意)  | 最新安定版     | –                   |
| GitHub アカウント  | –         | ブラウザでログイン           |

:::message
前回の記事でNode.jsとnpm入れてたはずなのにコマンドないと言われると思ったら、.bashrcが読み込まれてなかった…。.bash_profileで.bashrcを読み込むようにした。
:::
---

## Step 1 :TypeScriptで最小サイトをGitHub Pagesで公開

### 1‑1. プロジェクト初期化

```bash
mkdir pages && cd $_
git init
npm init -y
npm install --save-dev typescript
npx tsc --init           # tsconfig.json
```

*tsconfig.json* 最小編集:

```json
{
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./docs",
    "target": "es2016" # デフォルトのまま
  }
}
```

### 1‑2. フォルダ構成

```
pages/
├─ src/           # TypeScript
│  └─ index.ts
├─ docs/          # ★GitHub Pages 公開フォルダ
│  └─ index.html
├─ .gitignore     # node_modules/ を必ず追加
├─ package.json
└─ tsconfig.json
```

`src/index.ts`

```ts
const msg: string = "Hello, TypeScript World!";
document.getElementById("app")!.textContent = msg;
```

`docs/index.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head><meta charset="UTF-8" /><title>My TS Page</title></head>
<body>
  <h1 id="app">Loading...</h1>
  <script type="module" src="index.js"></script>
</body>
</html>
```

### 1‑3. ビルド & ローカル確認

```bash
npm run build   # → docs/index.js が生成
npm run start   # serve -s docs
```

### 1‑4. GitHub Pages へ公開

```bash
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/cdaket/pages.git
git push -u origin main
```
* GitHub → pagesリポジトリ作成　※リポジトリの名前は何でもOK
* GitHub → **Settings › Pages** : Branch `main` / Folder `/docs` を指定。
* 公開 URL: `https://cdaket.github.io/pages/`
→見れればここまでOK！

---

## Step 2 : Vite へ移行

### 2‑1. 依存追加 & 旧ビルド物の掃除

```bash
# docs/ は後で不要になるので削除
git rm -r --cached docs
rm -rf docs
npm i -D vite
```

### 2‑2. `vite.config.ts`

```ts
import { defineConfig } from "vite";
export default defineConfig({
  base: "/<repo>/",   // 必ず先頭と末尾にスラッシュ
  build: {
    outDir: "dist"      // Vite デフォルト dist/
  }
});
```

### 2‑3. ルート `index.html`（新規）

```html
<!DOCTYPE html>
<html lang="ja">
<head><meta charset="UTF-8" /><title>My Vite TS Page</title></head>
<body>
  <h1 id="app">Loading…</h1>
  <script type="module" src="/src/index.ts"></script>
</body>
</html>
```

### 2‑4. npm スクリプト `package.json`

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

### 2‑5. 起動確認

```bash
npm run dev      # http://localhost:5173
npm run build    # dist/ に本番ファイル
npm run preview  # http://localhost:4173
```
→見れればここまでOK！

---

## Step 3 : GitHub Actions で自動ビルド & デプロイ

1. GitHub → **Settings › Pages** – *Source* を **GitHub Actions** に切替
2. `.github/workflows/deploy.yml` を追加
```bash
mkdir -p .github/workflows
vim .github/workflows/deploy.yml
```
※ディレクトリ作らずにvimで開いて保存できず一敗
```yaml
name: Deploy Vite site to GitHub Pages

on:
  push:
    branches: [main]     # メインブランチに push したら実行
  workflow_dispatch:      # 手動実行も可

permissions:
  contents: read          # ソース取得
  pages: write            # Pages へ書き込み
  id-token: write         # OIDC で認証

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 18
          cache: "npm"
      - run: npm ci
      - run: npm run build   # → dist/ が生成される
      - name: Upload pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deploy.outputs.page_url }}
    steps:
      - id: deploy
        uses: actions/deploy-pages@v4
```

3. mainブランチに push すると **Actions → build → deploy** が緑になり、Pages が自動更新
- 失敗すると赤になる。buildをクリックするとエラー内容が見れる。

---

## まとめ
* サクッとできて楽しかった
* TypeScriptの実装フローの雰囲気がわかって良かった

## TODOメモ
* 設定周りよくわからずやったので調べる
  * もっと良いフローがある気がする
* GitHub Actionsもよくわからずやったので調べる
* VSCodeで自動コンパイル？しつつ実装できるようにしたい
* TypeScriptで何か簡単なもの作ってpagesで公開したい
