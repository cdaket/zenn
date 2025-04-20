---
title: "ZENNをGitHub連携で始めてみる"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn","zenncli","github","git","vscode"]
published: true
---

ZENNをGitHub連携で始めるためにやったことまとめ。
※2025/04/20時点

# ZENNアカウントを作成する
* googleアカウント連携で作成
# GitHubを連携する
* 公式の手順参照
https://zenn.dev/zenn/articles/connect-to-github
  * メモ:一時的にサーバ落ちてたのか連携まで時間かかった(10分くらい)
  * `zenn` という名前でリポジトリを作成した
# Zenn CLIをインストールする
* 公式の手順参照
https://zenn.dev/zenn/articles/install-zenn-cli
## windowsのgit bashでNode.js入れるところからやる
* 公式サイトの手順通りに進めるが引っかかったところがあるのでメモしておく
https://nodejs.org/ja/download
1. fnmをダウンロードしてインストールする
```
$ winget install Schniz.fnm
```
* git bash再起動を促されるので再起動する

2. Node.jsをダウンロードしてインストールする
```
$ fnm install 22
```
3. Node.jsのバージョンを確認する
```
$ node -v # "v22.14.0"が表示される。
```
* nodeコマンド見つからないと言われるのでパスを通す
```
$ fnm env
```
* exportなんちゃらと書かれた行が出てくるので、.bashrcにコピペする
```
$ vim ~/.bashrc
```
* 保存したら読み込み直す
```
$ source ~/.bashrc
```
* 再度バージョン確認してみる
```
$ node -v # "v22.14.0"が表示される。
$ npm -v # "10.9.2"が表示される。
```
* →表示できたのでOK
## ディレクトリ作成のところのメモ
1. GitHubに作ったリポジトリをcloneしてくる
```
$ git clone https://github.com/cdaket/zenn.git
```
2. ディレクトリに移動
```
$ cd zenn/
```
## あとは公式の手順通りにやればOK
# Zenn CLIで記事を作成する
* 公式の手順参照
https://zenn.dev/zenn/articles/zenn-cli-guide
## 一旦GitHubにpushしてみる
1. 下記コマンド実行
```
$ git add .
$ git commit -m "first commit"
```
※誰？って聞かれたら設定する必要あり
```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```
```
$ git push origin main
```
  * ブラウザ認証が出るのでやる
  * 普段の業務ではSSHでやってたのに、今回何故かHTTPSでcloneしたから思ってた手順と違った(ブラウザ認証なんてできるの知らなかった)
2. GitHub上で確認できればOK
https://github.com/cdaket/zenn/commit/9c91366ba9ac343535e464e27a02c4c1efb71881
## 記事を書く
1. VSCodeをインストールする
  * vimでmarkdown書くのしんどかったので入れる
  * 公式サイトからダウンロード
  https://azure.microsoft.com/ja-jp/products/visual-studio-code
2. VSCodeでzennディレクトリを開く
3. 記事のファイルを開く
4. Ctrl+K V でプレビューを見ながら書ける
5. 記事のファイル名を変えたくなったので変える
```
$ mv articles/3e7f5aad75d8ed.md articles/zenn_github_link_start.md
```
6. プレビュー見ながらやっていく
```
$ npx zenn preview
```
  * http://localhost:8000 で記事のプレビューが見れる。
  * ※手動保存忘れずに。AutoSave有効化してもいいかも。
# 記事を公開する
1. title決める
2. emojiはよくわからないのでそのまま
3. topics設定する
  * 5個までらしい
  * 公式サイトで検索して良い感じのを設定する
4. publishedをtrueにする
5. 下記コマンドを実行する
```
$ git add .
$ git commit -m "ZENNをGitHub連携で始めてみる 公開"
$ git push origin main
```
# TODOメモ
* SSHで認証するように変更したい
* Markdownの書き方を学んで良い感じにしたい
* 日々のメモで公開できることは記事にしていきたい
* mainに直接pushかPR運用どっちが良いか考える
* 校正してくれる仕組みいれたい