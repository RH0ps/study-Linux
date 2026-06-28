# Linux学習記録 Day26  
# Linuxユーザー管理・Docker環境での実践
## 今日学んだこと
**Linuxユーザー管理とホームディレクトリの仕組み**
- LinuxユーザーはUIDで管理される  
- `/etc/passwd` にユーザー情報が保存される  
- `su` と `su -` ではログイン環境の初期化範囲が異なる  
- ホームディレクトリはユーザーごとの作業領域として機能する  
- Dockerを使うことでMac上でもLinuxサーバー環境を再現できる  
## 実践したこと
### Docker環境の準備とLinux環境構築
- Macでは `useradd` が使用できないためDocker Ubuntu環境を利用  
- `docker run -it ubuntu bash` でコンテナ起動  
- `apt update` / `apt install passwd` で環境構築  
```bash
apt update
apt install passwd
```
### ユーザー作成と確認
- `useradd testuser` でユーザー作成  
- `passwd testuser` でパスワード設定  
- `/etc/passwd` にユーザー情報が追加されていることを確認  
```bash
useradd testuser
passwd testuser
cat /etc/passwd | grep testuser
```
### ユーザー切り替えとホームディレクトリ
- `su - testuser` でログインシェルとして切り替え  
- `/home/testuser` に移動されることを確認  
- 初期ファイル（.bashrc / .profile）の存在を確認  
```bash
su - testuser
pwd
ls -la
```
### rootと一般ユーザーの違い
- rootユーザーは全ディレクトリにアクセス可能  
- 一般ユーザーは権限によって操作が制限される  
- 所有者とパーミッションによって挙動が変わる  
## 間違えたこと・ハマった点
- Macで `useradd` を実行してエラー（Linux専用コマンド）  
- `/ctc/passwd` と入力してパスミス（正しくは `/etc/passwd`）  
- `passwd` 入力ミスでエラーが発生  
- `su` でホームディレクトリが存在せず移動できない状態になった  
- `useradd` に `-m` を付けずホームが作成されなかった  
## 覚えたコマンド
### ユーザー管理
```bash
useradd testuser      # ユーザー作成
passwd testuser       # パスワード設定
userdel -r testuser   # ユーザー削除（ホームごと削除）
```
### ユーザー確認
```bash
id testuser           # UID / GID確認
cat /etc/passwd       # ユーザー情報一覧
```
### ユーザー切り替え
```bash
su testuser           # ユーザー切り替え（環境そのまま）
su - testuser         # ログインシェルで切り替え（環境初期化）
```
### ファイル操作
```bash
pwd                   # 現在いるディレクトリを表示
ls -l                 # ファイル・ディレクトリを詳細表示
ls -la                # 隠しファイルも含めて一覧表示
```
### Docker環境
```bash
docker run -it ubuntu bash   # Ubuntuコンテナを起動して中に入る
apt update                   # パッケージ一覧を更新
apt install passwd           # パスワード管理コマンドをインストール
```
## 学んだこと
- LinuxユーザーはUIDで識別される  
- `/etc/passwd` がユーザー管理の基盤ファイル  
- ホームディレクトリはユーザーごとの作業領域  
- `su` と `su -` は環境の初期化範囲が異なる  
- `useradd` はオプションなしだと不完全なユーザーになる場合がある  
- Dockerを使うことでLinux環境を再現できる  