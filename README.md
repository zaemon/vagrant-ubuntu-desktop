Ubuntu 16.04 LTS GUI クライアント構築
======

VM生成起動
------

ローカルPCのコンソールで以下のコマンドを実行する.  
VMのスペックはかなりリッチにしてある. 必要に応じてスペックダウンすること.  
```
$ vagrant up --provision
  # かなり長い時間がかかる. 
  # 一度完了するが、この段階ではGUIはまだ起動していない.
$ vagrant reload --provision
  # GUI起動
```


ログイン
-------

GUIが起動しているはずなので、ユーザー"vagrant"に対してパスワード"vagrant"でログインする.  
コンソールも"vagrant ssh"でログインできるのでお好みで.  


Docker
------

sudoを使わずにdockerコマンドを使えるようにした.  
以下ユーザーで使用可能.  
- ユーザー: vagrant (グループ: docker)


日本語化
------

起動時のプロビジョニングによって必要な日本語パッケージはインストール済み.  
ログイン後、以下の設定を実施する.  
- System Settings -> Language Support
  - インストールを求められたらインストールする
  - Language for menus and windows: のリストの一番下にある"日本語"を一番上にドラッグする
  - Apply System-Wide ボタンを押す
  - Keyboard input method system: がfcitxになっていることを確認する
- ログアウト-ログインする
  - 再度ログインするとき、標準フォルダー名を日本語フォルダー名にするか聞かれるので任意に選択する
- メニューバーのキーボードアイコン(fcitxアイコン) -> 現在の入力メソッドの設定
  - "入力メソッド"タブ -> "+"ボタンで"Mozc"を追加
    - 日本語キーボードの場合、"キーボード - 日本語"も追加し、"キーボード - 英語(US)"を削除する
- 端末なりテキストエディタなりを起動して"Ctrl-space"で入力ソースが切り替わるか確認する
- 端末で"mozc"コマンドを実行し、Mozcプロパティを編集する(必要に応じて)

### US-Mozc切り替えをAlt+`で実現する

"Alt-`"はキーボードショートカットがすでに割り当てられていて、そのままでは入力ソース切り替えに使用できない。  
以下で既存のキーボードショートカットを無効かする。  

- メニューバーのキーボードアイコン(fcitxアイコン) -> 現在の入力メソッドの設定
  - "全体の設定"タブ -> "入力メソッドのオンオフ"
    - "Ctrl-space" をクリックし、変更を促すウィンドウが出るので "Alt+`" を入力する
- Dashから"compiz"を入力し、"CompizConfig Settings Manager"("CompizConfig 設定マネージャ"の場合も)を起動
  - "デスクトップ" -> "Ubuntu Unity Plugin"
  - "Switcher" タブ
  - "Key to flip through windows in the Switcher"の"無効"(デフォルト)をクリック  
    "無効"と言いつつ有効で、"Alt-`"が割り当てられている
  - "有効"にチェックを入れて"キーの組み合わせをつかむ" -> 何か入力する(ex. "Alt-Esc") -> OK で有効化
  - 入力したキーストロークをクリック -> "有効"のチェックを外す -> OK  
    明示的に無効化される
- "Alt-`"で入力ソースが切り替わることを確認
  - タスク切り替えウィンドウが出ないことを確認(ConpizConfigで設定した効果)
  - Altを押す時間が短いとDashが起動する場合がある。Alt (1秒) `のように入力すると確実に切り替わる


ホストOSとのファイル共有
------

Vagrantのsynced_folderに悩むのに疲れ、Ubuntu側からマウントすることにした.  
ホストOSがWindowsでもmacOSでもSMB共有を使う.  
当然、ホストOS側のファイル共有を有効化しておくこと.  
以下の"server"はホストOSのホスト名、"shareddir"は共有ディレクトリー名.  

### コマンド

以下のコマンドでマウントできる.  
uidやgidを指定しないと、root:rootでのマウントとなり、vagrantユーザーでの更新ができないので注意.  
```
$ mkdir /mountpoint
$ sudo mount.cifs //server/shareddir /mountpoint -o user=userofserver,uid=vagrant,gid=docker
```

### /etc/fstab

/etc/fstabに以下のように書くことで起動時マウントが可能.  
(マウントポイントのディレクトリーはあらかじめ作成しておくこと)
```
//server/shareddir /mountpoint cifs user=userofserver,password=serverpassword,uid=vagrant,gid=docker 0 0
```
書いたfstabを使ってその場でマウントするには以下のコマンドを実行する。
```
$ sudo mount -a
```

### gitリポジトリー配置場所として使用する場合の問題

macOSでは、gitで共有ディレクトリー上のリポジトリーを扱う際、エラーが出力される.  
(通常の読み書きは問題ない)  
原因及び回避方法模索中.  
Windowsでは発生しない.  

