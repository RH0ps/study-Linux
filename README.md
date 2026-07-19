# 🐧 Linux学習記録 (linux-study)
高校1年生からLinux・Dockerなどのインフラ技術を学習しています。

Linuxの基礎操作から始め、Docker、Shell Script、自動化、監視などを実際に手を動かしながら学習し、毎日の学習内容を記録しています。

将来的にはSRE/DevOps領域で、システム運用・自動化・クラウド設計に関わる技術者を目指しています。

---

## 学習の目的
* Linuxの操作（ターミナル操作）に習熟する
* 基本的なコマンドを使いこなし、システムの仕組みを理解する
* 学んだことをドキュメントとして整理し、アウトプット力を高める
* 継続する力を身につける

## 学習方針
- 実際に環境を構築して理解する
- コマンドを覚えるだけではなく仕組みを理解する
- エラー発生時は原因を調査する
- 再現可能な手順として記録する
- 学んだ内容をコードやドキュメントとして残す

## 学習環境

- Host OS
  - macOS

- Container
  - Docker Desktop
  - Ubuntu Container

- Shell
  - Bash

- Version Control
  - Git
  - GitHub

- Editor
  - VSCode

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
### Linux
- 基本コマンド
- ファイル・ディレクトリ操作
- パーミッション
- ユーザー管理
- プロセス管理
### Shell Script / 自動化
- Bash Script作成
- cronによる定期実行
- バックアップ自動化
- ログ出力
- エラー処理
- 関数化
- 引数処理
### システム管理・監視
- CPU/Disk使用率監視
- 状態管理
- メトリクス出力
- 通知処理
- ログ解析
- 障害発生時の原因調査
### Git / 開発環境管理
- Gitによるバージョン管理
- GitHubへの学習記録保存
- リポジトリ構成整理
- Git操作トラブル対応
### トラブルシューティング
- エラーログ確認
- 原因調査
- 設定変更
- 再検証
- 復旧手順の記録

## 現在取り組んでいること
- Docker上でのLinuxサーバー運用
- Docker環境での運用設計
- Shell Scriptによる監視・自動化
- 障害発生時の原因調査と復旧手順の改善
- クラウド環境でのLinux運用に向けた基礎学習

## 関連プロジェクト
### ops-lab

Linux・Docker環境で構築している運用自動化プロジェクト。

学習した内容をもとに、バックアップ、復元、監視、CLIツールなどを実装しています。

詳細
[ops-lab](https://github.com/RH0ps/ops-lab)

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
* [Day30-1: cronによるバックアップ自動化とSlack通知改善](./day30-1.md)
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
* [Day41-1: ops-labのCLI化の進行](./day41-1.md)
* [Day41-2: シェル設定整理・起動時エラー調査](./day41-2.md)
* [Day42: ops-lab CLIの機能改善](./day42.md)
* [Day43: Docker環境改善とGitHub ActionsによるCI構築](./day43.md)
* [Day44: Restoreスクリプトの運用性向上と実機検証](./day44.md)
* [Day45: CPU Monitor のOS差分対応・Linux環境検証](./day45.md)
* [Day46: ログ管理の共通化とログローテーションの実装](./day46.md)
* [Day47: Health Check機能追加とCLI運用改善](./day47.md)
* [Day48: restore.shの完成とCPU監視スクリプトの改善](./day48.md)
* [Day49: スクリプト全体の品質改善](./day49.md)
* [Day50: Memory MonitorとProcess Monitorを実装して監視対象を拡充](./day50.md)
* [Day51: 監視ダッシュボードCLIの実装](./day51.md)



---

## 今後取り組む予定

今後はLinuxの基礎をさらに深めながら、実際のサーバー運用を意識した技術を段階的に学習していきます。

- Linuxネットワーク基礎
  - IPアドレス
  - ポート
  - DNS
  - HTTP / HTTPS
- SSHによるリモートサーバー操作
- Nginxの設定・運用
- systemdによるサービス管理
- ログ管理・解析
- システム監視機能の拡張
- AWSでのLinuxサーバー構築・運用
  - EC2
  - IAM
  - VPC
- GitHub ActionsによるCI/CD
- 障害発生時の原因調査・復旧手順の実践

今後も実際に環境を構築しながら、学習内容を継続的に記録・改善していきます。
