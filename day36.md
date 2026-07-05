# Linux学習記録 Day36 
## Docker環境の修正とバックアップシステムの改善
## 学習内容
### backup.sh の改善

- バックアップ保存先・ログ・メトリクス・状態管理ファイルのパスを整理した。
- ログ・メトリクス・状態管理ディレクトリを自動作成する処理（mkdir -p）を追加した。
- trapを利用したエラーハンドリングを改善し、エラー発生時に失敗回数を記録してSlackへ通知するようにした。
- バックアップ成功時には成功回数を更新し、Prometheus用メトリクスファイル（backup.prom）へ成功回数・失敗回数・最終実行時刻を出力するようにした。
- DRY_RUN・世代管理・Slack通知・ログ出力を含めたバックアップ処理全体を整理した。

### Docker環境のトラブルシューティング

- backup.shを修正したにもかかわらず、コンテナ内では古いスクリプトが実行される問題を調査した。
- grep・wc・tailなどを使ってホスト側とコンテナ内のスクリプトを比較し、コンテナ内のbackup.shが古いままであることを特定した。
- DockerfileでCOPYされる仕組みと、コンテナが古いイメージから起動していたことを理解した。
- docker compose down → docker compose build --no-cache → docker compose up -d を実行してイメージを再構築し、最新のbackup.shを反映させた。
- コンテナ内でSUCCESS_COUNTの処理が追加されていることを確認した。

### ops_cli.shの修正

- backコマンドでコンテナへ入れない原因を調査した。
- OPS_COMPOSEのパスが誤っており、存在しないdocker-compose.ymlを参照していたことを特定した。
- 正しいパス（~/study/github/ops-lab/docker-compose.yml）へ修正し、backコマンドから正常にコンテナへ接続できることを確認した。

### 動作確認

- backup.shを実行し、終了コード0で正常終了することを確認した。
- backup_success_countが更新されることを確認した。
- backup_failure_countが保持されることを確認した。
- backup.promに以下のメトリクスが出力されることを確認した。

```bash
backup_success_total
backup_failure_total
backup_last_timestamp
```

- バックアップファイルの作成、ログ出力、世代管理、Slack通知が正常に動作することを確認した。

## 学んだこと

- DockerfileでCOPYしたファイルはイメージに含まれるため、ホスト側で編集した内容を反映するにはイメージの再ビルドが必要であることを理解した。
- ホスト側とコンテナ内のファイルを比較しながら原因を切り分ける方法を学んだ。
- バックアップ処理だけでなく、メトリクス・ログ・通知・エラーハンドリングまで含めた運用を意識したバックアップシステムへ改善できた。