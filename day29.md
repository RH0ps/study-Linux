# Linux学習記録 Day29
# ディスク使用量監視システムの改善（Slack通知・復旧通知実装）
## 実践したこと
- 既存のディスク監視スクリプト（disk_monitor.sh）を改善
- ディスク使用率が一定以上になった時にSlackへ警告通知する仕組みを確認
- 同じ警告が何度も送信されないように状態管理を実装
- 警告状態から復旧した時にSlackへ復旧通知を送る機能を追加
- cronを設定し、定期的に監視スクリプトを実行する環境を構築
- ログファイル（monitor.log）へ監視結果・通知結果を保存
- 手動実行で警告通知・復旧通知の動作テストを実施
## 実行した操作
```bash
# 監視スクリプト確認
cat ~/docker/disk_monitor.sh

# ログ確認
cat ~/docker/monitor.log

# 一度通知状態をリセット
rm /tmp/disk_alert

# 閾値を一時変更してテスト
bash ~/docker/disk_monitor.sh 1

# 復旧通知テスト
bash ~/docker/disk_monitor.sh 99

# cron設定確認
crontab -l

# cron編集
crontab -e

# cron起動確認
service cron status

# cron開始
sudo service cron start
```
## 実装した内容
- 閾値を引数で変更できるように変更
```bash
THRESHOLD=${1:-80}
```
- 使用率取得対象をコンテナ内のユーザー領域に変更
```bash
USAGE=$(df -h "$HOME/docker" | awk 'NR==2 {print $5}' | tr -d '%')
```
- ログ保存先をvolume同期しているdockerフォルダへ変更
```bash
LOG_FILE="$HOME/docker/monitor.log"
```
- 警告通知時にログへ記録
```bash
echo "Slack alert sent" >> "$LOG_FILE"
```
- 復旧時にstateファイルを削除して通知
```bash
rm -f "$STATE_FILE"

PAYLOAD="{\"text\":\"✅ ディスク復旧: ${USAGE}% ($HOME/docker)\"}"
```
## 間違えたこと
- ホスト側のパスとコンテナ側のパスを混同した
- ~/study/dockerと~/dockerの違いを理解できておらずファイル場所を間違えた
- disk_monitor.shを実行する時に現在いる場所を確認せずcommand not foundになった
- LOG_FILE="/data/monitor.log"のままにしており、現在のvolume構成と合っていなかった
- curlのログが大量に残り、原因確認に時間がかかった
- /tmp/disk_alertが残っていたため、通知テスト時に警告処理が実行されないことがあった
- if文をターミナルへ直接入力してしまい、処理待ち状態になった

## 学んだこと
- 監視システムでは「通知する」だけではなく「通知済み状態を管理する」ことが重要
- stateファイルを利用することで同じ警告を連続送信しない仕組みを作れる
- 復旧通知を追加することで、障害発生だけではなく正常復帰も確認できる
- cronは決めた時間ごとに自動実行できるため、監視処理と相性が良い
- ログを残すことで、いつ何が起きたか後から確認できる
- スクリプトは実行環境（ホスト・コンテナ）によってパスが変わるため、環境を意識する必要がある
- 実際の運用ではコマンドを暗記するより、仕組みを理解して必要な時に調べることが重要だと感じた

## 覚えたコマンド
```bash
cat
rm
bash
crontab -e
crontab -l
service cron status
sudo service cron start
df
awk
curl
echo
if文
testコマンド（-f）
```
## 気づき
- 自動警告システムは「条件判定」「通知」「状態管理」「ログ保存」「自動実行」が組み合わさって完成する
- 単純な通知ではなく、実際の運用を考えると重複通知防止や復旧確認が必要になる
- 今回作成した仕組みはSREやインフラ運用で使われる監視の基本構成に近い