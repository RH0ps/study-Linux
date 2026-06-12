# Linux学習記録 Day14

## シェルスクリプトとcronを使ったディスク容量自動監視システムの構築

## 今回実装したこと
- `df -h` コマンドによる手動チェックから一歩進め、サーバーのディスク満タンによるシステム停止を防ぐための**自動監視・通知システム**を構築。

- **通知機能の実装**: 自分専用の実験用Slackワークスペースを立ち上げ、**Incoming Webhooks** を使って外部からメッセージを送信できる仕組みを構築。
- **データの切り出し**: `df -h` の結果から `grep`（行抽出）、`awk`（列切り出し）、`tr`（記号削除）を組み合わせ、使用率の数値（％なし）だけをピンポイントで抽出するコマンドを習得。
- **条件分岐スクリプトの作成**: 抽出した数値が閾値（テスト時は10%、本番運用は80%）を超えた場合、Slackへ自動で警告メッセージを `curl` でPOST送信するシェルスクリプト（`disk_monitor.sh`）を自作。
- **cronによる自動化**: `crontab -e` を使用し、過去に作成した既存の自動化タスクを残したまま、次の行へ安全に今回のスクリプトを定期実行（毎時0分）する設定を追加。

## 成果物（Slackへの通知画面）
- プログラムのテスト実行時に無事、Slack側に以下の通知が届くことを確認しました。

> ⚠️ ディスク残量警告 現在の使用率: 45%

## 作成したシェルスクリプト (`disk_monitor.sh`)

```bash
#!/bin/bash

# セキュリティのため実際のWebhook URLは隠しています
WEBHOOK_URL="https://slack.com"
THRESHOLD=80

USAGE=(df -h | grep '/' | awk '{print \$5}' | tr -d '%')

if [ "USAGE" -gt "THRESHOLD" ]; then
    PAYLOAD="{\"text\":\"⚠️ ディスク残量警告 現在の使用率: \${USAGE}%\"}"
    curl -X POST -H "Content-type: application/json" --data "PAYLOAD" "WEBHOOK_URL"
fi
```
