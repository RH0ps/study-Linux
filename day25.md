# Linux学習記録 Day25
## 今日学んだこと
 **Gitの変更管理と履歴追跡の実践**
   - ファイル修正 → 差分確認 → 過去バージョン参照 → GitHub反映までの一連フローを理解
**Dockerコンテナのタイムゾーン（JST）統一**
   - UTC表示のコンテナをデバッグし、日本時間（JST）へ統一する方法を実践
##  実践したこと
###  Gitの変更管理（差分・履歴・同期）
- `~/study/github` にて `day11.md` を修正（nano使用）
- `git status` で作業ツリーの変更状態を確認
- `git diff` により変更内容が行単位ではなく変更ブロック単位で表示されることを確認

### 過去履歴の参照
- `git log` でコミット履歴を確認し、Day24時点のコミット `083001c` を特定
- `git show 083001c:day11.md` を実行し、
  過去コミット時点のファイル内容を**作業ツリーを変更せず読み取り専用で参照**

### GitHub同期
- ローカルが `ahead of origin/main by 1 commit` 状態であることを確認
- `git push` を実行し、ローカル変更をGitHubへ反映（Day25状態へ同期完了）
### 2. Dockerコンテナのタイムゾーン（JST化）

### 状態確認
- コンテナ内で `date` を実行しUTC表示であることを確認
- `echo $TZ` で環境変数の未設定を確認

### 実施した修正
- `/etc/localtime` を Asia/Tokyo にシンボリックリンク設定
- `/etc/timezone` を `Asia/Tokyo` に変更
- `tzdata` をインストール
- `dpkg-reconfigure -f noninteractive tzdata` で再設定を反映

### 結果
- `date` 出力が UTC → JST に変更されたことを確認
- ただし、どの設定（localtime / TZ / tzdata）が最終的な決定要因かは未分離（複合要因の可能性あり）
##  覚えたコマンド

- `git status` 作業ツリーの変更状態を確認する
- `git diff` コミット前の変更差分をブロック単位で表示
- `git log` コミット履歴を確認する
- `git show <commit>:<file>` 過去コミット時点のファイル内容を参照（読み取り専用）
- `git push` ローカルのコミットをGitHubへ反映
- `ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime` タイムゾーンを日本時間へリンク設定
- `echo "Asia/Tokyo" > /etc/timezone` システムタイムゾーン設定を書き換え
- `apt-get update && apt-get install -y tzdata` タイムゾーンデータを含むパッケージ更新
- `dpkg-reconfigure -f noninteractive tzdata` 非対話モードでタイムゾーン設定を再構成
## 間違えたこと・改善点

- Dockerfile命令（ENV / RUN）をコンテナ内で実行しエラー
  - → Dockerfileと実行環境の区別不足
- `ln -sf` 単体でタイムゾーンが完全に切り替わると誤解
  - → tzdataや/etc/timezoneとの関係理解が不足
- `git show` を「状態切り替え」と誤認
  - → 実際は読み取り専用の参照操作
## 学んだこと
- Gitは「変更履歴の積み重ね」であり、過去状態は削除されず共存している
- `git show` により過去のスナップショットを安全に参照できる
- `git diff` は単純な行比較ではなく変更ブロック単位で管理されている
- Dockerでは実行中コンテナとDockerfileビルド命令は別概念
- タイムゾーン変更は単一設定ではなく複数設定の組み合わせで決定される可能性がある
## 次にしたいこと
- Gitブランチ操作（branch / merge）による並行開発の理解
- cronジョブの実行時間とログをJST統一する実運用構築
- GitとLinux環境を組み合わせた「実務レベルの運用フロー」へ拡張
