# Linux学習記録 Day23
# バックアップログ解析＋状態確認スクリプト作成

## 今日学んだこと

- 今回は、バックアップ処理が正常に動いているか確認できるように、ログ解析と状態確認スクリプトを作成した。
- Day22まででバックアップ自動化・削除処理・エラー処理まで実装したため、今回は運用時に必要な「状態確認」の仕組みを追加した。

---

## 実践したこと

## バックアップログの集計

バックアップログから成功回数とエラー回数を確認した。

成功回数確認

    grep "backup completed" /data/backup/backup.log | wc -l

処理内容

- grep
  - 指定した文字列を検索する
- wc -l
  - 検索結果の行数を数える

結果

- バックアップ成功回数を確認できた。

エラー回数確認

    grep "ERROR" /data/backup/backup.log | wc -l

- ERRORを含むログを検索し、バックアップ失敗回数を確認した。

結果

- エラーが発生した回数を確認できた。

---

## 状態確認スクリプト作成

毎回コマンドを入力して確認する手間を減らすため、バックアップ状態確認用のスクリプトを作成した。

作成

    nano /status.sh

内容

    #!/bin/bash

    BACKUP_LOG="/data/backup/backup.log"

    echo "===== Backup Status ====="

    echo ""

    echo "Success:"
    grep "backup completed" "$BACKUP_LOG" | wc -l

    echo ""

    echo "Error:"
    grep "ERROR" "$BACKUP_LOG" | wc -l

    echo ""

    echo "Last backup:"
    grep "backup completed" "$BACKUP_LOG" | tail -1

    echo ""

    echo "========================"

実行権限追加

    chmod +x status.sh

実行

    ./status.sh

結果

    ===== Backup Status =====

    Success:
    1595

    Error:
    1

    Last backup:
    Sun Jun 21 09:00:02 UTC 2026 backup completed

    ========================

- ログから成功回数・エラー回数・最後のバックアップ時間を自動表示できるようになった。

---

## 覚えたコマンド

### Linux

- `grep`
  - 指定した文字列を検索する

- `wc -l`
  - 行数を数える

- `tail -1`
  - 最後の1行を取得する

- `chmod +x`
  - ファイルを実行可能にする

- `./ファイル名`
  - シェルスクリプトを実行する

---

## 現在の構成

    Docker
    └ Ubuntu
       ├ cron（Docker起動時自動起動）
       ├ backup.sh
       │   ├ バックアップ作成
       │   ├ ログ保存
       │   ├ 古いファイル削除
       │   └ エラー検知
       │
       ├ status.sh
       │   ├ 成功回数確認
       │   ├ エラー回数確認
       │   └ 最終バックアップ確認
       │
       └ /data
          └ backup
              ├ 時刻付きバックアップ
              └ backup.log

---

## 改善できたこと

- バックアップが正常に動作しているか確認できるようになった
- ログを分析して状態を判断できるようになった
- 自動化だけではなく、運用・監視の考え方を学んだ

## 次に改善したいこと

- 成功率の表示
- 最終バックアップからの経過時間表示
- cronによる監視自動化
- Docker外から状態確認できる仕組み作成
