# Linux学習記録 Day24
# Gitによる学習記録管理の導入＋GitHub同期トラブル解決
## 今日学んだこと
- 今日は、これまでGitHubサイト上のボタン操作で行っていた学習記録の管理を、Gitコマンドを使ってローカル環境から管理する方法に変更した。

- Gitは「変更履歴を管理するためのツール」であり、GitHubはその履歴をクラウド上で保存・共有するためのサービスであることを理解した。

- これまでGitHub上だけで管理していたため、ローカルとの履歴が一致していない状態になり、Gitの仕組みを理解する重要性を実感した。

## 実践したこと

### Gitリポジトリの作成
- `git init`ローカルフォルダをGit管理対象にした。
### ファイル追加とコミット
- `git add .`
`git commit -m "Day24: GitでLinux学習記録管理開始"`学習記録ファイルをまとめて追加・保存
### GitHubとの接続
- `git remote add origin https://github.com/RH0ps/linux-study`
`git remote -v`既存のリポジトリと接続した

### GitHubとの履歴ズレ問題
- push時にエラーが発生した。
```bash
! [rejected] main -> main (fetch first)
fatal: refusing to merge unrelated histories
```
原因は、GitHub側とローカル側で別の履歴が存在していたこと。

### 解決方法（履歴統合）
- GitHubとローカルの履歴を統合した。
```bash
git pull origin main --allow-unrelated-histories
```
その結果、複数ファイルでコンフリクトが発生した。

### コンフリクト解決
- 競合を解消し再コミットした。
```
git add .
git commit -m "resolve merge conflicts"
```
### GitHubへ反映
最終的にGitHubへ反映した。
```bash
git push -u origin main
```
## 間違えたこと・苦戦したこと
- push時に`fetch first`エラーが出た
- `unrelated histories`の意味が最初わからなかった
- コンフリクトが複数ファイルで同時発生して混乱した
- GitHubとローカルの関係（履歴の概念）を理解するのに時間がかかった

## 覚えたこと

### Git基本コマンド
- `git init`フォルダをGit管理できるようにする
- `git add .`変更したファイルをまとめて登録
- `git commit -m "message"`保存(履歴として確定)
- `git push`GitHubに送る
- `git pull`GitHubから持ってくる

### トラブル対応
- `unrelated histories`別履歴の統合が必要
- `merge conflict`同じファイルの競合
- `git remote -v`接続先確認

## 今日のまとめ
- GitHubはボタンで更新するものではなく、Gitを通して履歴を管理する仕組みであることを実体験で理解した。
- ローカルとGitHubの履歴がズレるとエラーになるため、今後はGitを前提に学習記録を管理していく。