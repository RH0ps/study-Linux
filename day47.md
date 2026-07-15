# Linux学習記録 Day47
# Health Check機能追加とCLI運用改善
## 実践したこと

今日はops-labの運用性・保守性を向上させるための改善作業を行った。

まず、作成していた共通ログライブラリ（lib/log.sh）を実際の監視スクリプトへ適用した。

以前は各スクリプト内で、

000
echo "$(date "+%F %T") [INFO] message" >> "$LOG_FILE"
000

のように個別でログを書き込んでいたが、ログ形式や管理方法がスクリプトごとに分散していた。

そこで、lib/log.shに用意した、

- log_info
- log_warn
- log_error

を利用する形へ変更し、ログ出力方法を統一した。

今回はbackup.shに続き、disk_monitor.shにも共通ログライブラリを適用した。

変更内容

- already running時の警告ログをlog_warnへ変更
- 使用率取得失敗時のエラーログをlog_errorへ変更
- 監視結果ログをlog_infoへ変更
- Slack通知失敗ログをlog_warnへ変更
- Slack無効時のログをlog_infoへ変更
- 状態変化ログをlog_infoへ変更
- ログローテーション実行ログをlog_infoへ変更

変更後、Dockerコンテナ内でdisk_monitor.shを実行し、以下のような共通形式のログが出力されることを確認した。

000
[2026-07-15 21:10:05] [INFO] usage=2% state=OK
000

これによりdisk_monitor.shのログ管理が共通化された。

---

次に、health_check.shを作成した。

目的は、ops-lab全体の状態確認を1つのコマンドで実行できるようにすること。

確認項目として以下を実装した。

- Dockerコンテナ起動確認
- 監視スクリプト存在確認
- logsディレクトリ確認
- metricsディレクトリ確認
- stateディレクトリ確認
- Prometheus形式メトリクスファイル確認
- 各種ログファイル確認

実行結果。

000
===== Health Check =====
[OK] Docker container
[OK] disk_monitor.sh
[OK] cpu_monitor.sh
[OK] backup.sh
[OK] restore.sh
[OK] log_rotate.sh
[OK] logs/
[OK] metrics/
[OK] cpu.prom
[OK] disk.prom
[OK] backup.prom
[OK] state/
[OK] monitor.log
[OK] cpu.log
[OK] backup.log
[OK] restore.log
[OK] rotate.log
000

正常に全項目を確認できる状態になった。

---

さらに、ops CLIへ新しい運用コマンドを追加した。

追加したコマンド。

- ops cpu
  - cpu_monitor.shを実行

- ops restore
  - restore.shを実行

- ops rotate
  - log_rotate.shを実行

- ops health-check
  - health_check.shを実行

- ops metrics
  - metricsディレクトリの一覧・内容確認

- ops state
  - stateファイル一覧・状態確認

これにより、監視・復元・ログ管理・状態確認をopsコマンドから統一して操作できるようになった。

また、ops health-check実行時に、

000
bash: /health_check.sh: No such file or directory
000

というエラーが発生した。

原因は、CLI側でhealth_check.shのパス指定が間違っていたためだった。

修正後、

000
ops health-check
000

で正常に実行できることを確認した。

---

## 間違えたこと

### Mac上でflockを使用して動作確認した

cpu_monitor.shやdisk_monitor.shでは多重実行防止のために、

000
flock
000

を使用している。

しかしmacOSには標準でflockが存在しないため、

000
./disk_monitor.sh: line 65: flock: command not found
000

というエラーが発生した。

原因はスクリプトの問題ではなく、実行環境の違いだった。

Macでは動かないが、実際の実行環境であるUbuntu Dockerコンテナ内では正常に動作することを確認した。

---

### opsコマンド名を間違えた

最初、

000
ops health_check
ops health_check.sh
000

のように実行したが、登録しているコマンド名は、

000
ops health-check
000

だった。

CLIではコマンド名を自分で定義しているため、登録名と実行名を一致させる必要があることを確認した。

---

## 学んだこと

### 運用ツールは「動く」だけではなく「管理しやすい設計」が重要

監視スクリプトが動作するだけではなく、

- ログ形式の統一
- 状態確認
- 障害調査のしやすさ
- 操作方法の統一

が運用では重要だと学んだ。

今回の共通ログライブラリ化やhealth_check.sh作成は、実際の運用環境で必要になる考え方だと理解した。

---

### 実行環境を意識する必要がある

同じBashスクリプトでも、

- macOS
- Ubuntu
- Dockerコンテナ

では利用できるコマンドや挙動が違う。

今回のflock問題から、開発環境と実行環境を分けて考える重要性を学んだ。

---

### CLIは単なるショートカットではなく運用インターフェースになる

opsコマンド追加を通して、CLIは単純にコマンドを短縮するものではなく、

- 操作方法を統一する
- 作業手順を標準化する
- ミスを減らす

ための運用レイヤーになることを理解した。

---

## 覚えたこと・使用したコマンド

### ファイル作成・編集

000
touch health_check.sh
chmod +x health_check.sh
nano health_check.sh
cat health_check.sh
000

### ファイル検索・確認

000
ls
ls -a
ls -l logs
grep -n "LOG_FILE" cpu_monitor.sh
grep -n '>> "$LOG_FILE"' disk_monitor.sh
tail -10 logs/monitor.log
000

### Dockerコンテナ操作

000
ops back
cd docker
./disk_monitor.sh
./cpu_monitor.sh
exit
000

### CLI確認

000
ops cpu
ops restore
ops rotate
ops health-check
ops metrics
ops state
000

### Git管理前の確認

000
ls -a
000

---

## 今日の成果

Day47では、ops-labを単なるスクリプト集から、より運用を意識した環境へ改善した。

実施内容。

- health_check.shによる状態確認機能追加
- ops CLIへの運用コマンド追加
- disk_monitor.shの共通ログライブラリ対応
- disk_monitor.shのログ出力形式の統一
- Docker環境とmacOS環境の違い確認

今後はcpu_monitor.sh、restore.sh、log_rotate.shせにも共通ログライブラリを適用し、ops-lab全体のログ管理を統一していく。