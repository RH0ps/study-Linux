# 🐧 Linux学習記録 (linux-study)
高校1年生からLinux・Dockerなどのインフラ技術を学習し、将来的にSRE/DevOps・クラウドアーキテクト設計職を目指しています

---

## 学習の目的
* Linuxの操作（ターミナル操作）に習熟する
* 基本的なコマンドを使いこなし、システムの仕組みを理解する
* 学んだことをドキュメントとして整理し、アウトプット力を高める
* 継続する力を身につける
## 学習環境
- OS
  - macOS
- 仮想環境
  - Docker Desktop
  - Ubuntu Container
- Tools
  - VSCode
  - Git/GitHub
## 実践した内容
### Docker
- Dockerfileによる環境構築
  - 環境をコード化し再現可能な構築を実践
- Volumeを利用したデータ永続化
  - コンテナ削除後もデータを保持する仕組みを理解
- コンテナの状態確認・トラブルシューティング
- Ubuntuコンテナ作成
- Nginx Webサーバー構築
  - コンテナ上でWebサービスを動作確認
- コンテナ自動再起動設定
### 自動化
- Shell Script作成
- cronによる定期実行
- バックアップ自動化
- ログ出力・エラー処理追加
### 監視
- dfコマンドを利用したディスク使用量監視
- awk/grepによるログ解析
- Shell Script + curlによる通知処理
- 障害発生時の原因調査・復旧確認
## 現在取り組んでいること
- Linuxサーバー運用の実践
- Dockerコンテナの運用・管理方法の学習
- 障害発生時の原因調査・エラー対応
  - Dockerコンテナの状態確認
  - ログ確認
  - 権限エラーの原因調査
  - 設定変更による動作確認

## 日々の学習ノート
各日付をクリックすると、その日の具体的な学習内容ページに移動します。

* [Day01: Linux学習のスタート](./day01.md)
* [Day02: 基本コマンドの習得](./day02.md)
* [Day03: 基本コマンドの習得と応用](./day03.md)
* [Day04: 基本コマンドの習得と応用](./day04.md)
* [Day05: 今までの復習](./day05.md)
* [Day06: 基本コマンドの習得](./day06.md)
* [Day07: dockerとHomebrewとVSCodeの導入](./day07.md)
* [Day08: Docker・LinuxによるWebサーバー構築と自動化の実践](./day08.md)
* [Day09: 自動化コマンドの習得](./day09.md)
* [Day10: 基本コマンドの習得](./day10.md)
* [Day11: Linuxのパーミッション（権限）の数値管理の実験](./day11.md)
* [Day12: 基本コマンドの習得](./day12.md)
* [Day13: 容量確認コマンドの習得](./day13.md)
* [Day14: シェルスクリプトとcronによるディスク容量自動監視システムの構築](./day14.md)
* [Day15: ログ解析とデータ自動集計コマンドの習得](./day15.md)
* [Day16: アクセスログ解析とトップアクティブIPの自動特定](./day16.md)
* [Day17: 複数スクリプトのCron同時運用における連鎖停止障害のトラブルシューティング](./day17.md)
* [Day18: Dockerでバックアップ自動化環境を構築](./day18.md)
* [Day19: バックアップ自動化システム改善：時刻付きバックアップ＋ログ機能追加](./day19.md)
* [Day20: Docker起動時にcron自動起動＋定期バックアップ自動化](./day20.md)
* [Day21: バックアップ不要ファイル自動削除＋Docker操作コマンド短縮](./day21.md)
* [Day22: Docker自動復旧設定＋バックアップ失敗時のエラー処理追加](./day22.md)
* [Day23: バックアップログ解析＋状態確認スクリプト作成](./day23.md)
* [Day24: Gitによる学習記録管理の導入＋GitHub同期トラブル解決](./day24.md)
* [Day25: Git履歴・Docker JST化](./day25.md)
* [Day26: Linuxユーザー管理・Docker環境での実践](./day26.md)
* [Day27-1: Linuxパーミッション・ユーザー管理・sudo設定](./day27-1.md)
* [Day27-2: Docker監視システム構築（Slack通知 + cron + 状態管理）](./day27-2.md)
* [Day28: Docker環境の再構築とdocker-compose導入](./day28.md)
* [Day29: ディスク使用量監視・Slack通知システム構築](./day29.md)
* [Day30-1: ronによるバックアップ自動化とSlack通知改善](./day30-1.md)
* [Day30-2: ディスク監視システムの状態管理化とSlack通知の最適化](./day30-2.md)
* [Day31-1: Gitリポジトリ再構築とプロジェクト構造整理（過去最大の環境トラブル解決）](./day31-1.md)
* [Day31-2: Docker + cron + ops-lab 再構築と運用設計の改善](./day31-2.md)
* [Day32: ops-lab CLI統合とアーキテクチャ再設計](./day32.md)
* [Day33: ops-lab監視システム機能改善・本番環境移行・Docker構成修正・動作確認](./day33.md)
* [Day34: ops-lab プロジェクト構成の整理と Docker ボリューム管理](./day34.md)
* [Day35: ops-lab：監視設計（logs / metrics / state）とPrometheus形式の理解](./day35.md)
* [Day36: Docker環境の修正とバックアップシステムの改善](./day36.md)
* [Day37: 復元システム強化・CLI運用設計・安全性改善](./day37.md)
* [Day38: restore.shの改善と安全な復元処理](./day38.md)
* [Day39: ops_cli.shのリファクタリングとモジュール化](./day39.md)
* [Day40: ops_check_allによるセルフヘルスチェック機能の実装](./day40.md)
* [Day41-1: ops-labのCLI化の進行](./day40-1.md)
* [Day41-2: シェル設定整理・起動時エラー調査](./day40-2.md)



## 今後取り組む予定
- Linuxネットワーク基礎
  - IPドレス
  - ポート
  - DNS
  - HTTP/HTTPS
- SSHによるリモートサーバー操作
- systemdによるサービス管理
- Nginx設定変更
- AWS環境でLinuxサーバー構築
- GitHub ActionsによるCI/CD
- 監視・障害対応の実践

---

## 実践で意識していること
- ただコマンドを覚えるだけではなく、実際に環境を作成して動作確認する
- エラーが発生した場合は原因を調査し、修正方法を記録する
- 作成した環境を再現できるように設定や手順をドキュメント化する
