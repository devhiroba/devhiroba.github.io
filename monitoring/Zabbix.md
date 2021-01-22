# Zabbix
[https://www.zabbix.com/jp](https://www.zabbix.com/jp)

## Zabbixとは
ZabbixはITインフラストラクチャ・コンポーネントの可用性やパフォーマンスを監視するためのエンタープライス向けソフトウェアです。  

Zabbixはオープンソース・ソフトウェアとして開発されており、無料でダウンロードいただくことが可能です。  

---公式サイトから引用

## Zabbixの構成要素

・Server  
・Agent  
・Proxy  
・Frontend  

### Server
モニタリングを行う中核のプロセスです。  
監視対象サーバーに設置された Agent とやり取りして監視対象サーバーの情報を処理します。

### Agent
監視対象サーバーに設置されて、そのサーバーの稼働情報データを収集し、Zabbix Server に報告します。

### Proxy

### Frontend



## Zabbixの構造
![Image](/resource/image/zabbix-diagram.png)

### Zabbix Server

### Zabbix Agent

## Zabbixの通信方法（Active・Passive）
![Image](/resource/image/zabbix-active-passive-diagram.png)

## Zabbix構築
・CentOS 7
・Zabbix 2.2
・MySql

### 構築手順
１．AWS EC2 インスタンス作成（CuntOS 7）  
２．Zabbix インストール  
```
#Root権限に変更
sudo su -  

#zabbix設置ファイルをダウンロード  
#zabbix設置ファイル検索(https://repo.zabbix.com/zabbix/)
rpm -ivh https://repo.zabbix.com/zabbix/2.2/rhel/7/x86_64/zabbix-release-2.2-1.el7.noarch.rpm

#設置できるサービスを確認
yum search zabbix

#ZabbixサーバのZabbixサーバプロセスをインストールする(mysql用)
yum install zabbix-server-mysql -y

#Zabbixを操作するためのWebインターフェースをインストールする(mysql用)
yum install zabbix-web-mysql -y

#Webインターフェースでグラフ表示時の日本語の文字化けを防ぐ為のフォントの設定
yum install zabbix-web-japanese -y

#Zabbix Server自分も監視したい為、agent もインストールする
yum install zabbix-agent -y

#zabbix_agentからデータの取得をコマンドラインでできる
yum install zabbix-get -y

#DBサーバーのRPM取得
rpm -Uvh http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm

#確認
yum info mysql-community-server

#DBインストール
yum install mysql-community-server -y
```




