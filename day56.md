# Linux学習記録 Day56
## backup.sh・restore.shの改善とエラー処理の修正
## 実践内容
- backup.sh の Slack 通知処理を確認し、通知されない原因の切り分けを行った。
- `.env` から `WEBHOOK_URL` が正しく読み込まれているか確認し、通知有効化の条件を確認した。
- `jq` を使った JSON 生成処理や `curl` によるWebhook送信処理を確認し、通知処理の問題点を調査した。

確認した内容

```bash
grep WEBHOOK_URL .env

jq --version

tail -30 logs/backup.log
```
- restore.sh のエラー処理を改善した。
- 失敗時に ERR トラップで処理をまとめ、失敗回数の更新、メトリクス更新、ログ出力、通知処理を行うよう修正した。
- エラー終了時の終了コードを正しく取得できるよう修正した。

修正前

```bash
[2026-07-24 16:16:51] [ERROR] No backup found
ERR trap called
[2026-07-24 16:16:51] [ERROR] Restore failed (exit code=0)
```
修正後

```bash
[2026-07-24 16:21:02] [ERROR] No backup found
ERR trap called
[2026-07-24 16:21:02] [ERROR] Restore failed (exit code=1)
```

動作確認を行い、`shellcheck` と `bash -n` で問題がないことを確認。

## 間違えたこと

- ERR トラップ内で `$?` を取得するタイミングを間違え、実際には失敗しているのに `exit code=0` と表示される問題が発生した。
- エラー処理で `exit` と `false` の動作の違いを理解できておらず、ERRトラップの発火条件を確認する必要があった。

## 学んだこと

- Bash の ERR トラップでは、最初に `$?` を保存しないと後続の処理で終了コードが変化することがある。
- エラー処理では「失敗した事実」だけではなく、正しい終了コードを保持することが重要。
- 通知処理は、Webhook・環境変数・依存コマンド（jqなど）を順番に確認すると原因を切り分けやすい。


## 覚えたこと・コマンド

```bash
# 環境変数確認
grep WEBHOOK_URL .env

source .env
echo "$WEBHOOK_URL"

# 依存コマンド確認
jq --version

# ログ確認
tail -30 logs/backup.log

# ShellCheck
shellcheck -x restore.sh

# Bash構文チェック
bash -n restore.sh

# Webhook送信テスト
curl -X POST \
-H "Content-Type: application/json" \
--data '{"text":"Slack test"}' \
"$WEBHOOK_URL"
```
