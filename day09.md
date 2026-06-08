# Linux学習記録 Day9
## 覚えたコマンド
- bashdocker start コンテナ名 コンテナを起動させるだけのコマンド
- docker restart コンテナ名 特定のサーバー再起動
- crontab -e タイマーのスケジュールを書き込むための専用の編集画面が開く
- escキー を1回押す :wq を入力で保存して終了
- 0 9 * * * /Users/r.h/webusaitozidouka/backup.sh 0分、9時、毎日、毎月、毎曜日になった瞬間に、後ろに書いてある場所の backup.sh を自動で実行
## 間違えたこと
- crontab -e を閉じるときにcontrolキー＋oで閉じようとした
- 解決 escキー を1回押す :wq を入力
