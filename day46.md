# Linux学習記録 Day46
# ログ管理の共通化とログローテーションの実装
## 実践したこと

- `lib/log.sh` を作成し、`log_info`・`log_warn`・`log_error` の3種類の共通ログ関数を実装した。
- 動作確認用として `test_log.sh` を作成し、共通ログライブラリが正常に動作することを確認した。
- `log_rotate.sh` を大幅に改善し、7日以上経過したログファイルを自動削除する機能を実装した。
- `KEEP_DAYS` を環境変数で変更できるようにし、設定の柔軟性を向上させた。
- `mktemp` と `trap` を利用し、一時ファイルを安全に管理する処理を追加した。
- `find` に `-print0` を使用し、空白を含むファイル名でも安全に処理できるよう改善した。
- `rotate.log` と `cron.log` を削除対象から除外し、運用ログを残せるように変更した。
- 削除したログファイル数を集計し、処理結果をログへ記録するよう改善した。
- `backup.sh` の独自 `log()` 関数を廃止し、`lib/log.sh` の共通ログ関数へ移行した。
- ログをスクリプトごとに分ける設計へ変更し、各スクリプトが専用ログへ出力する構成に改善した。
- `cronjob.txt` をDocker環境用のパスへ修正し、今後運用できるよう実行スケジュールを整理した。

## 間違えたこと

- `log_rotate.sh` を `logs` ディレクトリ内に作成してしまい、ログディレクトリとスクリプト配置場所が混在した。
- `PROJECT_ROOT` の取得で `..` を付けてしまい、プロジェクトルートのパスがずれていた。
- `LOG_FILE` を設定する前に `lib/log.sh` を読み込み、`LOG_FILE is not set` のエラーが発生した。
- プロセス置換 (`done < <(...)`) で構文エラーが発生し、`mktemp` を利用した実装へ変更した。
- `deleted++` を使用した際に `set -e` の影響でスクリプトが終了したため、`((++deleted))` に修正した。
- `backup.sh` をMacで直接実行したため、`.env` が読み込まれず `WEBHOOK_URL is required` エラーが発生した。

## 学んだこと・覚えたこと

- Bashでも共通ライブラリを作ることで、複数のスクリプトで同じ処理を再利用できることを学んだ。
- ログは1つにまとめるより、用途ごとに分けた方が保守・障害調査がしやすいことを理解した。
- `trap` を利用すると、異常終了時でも一時ファイルの削除などの後処理を確実に実行できることを学んだ。
- `mktemp` を利用すると、安全に一時ファイルを作成できることを学んだ。
- `find -print0` と `read -d ''` を組み合わせることで、空白を含むファイル名でも安全に処理できることを学んだ。
- `LOG_FILE` を環境変数として渡すことで、同じログライブラリを複数のスクリプトで使い回せることを理解した。
- Dockerで動かすスクリプトとMacで直接実行するスクリプトでは、環境変数やパスの違いを意識する必要があることを学んだ。

## コマンド

000
mkdir -p lib
touch lib/log.sh
chmod +x lib/log.sh

touch test_log.sh
chmod +x test_log.sh

./test_log.sh
cat logs/test.log

touch logs/test.log
touch -t 202507010000 logs/test.log

./log_rotate.sh
cat logs/rotate.log
tail -5 logs/rotate.log

rm logs/rotate.log

grep -n "echo" backup.sh
grep -n "backup.log" backup.sh
grep -n 'log "' backup.sh

sed -n '20,40p' backup.sh

cat backup.sh
cat lib/log.sh
cat log_rotate.sh

./backup.sh
tail -20 logs/backup.log
000