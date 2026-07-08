# Linux学習記録 Day40
# ops_check_allによるセルフヘルスチェック機能の実装
## 学習内容

運用CLIへシステム全体の状態を一括で確認できる`ops_check_all`コマンドを追加。

いままではコンテナの状態やHealth Check、バックアップ、ディスク監視などを個別のコマンドで確認していたが、1つのコマンドですべて確認できるよう改善。

## 実装内容

- `ops_check_all`コマンドを新規作成
- コンテナの起動状態を確認
- Health Checkの状態を確認
- バックアップスクリプトを実行
- ディスク監視スクリプトを実行
- `restore.sh`の存在確認
- `backup`ディレクトリの存在確認
- `metrics`ディレクトリの存在確認
- `logs`ディレクトリの存在確認
- PASS / FAILの件数を集計
- 最終的に`System Status : HEALTHY`または`System Status : FAILED`を表示するサマリーを追加
- `ops_help`へ`ops_check_all`の説明を追加

## 発生した問題

`ops_check_all`を追加した直後は、

000
ops_check_all
000

を実行しても

000
zsh: command not found: ops_check_all
000

と表示され、コマンドを実行できませんでした。

原因を調査するため、

- `type`
- `BASH_SOURCE`
- `CLI_DIR`

を確認し、`ops_cli.sh`が正しく読み込まれているかを検証しました。

最終的に

000
source ./ops_cli.sh
000

でCLIを読み込み直したところ、関数が正常に登録され、`ops_check_all`を実行できることを確認しました。

## 学んだこと

今回の作業を通して、シェル関数はファイルを編集・保存しただけでは利用できず、現在のシェルへ`source`で読み込む必要があることを理解しました。

また、

- シェル関数の読み込みの仕組み
- `source`の役割
- `type`による関数の確認方法
- `BASH_SOURCE`を利用したスクリプトの位置取得
- エラー発生時の原因調査と切り分け

について学びました。

## 動作確認

`ops_check_all`を実行し、以下の項目を正常に確認できることを確認しました。

- Container
- Health
- Backup
- Disk Monitor
- Restore Script
- Backup Directory
- Metrics Directory
- Logs Directory

最終的に
000
===== Container =====
NAME      IMAGE             COMMAND                   SERVICE   CREATED      STATUS                PORTS
ops-lab   ops-lab-ops-lab   "/home/r.h/docker/en…"   ops-lab   2 days ago   Up 2 days (healthy)   

===== Health =====
healthy

===== Backup =====
2026-07-08 21:09:13 Backup created: index_20260708_210913.html
2026-07-08 21:09:13 Rotation completed (kept 10 backups)
2026-07-08 21:09:13 Backup success: index_20260708_210913.html

===== Disk Monitor =====

===== Restore Script =====
OK - restore.sh exists

===== Backup Directory =====
OK - backup directory exists

===== Metrics Directory =====
OK - metrics directory exists

===== Logs Directory =====
OK - logs directory exists

===== Summary =====
PASS : 4
FAIL : 0

System Status : HEALTHY
000
と表示され、すべて正常に動作することを確認しました。

## 振り返り

今回は新しい運用コマンドを追加するだけでなく、コマンドが認識されない原因を調査しながら修正する経験ができました。

これまでは複数のコマンドを実行して個別に状態を確認していましたが、`ops_check_all`を実装したことで、システム全体の状態を1回の実行で確認できるようになりました。

また、機能を実装するだけでなく、問題発生時に原因を調査し、修正して動作確認まで行う一連の流れを経験できたことも大きな学びとなりました。
