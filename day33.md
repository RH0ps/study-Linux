# Linux学習記録 Day33
# ops-lab監視システム機能改善・本番環境移行・Docker構成修正・動作確認

## 実践内容

これまでDocker実験環境で開発・改善していた`ops-lab`の監視・自動化環境を、GitHub管理している本番用リポジトリ`ops-lab`へ移行した。

## Docker実験環境で改善した監視機能を本番へ移行

これまでDocker実験環境で開発・改善していた`disk_monitor.sh`を、本番用`ops-lab`環境へ移行した。

改善済みの監視機能

- 状態変化時のみSlack通知
- Prometheus形式メトリクス生成
- flockによる多重実行防止
- timeoutによる監視処理停止対策
- trapによるエラー検知

Day33ではこれらの機能を含む監視システムを本番環境へ反映し、Docker構成の調整と動作確認を行った。

## 本番環境移行で行った作業

Docker実験環境で完成していた監視システムを本番環境へ移行するため、以下の調整を行った。

- Docker本番環境に合わせた構成修正（COPY / volume整理）
- コンテナ再起動可能な状態への調整
- Docker実験環境との差分吸収
- 既存監視システムの本番動作保証

## Docker実験環境での動作確認

本番移行前にDocker環境で監視システムが正常動作するか確認した。

確認内容

- `disk_monitor.sh`の構文チェック
- `bash -x`による詳細実行確認
- `.env`から`Slack Webhook URL`読み込み確認
- ディスク使用率取得確認
- `state`管理確認
- Prometheus形式メトリクス生成確認
- Slack通知処理確認


確認結果


ディスク使用率取得成功
```bash
usage=2%
```
状態管理成功
```bash
state=OK
```
メトリクス生成成功
```bash
node_disk_usage_percentage{mountpoint="/"} 2
```
Slack通知成功
```bash
state: UNKNOWN -> OK (slack=1)
```
## ops-lab本番側への移行

Docker実験環境で動作確認できたファイルをGitHub管理のops-labへ移行。


移行したファイル

- disk_monitor.sh
- backup.sh
- cronjob.txt
- entrypoint.sh
- Dockerfile
- docker-compose.yml
- .env


## Docker構成差分による問題修正

移行時、Docker実験環境とops-lab本番環境でディレクトリ構成が違うため、そのままでは動作しなかった。


Docker実験環境では`docker`ディレクトリを前提にした設定が残っていた。


問題
```bash
COPY docker/ /home/r.h/docker/
```
ops-labではbuild contextがリポジトリ直下だったため、`docker/` ディレクトリが存在せずbuild失敗。


修正後
```bash
COPY . /home/r.h/docker/
```
build contextに合わせて、リポジトリ全体をコンテナ内へコピーする構成へ変更した。


## docker-compose.yml修正

以前のvolume設定
```bash
./docker:/home/r.h/docker:ro
```
では、コンテナ内の作業ディレクトリ全体を読み取り専用でマウントしていた。

そのため、監視スクリプトが生成するstateやmetricsを書き込めず、本番環境では問題になる構成だった。


修正後
```bash
./logs:/home/r.h/docker/logs
./state:/home/r.h/docker/state
./metrics:/home/r.h/docker/metrics
```
ログ・状態ファイル・メトリクスだけをホスト側と同期する構成へ変更した。


## 本番Docker環境再構築

修正後、ops-lab本番環境を再作成した。


実行
```bash
docker compose down
docker rm -f ops-lab
docker compose up -d --build
```
途中で既存コンテナ名の競合が発生。


エラー
```bash
Conflict. The container name "/ops-lab" is already in use
```
対応
```bash
docker rm -f ops-lab
```
既存コンテナを削除して再作成した。


## healthcheck確認

コンテナ起動後に確認。

```bash
docker ps
```
結果
```bash
Up (healthy)
```
cronプロセスが正常起動していることを確認した。


## 本番環境で最終確認

コンテナ内部へ入り監視スクリプトを確認。

```bash
docker exec -it ops-lab bash
```
構文チェック
```bash
bash -n disk_monitor.sh
```
詳細実行
```bash
bash -x disk_monitor.sh
```
結果

正常終了。


ログ確認
```bash
2026-07-01 17:33:48 [INFO] usage=2% state=OK
```
状態確認
```bash
cat state/disk_state.txt

OK
```
メトリクス確認
```bash
cat metrics/disk.prom

node_disk_usage_percentage{mountpoint="/"} 2
```
## 学んだこと

- Docker実験環境と本番リポジトリではディレクトリ構成が違うため、`Dockerfile`や`docker-compose.yml`をそのまま移行できない場合があることを理解した。
- `Dockerfile`の`COPY`と`volume``mount`の役割の違いを理解した。

COPY
イメージ作成時にファイルをコピーする。

volume
コンテナ起動時にホスト側と同期する。

- Dockerでは「イメージ（COPY）」と「実行時マウント（volume）」の役割が違い、実行環境前提で設計しないと本番で破綻することを理解した。
- 実験環境で動作確認したシステムを本番へ移行する場合、スクリプトだけではなくDockerfile、docker-compose.yml、環境変数、volume設定まで確認する必要があると理解した。
- Docker + Bash + cron + Slack通知 + state管理 + metrics生成を組み合わせた運用監視環境を再現可能な形で構築できた。


## 現在の状態

ops-lab本番環境は以下が動作する状態。

- Dockerコンテナ起動
- cron実行環境
- disk_monitor.sh
- backup.sh
- Slack通知
- state管理
- Prometheus形式メトリクス生成
- healthcheck
- GitHub管理


Docker実験環境で改善した監視システムを、本番用ops-lab環境へ移行完了。