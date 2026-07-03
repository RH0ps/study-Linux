# Linux学習記録 Day35
# ops-lab：監視設計（logs / metrics / state）とPrometheus形式の理解

## 実践内容

### 監視データ構造の整理（logs / metrics / state）
- ops-lab内のデータを logs / metrics / state の3種類に分類して整理した。
- それぞれの役割を明確化した
  - logs：実行履歴・エラー・状態変化などの時系列データ
  - metrics：CPU・ディスク使用率・バックアップ回数などの数値データ
  - state：前回の状態を保持し、差分検知や制御に使用するデータ
- Dockerボリュームでこれらをホスト側へ永続化し、コンテナ再生成でもデータが保持される構成を維持した。

### metrics設計の実装（Prometheus風フォーマット）
- metricsディレクトリ配下にファイルを分割して管理する構成を採用した。
  - `disk.prom`：ディスク使用率・状態
  - `backup.prom`：バックアップ成功回数・失敗回数・最終時刻
- 各スクリプトでメトリクスを出力するように実装した
  - `disk_monitor.sh`：disk_usage_percent / disk_status
  - `backup.sh`：backup_success_total / backup_failure_total / backup_last_timestamp
- すべて「1ファイル＝複数メトリクス」のPrometheus形式で統一した。

### state管理の理解と実装確認
- stateディレクトリに disk_state.txt を配置し、前回状態（OK/WARN/CRITICAL）を保持する仕組みを確認した。
- stateを利用することで、状態変化時のみSlack通知を送る制御が可能になっていることを理解した。

### logsの役割整理
- logs/monitor.log に実行ログを記録し、以下を確認した
  - ディスク使用率の記録
  - state変化ログ
  - Slack通知結果
- logsはデバッグ・運用確認用の履歴データとして機能していることを整理した。

### Git管理の整理
- logs / metrics / state をすべてGit管理対象外に設定（.gitignoreで除外）
- GitHubにはスクリプトや設定ファイルのみを残し、実行時データはローカルで管理する構成を維持した。

### Docker構成の理解
- Docker volumeを利用して以下ディレクトリをホストと共有：
  - logs/
  - state/
  - metrics/
- コンテナを再作成しても監視データが保持される構成になっていることを確認した。

## 学んだこと
- logs / metrics / state は役割が完全に異なるデータ構造
- metricsは機械が読む数値データ、logsは人間用履歴、stateは制御用の記憶
- Prometheus形式のメトリクスは1ファイル内に複数指標を書く構造
- stateを使うことで、状態変化時のみ処理を実行する設計ができる
- Docker volumeを使うことで監視システムのデータ永続化が可能になる
- 監視システムは「収集（metrics）・記録（logs）・制御（state）」の3層構造で成り立つ

## 使用した主なコマンド

```bash
docker compose up -d --build  
docker exec  
docker inspect  
git ls-files  
git status  
nano  
cat  
ls  
grep  
rm
```