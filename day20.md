# Linux学習記録 Day20
# Docker起動時にcron自動起動＋バックアップ常時稼働化

## 今日学んだこと

- Day18でDocker内のUbuntu環境でバックアップ処理を実行できる環境を構築した。
- Day19では時刻付きバックアップとログ保存機能を追加した。
- 今回は、毎回手動でcronを起動しなくてもDocker起動時に自動でcronが動くように改善した。

今までは、

service cron start

を手動で実行してcronを起動していた。

今回はDocker起動時に自動でcronが起動する構成へ変更した。

---

## 実践したこと

## Dockerfileの変更

Docker起動時にcronを自動起動するように変更した。

変更後：

CMD ["cron", "-f"]

cron -fを使うことで、Dockerコンテナを起動したままcronを動かせるようにした。

---

## cron設定ファイル作成

cronjobファイルを作成し、自動バックアップ設定を追加した。

テスト時：

*/1 * * * * /backup.sh >> /data/backup/cron.log 2>&1

1分ごとにバックアップを実行し、動作確認を行った。

確認後：

0 9 * * * /backup.sh >> /data/backup/cron.log 2>&1

毎日9時にバックアップする設定へ変更した。

---

## Dockerをバックグラウンド実行

変更前：

docker run -it

ターミナルを開いた状態でDockerを操作していた。

変更後：

docker run -d \
-v /Users/r.h/webusaitozidouka:/data \
--name backup-container \
my-backup

-dを使い、バックグラウンドでコンテナを起動できるようにした。

---

## 使用したコマンド

## Docker

docker rm -f backup-container

動作中のコンテナを停止・削除。

docker build -t my-backup .

Dockerfileからイメージを作成。

docker run -d

バックグラウンドでコンテナ起動。

docker ps

起動中のコンテナを確認。

---

## Linux

nano cronjob

cron設定ファイルを編集。

ls

ファイル一覧確認。

---

## トラブルシューティング

cron実行時に、

cp: cannot stat '/Users/r.h/webusaitozidouka/index.html': No such file or directory

というエラーが発生した。

原因：

Docker内からMac側のパスを直接指定していたため。

Dockerではホスト側とコンテナ内の環境が分かれているため、-vを使ってMac側のフォルダをDocker内の/dataへ共有する必要があることを確認した。

修正後は、

index_20260618_132101.html

などのファイルが自動生成され、バックアップ処理が正常に動作した。

---

## 現在の構成

Docker
└ Ubuntu
   ├ cron（自動起動）
   ├ backup.sh
   ├ cronjob
   └ /data
      └ backup
          ├ 時刻付きバックアップファイル
          └ backup.log

Dockerを起動するだけでcronが動作し、自動バックアップできる環境を構築できた。

---

## 次に改善したいこと

- 古いバックアップファイルを自動削除する仕組みを追加
- 一定期間以上前のバックアップを削除する
- バックアップ削除のログを残す
- バックアップ失敗時のエラー処理追加
- UTCから日本時間（JST）へ変更
