# Linux学習記録 Day21
# バックアップ不要ファイル自動削除＋Docker操作コマンド短縮

## 今日学んだこと
- 今回は、増え続けるバックアップファイルを自動削除する仕組みと、Dockerコンテナへ入るコマンドの短縮を行った。
## 実践したこと
### 古いバックアップファイルの自動削除
追加した処理

    find "$BACKUP_DIR" -name "index_*.html" -mtime +2 -print -delete >> "$BACKUP_DIR/backup.log"

処理内容
- find
  - 条件に一致するファイルを検索する
- -name "index_*.html"
  - バックアップファイルのみ対象にする
- -mtime +2
  - 2日以上古いファイルを対象にする（テスト用）
- -print
  - 削除対象のファイル名を表示する
- -delete
  - 対象ファイルを削除する
テスト用に5日前のファイルを作成
```bash
    touch -d "5 days ago" /data/backup/index_test_old.html
```
実行

    ./backup.sh

ログ確認

    cat /data/backup/backup.log

結果

    /data/backup/index_test_old.html
    Fri Jun 19 12:08:04 UTC 2026 old backup check completed

- 古いバックアップファイルが自動削除されることを確認できた。

本番
```bash
find "$BACKUP_DIR" -name "index_*.html" -mtime +2 -print -delete >> "$BACKUP_DIR/backup.log"
```
### Docker操作コマンド短縮
変更前

    docker exec -it backup-container bash

- 長いためaliasを設定して短縮した。

.zshrcに追加

    alias back="docker exec -it backup-container bash"

反映

    source ~/.zshrc

変更後

    back

だけでDockerコンテナへ入れるようになった。
## 覚えたコマンド

### Linux
- `find`条件に合うファイルを検索する
- `touch -d "5 days ago" ファイル名`指定した日時のファイルを作成・変更する
- `-print`対象を表示する
- `-delete`対象ファイルを削除する
### Docker
- `docker exec -it backup-container bash`Dockerコンテナ内へ入る
- `source ~/.zshrc`設定変更を反映する
## 現在の構成

    Docker
    └ Ubuntu
       ├ cron（Docker起動時自動起動）
       ├ backup.sh
       │   ├ バックアップ作成
       │   ├ ログ保存
       │   └ 古いファイル削除
       ├ cronjob
       └ /data
          └ backup
              ├ 時刻付きバックアップ
              └ backup.log


- バックアップ作成
- ログ保存
- 古いバックアップ削除
- Docker操作短縮

まで自動化できる環境を構築できた。
## 次に改善したいこと
- バックアップ失敗時のエラー処理追加
- UTCから日本時間（JST）へ変更
- AWS環境でも同じ自動化構成を作れるようにする
