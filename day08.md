# Linux学習記録 Day8
## Docker・LinuxによるWebサーバー構築と自動化の実践
- **Webサーバーの起動**: Dockerを使用して軽量Webサーバー（Nginx）を起動。ローカル環境（localhost）で自作のHTMLファイルを正常に表示させた。
- **バックアップの自動化**: シェルスクリプト（backup.sh）を自作。コマンド一発でファイルに日付（タイムスタンプ）を自動付与し、別フォルダへコピー・退避させる仕組み（SREの基礎）を構築した。

## 覚えたコマンド
- index.html 一般的にウェブサイトのトップページとして使われるファイル名
- ! ＋ html(5) 文字化けしなくなるようにこの二つのどちらかを打ち込む
- < html lang="en"> を < html lang="ja"> に変更する
- <title>タイトル</title> クロームの場合タブの名前が変わる
- < body> この中に言葉を入れる
- <h 1>ここに打ち込む</h 1>
- backup.sh 一般的にLinuxなどでファイルの定期バックアップを自動化するときに使われるファイル名
## 自作した実際のコード
```bash
#!/bin/bash

# --- 設定項目 ---
# バックアップ対象のディレクトリ
TARGET_DIR="/Users/r.h/webusaitozidouka"
# バックアップ先のディレクトリ
BACKUP_DIR="/Users/r.h/backup"
# バックアップファイル名のプレフィックス
PREFIX="backup"
# 保存する日数
RETENTION_DAYS=7
# --- 設定項目ここまで ---

# --- 実行処理 ---
# バックアップ先のディレクトリが存在しなければ作成
mkdir -p "$BACKUP_DIR"
# 日数を取得
DATE=$(date "+%Y%m%d")
cp "$TARGET_DIR/index.html" "$BACKUP_DIR/index_$DATE.html"
```
## 自力でのDocker起動に成功
  - ネットから `docker run --name my-nginx -d -p 8080:80 -v $(pwd):/usr/share/nginx/html nginx` というコマンドを検索して組み立てた。
## 間違えたこと
  - 実行時にポート競合のエラー（`port is already allocated`）や名前の重複エラー
  - 原因 `docker stop` と `docker rm` を駆使して、裏で暴れていた古いコンテナ（`my-nginx` / `my-web-server`）を完全に停止・削除して環境をクリーンアップした。
  - 解決 再度コマンドを実行し、エラーなしでWebサーバーを完全自力で立ち上げ、ブラウザでの表示まで成功させた。


## 学んだこと
- 「裏側のサーバーを自動化で安全に守る（Ops/SRE）」というインフラ職の楽しさを、実際に手を動かすことで実感できた。
- ウェブ上は一行しか書いていなくても、裏側は何行も書くことを知れた.
