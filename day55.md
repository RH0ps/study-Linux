# Linux学習記録 Day55
## GitHub ActionsにBatsテストを追加しCIを強化
## 実践内容
GitHub ActionsのCIを強化し、自動テストとしてBats（Bash Automated Testing System）を導入。

プロジェクト内に`tests`ディレクトリを作成し、`health_check.bats`を追加。

以下の内容をテスト対象とした。

- health_check.shが存在すること
- health_check.shに実行権限があること
- Bash構文エラーがないこと

GitHub Actionsの`ci.yml`も更新し、ShellCheck・Bash構文チェック・Docker Buildに加えてBatsを自動実行するように変更。

CIの処理内容は以下の順番となった。

- Bash Syntax Check
- ShellCheck
- Run Bats Tests
- Docker Build

ローカル環境にもBatsを導入するため、Homebrewを利用して`bats-core`をインストールした。

インストール後、以下のコマンドで動作確認を実施した。

- `bats --version`
- `bats tests`

実行結果では3件すべてのテストが成功し、正常に動作することを確認。

その後GitHubへPushし、GitHub Actionsが自動実行されることを確認。

ActionsではすべてのジョブがSuccessとなり、Batsを含めたCIが正常に動作することを確認。

## 間違えたこと

- 最初はBatsがGitHub Actionsだけで利用できるものだと思っていたが、ローカルで実行するためにはMacにも`bats-core`をインストールする必要があった。
- GitHub ActionsへPushする前にローカルでテストを実行できる環境を整える重要性を学んだ。

## 学んだこと

- Batsを利用することで、Bashスクリプトを自動テストできる。
- GitHub Actionsへ組み込むことで、Pushするたびに品質チェックを自動で実施できる。
- ローカルでテストを通してからPushすることで、GitHub Actionsの失敗を減らし、開発効率を向上できる。
- CIはLintだけではなく、自動テストとDocker Buildまで含めて構成することで、より実践的な品質管理が行える。

## 覚えたこと・コマンド

```bash
# Batsインストール
brew install bats-core

# バージョン確認
bats --version

# テスト実行
bats tests
```
