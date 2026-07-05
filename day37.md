# Linux学習記録 Day37
# 復元システム強化・CLI運用設計・安全性改善
# 今日学んだこと
# 復元システムの安全設計と運用思想
- 復元処理は「実行」よりも「検証・保険設計」が重要
- スナップショットを取ることで復元失敗時の安全性が上がる
- ログ・履歴・JSON出力で操作をデータ化できる
- SHA256でデータ整合性チェックができる
- trapを使うとエラー時に自動復旧処理が可能
- LOCKファイルで多重実行を防げる
# 実践したこと
# restore.sh の大幅改善
- バックアップから index.html を安全に復元する仕組みを改良
- 復元前にスナップショットを自動保存
- diffで変更差分を確認してから復元実行
- rollbackファイルで復旧可能な設計に変更
```bash
cp -a "$LATEST" "$TARGET_FILE"
diff "$TARGET_FILE" "$LATEST"
```
# 安全性強化（復元システム）
- 不正パス（.. や ~）を拒否するチェック追加
- バックアップが古すぎる場合は復元禁止（7日制限）
- LOCKファイルで同時実行防止
- FORCEモードで手動確認スキップ可能
```bash
if [ -f "$LOCK_FILE" ]; then
  echo "Restore already running"
  exit 1
fi
```
# データ整合性チェック
- SHA256で復元後のデータ確認
- バックアップと復元後ファイルの一致検証
- 不一致なら自動ロールバック
```bash
sha256sum "$LATEST"
sha256sum "$TARGET_FILE"
```
# ログ・履歴管理強化
- 通常ログ（restore.log）
- 履歴ログ（restore_history.log）
- JSONログで機械的に扱える形式を追加
- カウント管理で復元回数を記録
```bash
echo "{\"time\":\"$(date +%s)\",\"file\":\"$(basename "$LATEST")\"}" >> log.json
```
# =========================
# 間違えたこと
# =========================
- 安全チェックの順番が最初は曖昧だった
- バックアップパス処理が未完成だった時期があった
- rollbackと復元の境界が不明確になりかけた
- diffやhashチェックの位置を調整する必要があった
- LOCK設計を入れないと並列実行で危険になることを理解した
# 覚えたコマンド

# ファイル検証・比較
`diff file1 file2`
`sha256sum file`
`stat file`

# 安全・制御系
`trap '...' ERR`
`touch lockfile`
`rm -f lockfile`

# ファイル操作
`cp -a file1 file2`
`ls -1t`
`head -1`

# Docker / 運用CLI
`ops_up`
`ops_down`
`ops_logs`
`ops_status`
# 学んだこと
- 復元システムは「壊れないこと」より「壊れても戻せること」が重要
- スクリプトは単体よりも“安全層の積み重ね”で設計する
- ログは人間用と機械用（JSONなど）を分けると強い
- CLI化すると運用のミスが減り、作業が標準化される
- エラー処理（trap）は実運用では必須レベルの仕組み
- 小さなツールでもSRE設計思想になる