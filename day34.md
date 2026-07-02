```bash
# Linux学習記録 Day34
# ops-lab プロジェクト構成の整理と Docker ボリューム管理
## 実践内容
### プロジェクト構成の整理
- `~/study/docker` に残っていた旧プロジェクトを削除し、`ops-lab` に開発環境を統一した。
- 重複していた `docker-compose.yml`、`Dockerfile`、各種スクリプトを整理し、現在利用するファイルを明確化した。
- `docker inspect` を利用して Docker コンテナのボリュームマウント先を確認した。

### Docker ボリュームの理解
- 以下のディレクトリをホストとコンテナで共有する構成を確認した。
  - `logs/`
  - `state/`
  - `metrics/`
- `docker-compose.yml` に
  `./backup:/home/r.h/backup`
  を追加し、バックアップファイルをホスト側へ保存できるようにした。
- `backup.sh` を実行し、バックアップファイルとログがホスト・コンテナ間で同期されることを確認した。

### Git 管理の整理
- `.gitignore` を見直し、以下を Git 管理対象外に設定した。
  - `.env`
  - `.DS_Store`
  - `logs/`
  - `metrics/`
  - `state/`
  - `backup/`
  - `*.log`
- 以前 Git 管理されていた
  - `metrics/disk.prom`
  - `state/disk_state.txt`
  を `git rm --cached` で管理対象から外した。
- GitHub にはソースコードのみを管理し、実行時データはローカルで保持する構成へ変更した。

### Dockerfile の修正
- `COPY docker/ /home/r.h/docker/` から
  `COPY . /home/r.h/docker/`
  に変更し、プロジェクト全体をコンテナへコピーする構成へ整理した。
- Git 上で `dockerfile` と認識されていたファイル名を `Dockerfile` に修正した。
- `git ls-files` を利用し、Git が正しく `Dockerfile` と認識していることを確認した。

## 学んだこと
- Docker ボリュームを利用することで、コンテナを再作成しても必要なデータを保持できる。
- 実行時に生成されるログや状態ファイル、メトリクス、バックアップは Git で管理しないのが一般的である。
- `.gitignore` は新規ファイルのみを無視するため、既に Git 管理されているファイルは `git rm --cached` が必要になる。
- `docker inspect` を使うことで、実際のボリュームマウント先を確認できる。
- プロジェクト構成を整理することで、ソースコードと実行データを明確に分離できる。
- ホスト側へボリュームマウントすることで、コンテナを再作成してもバックアップやログなどの永続データを維持できる。
- Dockerイメージに含めるデータと、ボリュームで永続化するデータは役割が異なることを理解した。

## 使用した主なコマンド

```bash
docker inspect
docker compose up -d --build
docker exec
git ls-files
git rm --cached
git status
git commit
git push
nano
diff
grep
rm -rf
```