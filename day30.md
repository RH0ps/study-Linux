# Linux学習記録 Day30
# cronによるバックアップ自動化とSlack通知改善
## 実践したこと
- バックアップスクリプト（backup.sh）をcronで自動実行する環境を構築
- 手動実行では成功していた処理を、cron環境でも動作するよう調整
- cron実行時の環境変数問題（.env未読み込み）を修正
- Slack通知の動作確認とエラー原因の調査
- .gitignoreを作成し、秘密情報や生成ファイルをGit管理対象外に設定
- cronログをファイル出力するように変更し、デバッグ可能にした
## backup.sh(現在の内容)
```bash
#!/bin/bash
set -euo pipefail

# =========================
# 設定
# =========================

TARGET_FILE="/home/r.h/docker/index.html"
BACKUP_DIR="/home/r.h/docker/backup"
LOG_FILE="/home/r.h/docker/backup/backup.log"

if [ -f "/home/r.h/docker/.env" ]; then
    source "/home/r.h/docker/.env"
fi

WEBHOOK_URL="${WEBHOOK_URL:?WEBHOOK_URL is required}"

DRY_RUN="${DRY_RUN:-false}"

# 世代管理数
KEEP_BACKUPS=10

# =========================
# ログ関数
# =========================

log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') $1" | tee -a "$LOG_FILE"
}

# =========================
# 通知関数
# =========================

notify() {
    local message="$1"

    if ! curl -s -X POST \
        -H "Content-type: application/json" \
        --data "$(jq -n --arg text "$message" '{text:$text}')" \
        "$WEBHOOK_URL" >/dev/null 2>>"$LOG_FILE"; then
        log "WARN: Slack notification failed"
    fi
}

# =========================
# trap（エラー検知）
# =========================

trap 'log "ERROR: script failed (exit code=$?, line=$LINENO)"; notify "❌ Backup failed"' ERR

# =========================
# DRY RUN
# =========================

if [ "$DRY_RUN" = "true" ]; then
    log "DRY_RUN enabled: no changes will be made"
    exit 0
fi

# =========================
# 準備
# =========================

mkdir -p "$BACKUP_DIR"

DATE=$(date "+%Y%m%d_%H%M%S")
BACKUP_FILE="index_${DATE}.html"

# =========================
# チェック
# =========================

if [ ! -f "$TARGET_FILE" ]; then
    log "ERROR: target file not found: $TARGET_FILE"
    notify "❌ Backup failed: file not found"
    exit 1
fi

if [ ! -s "$TARGET_FILE" ]; then
    log "ERROR: target file is empty"
    notify "❌ Backup failed: empty file"
    exit 1
fi

# =========================
# バックアップ実行
# =========================

cp -a "$TARGET_FILE" "$BACKUP_DIR/$BACKUP_FILE"
log "Backup created: $BACKUP_FILE"

if [ ! -f "$BACKUP_DIR/$BACKUP_FILE" ]; then
    log "ERROR: backup file was not created"
    notify "❌ Backup failed: file creation error"
    exit 1
fi

# =========================
# 世代管理
# =========================

mapfile -t files < <(ls -1t "$BACKUP_DIR"/index_*.html 2>/dev/null)

if [ "${#files[@]}" -gt "$KEEP_BACKUPS" ]; then
    for ((i=KEEP_BACKUPS; i<${#files[@]}; i++)); do
        rm -f "${files[$i]}"
    done
    log "Rotation completed (kept $KEEP_BACKUPS backups)"
else
    log "Rotation skipped (not enough files)"
fi

# =========================
# 成功通知
# =========================

log "Backup success: $BACKUP_FILE"
notify "📦 Backup success: $BACKUP_FILE ($(date '+%Y-%m-%d %H:%M:%S'))"
```
## 実行したこと
### cron設定
crontabを編集して以下を設定

- disk_monitor.sh（ディスク監視）
  - 5分ごとに実行

- backup.sh（バックアップ）
  - 毎日9時に実行

設定確認
```bash
crontab -l
```
### cronからのバックアップ実行（最初の状態）
最初はシンプルに設定
```bash
*/1 * * * * /home/r.h/docker/backup.sh
```
しかしcron環境では環境変数が読み込まれず、Slack通知に失敗。
### 修正後の設定
.envを読み込むように修正
```bash
0 9 * * * bash -c 'source /home/r.h/docker/.env && /home/r.h/docker/backup.sh' >> /home/r.h/docker/cron.log 2>&1
```
### ログ確認
```bash
tail -20 /home/r.h/docker/backup/backup.log
```
確認できた内容
- Backup created
- Rotation completed
- Backup success

バックアップがcronでも正常動作していることを確認。
### Slack通知テスト
```bash
source /home/r.h/docker/.env

curl -X POST \
-H "Content-type: application/json" \
--data '{"text":"test backup notification"}' \
$WEBHOOK_URL
```
Webhook自体は正常に動作することを確認。
## 間違えたこと・修正したこと
### cronで.envが読み込まれなかった
初期設定
```bash
*/1 * * * * /home/r.h/docker/backup.sh
```
- cronでは環境変数が無いためWEBHOOK_URLが取得できずSlack通知失敗

修正
```bash
bash -c 'source /home/r.h/docker/.env && /home/r.h/docker/backup.sh'
```
### jqコマンド不足
Slack通知をjq版に変更した際にエラー発生
```bash
jq: command not found
```
原因：jq未インストール

対応
```bash
sudo apt update
sudo apt install jq
which jq
```
### ログ確認場所の誤り
最初
```bash
cat /var/log/syslog
```
- コンテナ環境では存在しない

対応：cronログを明示的に出力
```bash
>> /home/r.h/docker/cron.log 2>&1
```
## 覚えたコマンド
### cron関連
```bash
crontab -e
crontab -l
```
### 環境変数
```bash
source /home/r.h/docker/.env
```
### ログ確認
```bash
tail -20 ファイル名
```
### コマンド確認
```bash
which jq
```
### Git管理除外
```bash
cat .gitignore
```
## 学んだこと
- cronは通常のシェルと違い、環境変数やPATHが引き継がれない
- .envを使う場合は明示的にsourceする必要がある
- 「動くかどうか」だけでなく、ログ設計が重要
- 外部連携（Slack/Webhook）は依存関係の確認が必須
- 本番運用では「自動化＋監視＋通知」がセットになる
- コンテナ環境ではログ場所やシステム構成がホストと違う場合がある
## まとめ
- cronを使ってバックアップの完全自動化を実装した。
- 手動では問題ない処理でも、cron環境では環境差でエラーが出ることを経験し、
- 運用を意識した設計（ログ・通知・環境変数管理）の重要性を理解した。