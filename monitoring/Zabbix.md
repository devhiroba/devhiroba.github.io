{:.bg-gray-dark.text-white.p-5}
# Zabbix
[https://www.zabbix.com/jp](https://www.zabbix.com/jp)

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbixとは（公式サイトから引用）

ZabbixはITインフラストラクチャ・コンポーネントの可用性やパフォーマンスを監視するためのエンタープライス向けソフトウェアです。Zabbixはオープンソース・ソフトウェアとして開発されており、無料でダウンロードいただくことが可能です。  

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbixの構成要素

### Server
モニタリングを行う中核のプロセスです。  
監視対象サーバーに設置された Agent とやり取りして監視対象サーバーの情報を処理します。

### Agent
監視対象サーバーに設置されて、そのサーバーの稼働情報データを収集し、Zabbix Server に報告します。

### Proxy
Zabbix Server の代わりにモニタリング対象サーバーからデータを収集して Zabbix Server に転送するプロセスです。   
Proxy の利用可否は任意です。  

### Frontend
Zabbix 設定・監視を行う Web インターフェースです。

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbixの構造
### Proxy利用しない
---
![Image](/resource/image/zabbix-diagram.png)
### proxy利用
---
![Image](/resource/image/zabbix-diagram2.png)

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbixの通信方法（Active・Passive）
デフォルトは Passive 方式です。  
  
![Image](/resource/image/zabbix-active-passive-diagram.png)

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbix Server 構築
※ AWS EC2 インスタンス（CuntOS 7）は作成前提で Zabbix Server をインストールします。  

- CentOS 7  
- Zabbix 2.2  
- MySql 5.7.33  

### Zabbix Server インストール
---
・Root権限に変更
```
sudo su -  
```
・zabbix設置ファイルをダウンロード  
・zabbix設置ファイル検索(https://repo.zabbix.com/zabbix/)
```
rpm -ivh https://repo.zabbix.com/zabbix/2.2/rhel/7/x86_64/zabbix-release-2.2-1.el7.noarch.rpm
```
・設置できるサービスを確認
```
yum search zabbix
```

・ZabbixサーバのZabbixサーバプロセスをインストールする(mysql用)
```
yum install zabbix-server-mysql -y
```
・Zabbixを操作するためのWebインターフェースをインストールする(mysql用)
```
yum install zabbix-web-mysql -y
```
・Webインターフェースでグラフ表示時の日本語の文字化けを防ぐ為のフォントの設定
```
yum install zabbix-web-japanese -y
```
・Zabbix Server自分も監視したい為、agent もインストールする
```
yum install zabbix-agent -y
```
・zabbix_agentからデータの取得をコマンドラインでできる
```
yum install zabbix-get -y
```

### MySQL インストール
---
・DBサーバーのRPM取得
```
rpm -Uvh http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
```
・確認
```
yum info mysql-community-server
```
・DBインストール
```
yum install mysql-community-server -y
```
・DBバージョン確認
```
mysql --version
```
・DB起動
```
systemctl start mysqld
```
・OS起動時に自動的に起動するように設定
```
systemctl enable mysqld
```
・Mysqlの初期パスワードを確認する
```
cat /var/log/mysqld.log | grep password
```
・初期設定をする
```
mysql_secure_installation
```
・変更後のPWでログイン確認
```
mysql -u root -p
```
・Zabbix用DB作成
```
create database zabbix character set utf8 collate utf8_bin;
```
・Zabbix用DBアカウント作成（アカウント：zabbix、パスワード：zabbix%2jaDIQAEWpV）
```
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix%2jaDIQAEWpV';
```
・データ作成
```
mysql -uroot -p zabbix < /usr/share/doc/zabbix-server-mysql-2.2.23/create/schema.sql
mysql -uroot -p zabbix < /usr/share/doc/zabbix-server-mysql-2.2.23/create/images.sql
mysql -uroot -p zabbix < /usr/share/doc/zabbix-server-mysql-2.2.23/create/data.sql
```

### Zabbix 定義ファイルの修正
---
・Zabbixサーバーの設定ファイルにDBパスワードを設定する
```
vi /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix%2jaDIQAEWpV
```
・Zabbixの設定ファイルにtimezone設定をする
```
vi /etc/httpd/conf.d/zabbix.conf
php_value date.timezone Asia/Tokyo
```
・firewalldコマンドが実行できない場合のみ実行（firewall設定をしない場合は下記のコマンドは不要）
```
yum install firewalld
systemctl unmask firewalld
systemctl enable firewalld
systemctl start firewalld
```
・firewalldの設定（firewall設定をしない場合は下記のコマンドは不要）
```
firewall-cmd --add-port=10051/tcp --zone=public --permanent
firewall-cmd --add-service=http --zone=public --permanent
firewall-cmd --add-port=10050/tcp --zone=public --permanent
firewall-cmd --reload
```
・firewallの停止（IP制限をAWSのSGに任せてfirewallは停止する場合）
```
#firewalld自動起動設定確認
systemctl is-enabled firewalld

#iptables自動起動設定確認
systemctl is-enabled iptables

#firewalld停止
systemctl stop firewalld

#firewalld停止確認
systemctl status firewalld

#firewalld自動起動停止
systemctl disable firewalld

#firewalld自動起動設定確認
systemctl is-enabled firewalld
```
・Zabbixサーバー起動
```
systemctl start zabbix-server
```
・Apach起動
```
systemctl start httpd
```

### 接続確認  
---
http://IPアドレス/zabbix/  
or  
http://AWSのDNS/zabbix/  

・初期アカウント  
ID：Admin  
PW：zabbix  

※Zabbix Serverが起動できない場合はSELINUXを確認しよう！  
・SELINUXが実行中か確認
```
 sestatus
```
・SELINUXを一時的に無効化
```
setenforce 0
```
・SELINUXを永続的に無効化
```
vi /etc/selinux/config
SELINUX=disabled
```
・SELINUXの設定を反映させるためにはサーバー再起動が必要
```
reboot
```

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Zabbix Agent 設置
### Zabbix Agent インストール  
---
・Root権限に変更
```
sudo su -  
```
・zabbix設置ファイルをダウンロード  
・zabbix設置ファイル検索(https://repo.zabbix.com/zabbix/)
```
rpm -Uvh https://repo.zabbix.com/zabbix/2.2/rhel/7/x86_64/zabbix-release-2.2-1.el7.noarch.rpm
```
・zabbix agent設置
```
yum -y install zabbix zabbix-agent
```
・zabbix_agentd.conf編集(サーバーとの通信はPassive方式)
```
vi /etc/zabbix/zabbix_agentd.conf
Server=111.222.333.444(Zabbix Server IP)
Hostname=centospassive
```
・zabbix agentの起動
```
systemctl enable zabbix-agent
systemctl start zabbix-agent
```

### 監視対象サーバーの登録 
---
・ホストグループを作成する。  
```
設定＞ホストグループ＞ホストグループの作成  
```
![Image](/resource/image/zabbix_web.png)
・ホストを作成する。  
```
設定＞ホスト＞ホストの作成  
```
![Image](/resource/image/zabbix_web2.png)
![Image](/resource/image/zabbix_web3.png)
