> # NerveNet インストール方法
{{>toc}}
## debian インストール
CLIもしくはGUIインストーラを用いて最低限のものを入れる
　→ ここは実際に後でインストールしつつ確認しながら埋めます！


## sudo インストール、ユーザ設定
~~~
su
　[input password]

apt update
apt install sudo
cd /etc/
vi sudoers
~~~

[/etc/sudoers]
~~~
# User privilege specification
root    ALL=(ALL:ALL) ALL
↓追加
nict    ALL=(ALL:ALL) ALL
~~~


## docker インストール
下記のページ内のインストール手順が詳しい
https://matsuand.github.io/docs.docker.jp.onthefly/engine/install/debian/
~~~
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg2
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce
sudo usermod -aG docker $USER
docker --version
~~~


## NerveNet Docker インストール
Owncloudの所定の場所から最新のNervenet-Installerを取得する
（URLは要確認）
~~~
tar zxfv <nervenet-installer最新バージョン>
sudo apt-get install php8.2
./install.sh -o wifi
~~~
インストールにphp8.2が必要（バージョン指定あり？）
WiFi機能の有効化オプションをつけてインストールする場合、-o wifiでオプション指定

http://\<nervenet node ipaddr>:10700/bsconfig/ja/
にブラウザでアクセス
user:admin
password:password
nervenet管理画面にログインし各種設定の閲覧や変更が可能

---

不要な常駐モジュールの停止などが必要な場合
例えば自己産出エッジクラウド機能をアペンドする際は内部で専用のWebサーバが立ち上がるため、NerveNet常駐のApacheを下記のように停止する必要がある
~~~
# systemctl -stop apache2
# systemctl -disable apache2
~~~
 