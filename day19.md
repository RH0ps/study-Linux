# Linux学習記録 Day19
# バックアップ自動化システム改善①：時刻付きバックアップ＋ログ機能追加

## 今日学んだこと
- Day18でDocker内のUbuntu環境でバックアップ処理を実行できるようにしたため、今回はバックアップシステムの改善を行った。
- 今までのバックアップでは、同じ日に複数回実行すると同じファイル名になり、以前のバックアップが上書きされる問題があった。
- ファイル名に時刻を追加し、実行履歴をログに保存する仕組みを追加した。

---

## 実践したこと

## 時刻付きバックアップ

変更前：

    index_20260617.html

同じ日に複数回実行すると上書きされる。

↓

変更後：

    index_20260617_103935.html

日時＋時刻をファイル名に追加することで、同じ日に複数回バックアップしても別ファイルとして保存されるようにした。

使用したコード：

    DATE=$(date "+%Y%m%d_%H%M%S")

    cp "$TARGET_DIR/index.html" "$BACKUP_DIR/index_$DATE.html"

---

## バックアップログ追加

バックアップ実行時にログを残すようにした。

追加した処理：

    echo "$(date) backup completed" >> "$BACKUP_DIR/backup.log"

実行結果：

    Wed Jun 17 10:39:35 UTC 2026 backup completed
    Wed Jun 17 10:40:01 UTC 2026 backup completed

いつバックアップされたか確認できるようになった。

---

## 覚えたコマンド
## Linux
    nano /backup.sh

シェルスクリプトを編集。

    cat /data/backup/backup.log

ログ内容を確認。

    ls /data/backup

バックアップファイル一覧を確認。

    ./backup.sh

シェルスクリプトを実行。

## dateコマンド

    date "+%Y%m%d_%H%M%S"

現在日時を指定した形式で取得できる。

例：

    20260617_103935
---

## 現在の構成

    Docker
    └ Ubuntu
       ├ cron
       ├ backup.sh
       └ /data
          └ backup
              ├ index_20260617_103935.html
              └ backup.log

Docker内でバックアップ処理を実行し、時刻付きファイル作成とログ保存まで確認できた。

---

## 次に改善したいこと

- Docker起動時にcronが自動起動するように設定

- UTCから日本時間（JST）へ変更

- バックアップ失敗時のエラー処理追加

- 古いバックアップを自動削除する仕組み作成
