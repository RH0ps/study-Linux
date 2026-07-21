# Linux学習記録 Day53
# restore.sh機能の安全性向上とプロジェクト整理
## 実践内容
### restore.shのバックアップ復元処理の安全性確認と改善
restore.shの内容を確認し、以下の復元機能が実装されていることを確認。
- 復元対象ファイルの選択機能
- バックアップ一覧表示
- 最新バックアップまたは指定バックアップからの復元
- 不正なバックアップパスの拒否
- 空ファイルバックアップの拒否
- バックアップファイル名形式の検証
- 古すぎるバックアップの復元拒否
- 復元前スナップショット作成
- SHA256による復元後チェック
- 失敗時のロールバック
- 復元履歴の記録
- Slack通知対応
- dry-run対応
- 多重実行防止用ロック処理

restore.shの安全チェック部分を確認し、バックアップ指定時にパス指定を許可してしまう問題を防ぐため、以下のような検証を追加。

```bash
if [[ "$TARGET_BACKUP" == */* ]]; then
    log_error "Invalid backup name"
    exit 1
fi
```

これにより、backupディレクトリ外のファイルを指定して復元することを防止。

また、ShellCheckによる静的解析を実施。

```bash
shellcheck restore.sh
```

最初はSC1091が発生した。

原因はsourceしている外部ファイル(lib/log.sh、.env)をShellCheckが追跡できなかったため。

```bash
shellcheck -x restore.sh
```

を使用することで外部ファイルを読み込んだ状態で解析でき、警告が消えることを確認。

構文確認も実施。

```bash
bash -n restore.sh
```

エラーがないことを確認。

### ops-lab全体の整理を実施

現在の構成をtreeコマンドで確認し、以下のフォルダ内容を確認。

- backup
  - 自動バックアップファイル
  - backup.log
  - index.htmlやdocker-compose.ymlの履歴

- backup_snapshots
  - restore実行前に作成される一時的なスナップショット

- cli
  - opsコマンドの各機能ファイル
  - backup.sh
  - docker.sh
  - lifecycle.sh
  - status.sh
  - version.sh
  - diagnose.shなど

- lib
  - log.shのみ存在
  - 各スクリプト共通のログ関数を管理

- logs
  - 実行時に生成されるログファイル
  - app.log
  - backup.log
  - cpu.log
  - memory.log
  - monitor.log
  - restore.log
  - rotate.logなど

- metrics
  - Prometheus形式の監視メトリクス

- state
  - 監視状態やカウント情報

整理作業として、不要になった以下のファイルを削除した。

- test_log.sh
  - log.shの動作確認用スクリプト
  - 本番機能では使用しないため削除

- cron.log
  - 一時的なcron実行ログ
  - logs管理対象ではなく不要だったため削除

- backup_snapshots
  - Git管理対象ではなく、一時的に生成される復元用データだったため削除
  - 必要になった場合はrestore.sh実行時に再生成される

削除前には以下のような確認を実施。

```bash
grep -R "対象ファイル名" .
```

参照箇所を確認し、削除してもスクリプトやREADMEへの影響がないことを確認。

また、ログファイルについてはGit管理対象ではなく、実行時に生成されるデータであることを確認。

```bash
git ls-files logs
```

でGit管理されていないことを確認。

## 間違えたこと

- restore.shのロック処理について、コメント名だけを見て別の処理が存在すると判断しかけた。
  実際には以下の処理がすでに存在していた。

```bash
# ロック対応確認（macOS / Linux）

if command -v flock >/dev/null 2>&1; then
    LOCK_SUPPORTED=true
else
    LOCK_SUPPORTED=false
fi
```

- 削除対象の判断では、Git管理されているかだけではなく、実行時に再生成されるデータか、運用上必要な履歴かを確認する必要があることを確認。

- libフォルダについて、log.shのみ存在するため整理候補として考えたが、共通ライブラリとして管理する目的があるため、単純に削除するのではなく今後の構成変更時に判断する必要がある。

## 学んだこと
- Git管理対象ではないファイルでも、運用上必要なデータの場合があるため、削除前には参照元や生成タイミングを確認する必要がある。
- logs、metrics、stateなどのディレクトリはアプリケーション実行時に生成されるデータを管理する場所であり、ソースコードとは分離する設計が重要である。
- restore機能では単純なコピーではなく、安全確認、バックアップ検証、ロールバック、履歴管理など複数の仕組みが必要になる。
- ShellCheckは単純な文法確認だけではなく、外部ファイル読み込みやコード品質確認にも利用できる。
- ops-labは単なるスクリプト集ではなく、監視・バックアップ・復元・CLI操作を含む運用管理システムとして整理する必要がある。
## 覚えたこと・コマンド

```bash
# ShellCheck実行
shellcheck restore.sh

# 外部sourceファイル込みでShellCheck実行
shellcheck -x restore.sh

# Bash構文確認
bash -n restore.sh

# ファイル参照確認
grep -R "ファイル名" .

# Git管理対象確認
git ls-files logs

# ディレクトリ構成確認
tree -L 2

# ファイル一覧確認
ls -a

# ファイル削除
rm ファイル名

# ディレクトリ削除
rm -rf ディレクトリ名
```
