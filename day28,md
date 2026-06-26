# Linux学習記録 Day28
# Docker環境の再構築とdocker-compose導入
## 実践したこと
- Docker環境（study-env）をDockerfileで再構築し、Ubuntu開発環境をコード化
- Dockerfileにより、sudo・vim・curl・git・nano・nginx・cron・tzdataを含む開発環境を自動構築
- r.hユーザー作成とsudo権限付与をDockerfileで自動化
- timezoneをAsia/Tokyoに設定し、JST環境に統一
- ホスト（Mac）の ~/study/docker とコンテナ /home/r.h/docker をvolumeで同期
- docker runでの手動起動からdocker-compose管理へ移行
- docker-compose.ymlを作成し、コンテナ起動・管理を簡略化
- 古い不要コンテナ（backup-container / my-nginx）を削除し環境を整理
- 不要イメージ（my-backup / nginx）を削除してDocker環境をクリーン化
- alias back を新しい study-container に修正し、即アクセスできるようにした
## 実行した操作
```bash
cd ~/study
nano Dockerfile
```
```bash
FROM ubuntu:latest

RUN apt update && apt install -y \
    sudo vim curl git nano nginx cron tzdata

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    echo "Asia/Tokyo" > /etc/timezone

RUN useradd -m r.h && \
    echo "r.h:password" | chpasswd && \
    usermod -aG sudo r.h

WORKDIR /home/r.h
USER r.h
CMD ["/bin/bash"]
```
```bash
docker build -t study-env .
```
```bash
docker run -it \
--name study-container \
-v ~/study/docker:/home/r.h/docker \
study-env
```
```bash
nano docker-compose.yml
```
```bash
services:
  study:
    image: study-env
    container_name: study-container
    tty: true
    stdin_open: true
    volumes:
      - ./docker:/home/r.h/docker
```
```bash
docker compose up -d
```
- コンテナ操作・確認
```bash
docker ps
docker ps -a
```
```bash
docker exec -it study-container bash
```
```bash
whoami
groups
date
ls ~/docker
```
```bash
docker stop backup-container
docker rm backup-container

docker stop my-nginx
docker rm my-nginx
```
```bash
docker rmi my-backup
docker rmi nginx
```
```bash
cat ~/.zshrc
alias back='docker exec -it study-container bash'
source ~/.zshrc
```
## 間違えたこと
- backコマンドが古いコンテナ（backup-container）を参照していた
- docker compose up時に既存コンテナ名の競合エラーが発生
- コンテナを間違えて操作し、一時的にどの環境に入っているか混乱した
- コンテナ内でdockerコマンドが使えないことを理解していなかった（ホストとコンテナの違いの認識ミス）
- コンテナを間違えて操作し、my-backup側でsudo設定を変更してしまった
## 学んだこと
- Dockerfileは「環境の設計図」であり、コンテナ内の変更は自動では反映されない
- コンテナは削除すると中の変更は消えるため、再現性はDockerfileで担保する必要がある
- docker-composeはrunコマンドを簡略化し、コンテナ管理を一元化できる
- 同じ名前のコンテナは同時に存在できない
- コンテナごとに環境が完全に分離されている（別コンテナの影響は基本受けない）
- ホストとコンテナは別環境であり、dockerコマンドはホスト側で実行するもの
## 覚えたコマンド
- `docker build -t study-env .`
- `docker run -it --name study-container -v ~/study/docker:/home/r.h/docker study-env`
- `docker compose up -d`
- `docker compose down`
- `docker ps -a`
- `docker rm / docker stop`
- `docker rmi`
- `docker exec -it study-container bash`
- `alias back`
- `docker images`
- `docker ps`
- `exit`
## 気づき
- コンテナ運用は「手動管理」ではなく「コード管理（Dockerfile + compose）」が前提
- 一時的な修正は消えるため、残すなら必ずDockerfileに戻す必要がある
- コンテナ名・alias・実際の稼働環境の対応関係を常に意識する必要がある