# Linux学習記録 Day18
# Dockerでバックアップ自動化環境を構築
- Dockerを使って、Linux環境内でバックアップ処理を実行できる環境を作成した。
- 今まではMac上でcronを使って`backup.sh`を実行していたが、Dockerコンテナ内にUbuntu環境を作成し、その中でバックアップ処理を動かす形に変更。

## 実践したこと

## Docker環境構築

Dockerfileを作成し、以下の環境を構築。

- Ubuntu
- cron
- nano
- backup.sh

Dockerfileで環境を定義し、イメージを作成。

```bash
docker build -t my-backup .
```

作成したイメージからコンテナを起動。

```bash
docker run -it \
-v /Users/r.h/webusaitozidouka:/data \
--name backup-container \
my-backup
```

`-v`を使い、Mac側のフォルダをDocker内の`/data`へ共有。

## 実践したコマンド

### Docker

```bash
docker build -t my-backup .
```

Dockerfileからイメージを作成。

```bash
docker images
```

作成したDockerイメージを確認。

```bash
docker run
```

イメージからコンテナを起動。

```bash
docker rm
```

コンテナを削除。

### Linux

```bash
ls
```

ファイル一覧を確認。

```bash
pwd
```

現在いるディレクトリを確認。

```bash
nano
```

ファイルを編集。

```bash
chmod +x
```

ファイルに実行権限を付与。

### cron

```bash
crontab -e
```

cron設定を編集。

```bash
service cron start
```

cronを起動。

```bash
service cron status
```

cronの状態を確認。

## トラブルシューティング

### Docker内でバックアップが動かなかった原因

Mac側のパス：

```
/Users/r.h/webusaitozidouka
```

はDockerコンテナ内には存在しないためエラーになった。

Dockerではコンテナは別環境として動くため、`-v`を使ってMac側のフォルダをDocker内へ共有する必要があることを学んだ。

## 現在の構成

```
Docker
└ Ubuntu
   ├ cron
   ├ backup.sh
   └ /data
      ├ index.html
      └ backup
          └ index_20260616.html
```

Docker内で`backup.sh`を実行し、バックアップファイル作成まで確認できた。

## 次に改善したいこと

- 同じ日に複数回バックアップした場合の上書き問題を解決
  - 時刻まで含めたファイル名に変更する

- バックアップログを残す

- エラー発生時の処理を追加する

- 古いバックアップを自動削除する仕組みを作る

- Docker起動時にcronが自動起動するように設定する

