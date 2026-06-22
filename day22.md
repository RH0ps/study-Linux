# Linux学習記録 Day22
# Docker自動復旧設定＋バックアップ失敗時のエラー処理追加

## 今日学んだこと
- 今回は、Dockerコンテナの自動復旧設定と、バックアップ失敗時に原因を確認できるエラー処理を追加した。
## 実践したこと
### Dockerコンテナの自動復旧設定
- 今まではDockerコンテナが停止した場合、手動で起動する必要があった。
- そこで、コンテナ停止後も自動で復帰できるように設定した。

使用したコマンド

    docker update --restart always backup-container

確認

    docker inspect backup-container | grep RestartPolicy

- 設定後、コンテナを停止・起動して動作確認した。
```bash
    docker stop backup-container
```
```bash
    docker start backup-container
```
- Dockerコンテナが再起動できることを確認した。
### バックアップ失敗時のエラー処理追加

変更前

    cp "$TARGET_DIR/index.html" "$BACKUP_DIR/index_$DATE.html"

- この状態では、バックアップ対象のファイルが存在しない場合にエラーが出るだけで、ログで原因を確認できなかった。

- そこで、バックアップ対象ファイルの存在確認を追加した。

追加した処理

    if [ ! -f "$TARGET_DIR/index.html" ]; then
        echo "$(date) ERROR: index.html not found" >> "$BACKUP_DIR/backup.log"
        exit 1
    fi

処理内容

- -f
  - ファイルが存在するか確認する
- ファイルが存在しない場合
  - エラーメッセージをログへ保存
  - バックアップ処理を停止する
### エラー処理のテスト

正常な状態ではなく、わざとバックアップ対象ファイルを変更してテストした。

変更

    mv index.html index_test.html

バックアップ実行

    ./backup.sh

ログ確認

    cat /data/backup/backup.log

結果

    Sat Jun 20 12:33:48 UTC 2026 ERROR: index.html not found

- バックアップ対象がないことを検知し、エラーをログへ保存できることを確認した。
- その後、ファイル名を戻して復旧した。
```bash
    mv index_test.html index.html
```
## 覚えたコマンド

### Docker
- `docker update --restart always backup-container`コンテナを自動再起動する設定
- `docker inspect backup-container`コンテナ設定を確認する
- `docker stop backup-container`コンテナ停止
- `docker start backup-container`コンテナ起動
### Linux
- `if [ ! -f ファイル ]; then`ファイル存在確認を行う
- `exit 1`エラーとして処理を終了する
## 現在の構成

    Docker
    └ Ubuntu
       ├ cron（Docker起動時自動起動）
       ├ backup.sh
       │   ├ バックアップ作成
       │   ├ ログ保存
       │   ├ 古いファイル削除
       │   └ エラー検知
       ├ cronjob
       └ /data
          └ backup
              ├ 時刻付きバックアップ
              └ backup.log

## 改善できたこと

- Dockerコンテナ停止後も自動復帰できるようになった
- バックアップ失敗時に原因をログで確認できるようになった
- 正常動作だけではなく、異常時の対応も考えた自動化へ改善できた
## 次に改善したいこと

- バックアップログのローテーション
- Dockerコンテナの状態監視
- バックアップ通知機能の追加
- UTCから日本時間（JST）へ変更
