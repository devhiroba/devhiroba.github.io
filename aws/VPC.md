# VPC
[https://aws.amazon.com/jp/vpc](https://aws.amazon.com/jp/vpc/?vpc-blogs.sort-by=item.additionalFields.createdDate&vpc-blogs.sort-order=desc)  
[https://docs.aws.amazon.com/ja_jp/vpc/](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## VPC とは
- Amazon Virtual Private Cloud  
- VPN のクラウドバージョン  
- AWS アカウント専用の仮想ネットワーク  
- 2019年の時点で VPC はすべてのアカウントに強制適用されている(VPC を利用しなければならない)  
- アカウント作成時に Default VPC が自動作成される

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## VPC の基本構成要素
アカウントを作成すると下記のリソースが生成されます。
- VPC
- Subnet
- Route Table
- Network ACL
- Security Group
- Internet Gateway
- DHCP options set

### VPC
- VPC 作成時に一番基本的なリソースです。  
- 名前と IPv4 CIDR ブロックで構成されます。  
- VPC の範囲内ですべての IP が割り当てされるとそれ以上リソースの作成はできません。  
- 一つの VPC で利用可能な IP の数は2^(32-16)=65536個です。
- インターネットに接続が必要な場合は必ずプライベートIPアドレスを利用しなければなりません。
また、インターネットに接続しなくてもプライベートIPアドレスを使用するようにしましょう。
VPC 内でプライベートIPアドレスの範囲以外のIP(インターネットで利用できるIP)を利用した場合、
VPC 内でそのIPに接続することができません。
- CIDR ブロックの指定は自由ですが、他の VPC と通信する時に CIDR が被るリソース間は通信ができません。

#### プライベートIPアドレスの範囲
クラスA：10.0.0.0～10.255.255.255 （10.0.0.0/8）  
クラスB：172.16.0.0～172.31.255.255 （172.16.0.0/12）  
クラスC：192.168.0.0～192.168.255.255 （192.168.0.0/16）

#### CIDR ブロックとは？
IP の範囲を指定する方式のことです。  
IP + / + ネットマスク、例)192.168.0.0/32  

### Subnet
- CIDRブロックを分割したネットワーク群のことです。
- VPC：Subnet は 1:N です。
- パブリックサブネットとプライベートサブネットがあります。
- サブネットのネットマスク範囲は 16(2^(32-16)=65536) ~ 28(2^(32-28)=16) です。
- 各サブネットは AZ を指定する必要があります。Subnet：AZ は 1：1 です。
- デフォルト VPC では AZ 数分のネットマスク20の Subnet が生成されます。

### Route Table
- サブネットはネットワーク上で通信する時にルートテーブルを利用して行き先を探します。
- 各サブネットに対してルートテーブルの作成が必要です。
- VPC 作成時に生成されて VPC にも繋がっています、VPC に属するサブネットを作成する時に基本ルートテーブルとして利用されます。
- 一つのルートテーブルは VPC に属する複数のサブネットで利用できます。
- インターネット又は他の VPC と通信するためにはルートテーブルにそのルールを追加する必要があります。
- 自動生成されるルートテーブルには「172.31.0.0/16範囲内のリソースは local で探す」というルールが定義されています。(172.31.0.0/16 local)

### Network ACL
- サブネットの前に位置して通信を制御する仮想のファイヤーウォールです。
- プロトコル・ポート・IPアドレスを登録してインバウンド・アウトバウンドの制御を行います。
- 一つの Network ACL は複数のサブネットで利用可能です。
- 外部 - IGW(VPC) - Router - Network ACL - Subnet - SG - EC2インスタンス

### Security Group
- インスタンスの前に位置して通信を制御する仮想のファイヤーウォールです。
- プロトコル・ポート・IPアドレスを登録してインバウンド・アウトバウンドの制御を行います。
- 一つの Security Group は複数のインスタンスで利用可能です。

### Internet Gateway
- VPC(Virtual Private Cloud)は名前通り、プライベートなクラウド環境なので VPC 内で作成されたリソースは基本的にインターネットに接続できません。
- インターネットに接続するためにはインターネットゲートウェイ（IGW）が必要です。
- IGWを作成して、サブネットに繋ぐことで、インターネットに接続できます。
ただ、この時インターネットを利用するリソースはパブリック IP が必要です。

### DHCP options set
- 「Dynamic Host Configuration Protocol」IPv4アドレス、 サブネットマスク、デフォルトゲートウェイ、DNSサーバのアドレスなどの通信用の基本的な設定を自動的に行うためのプロトコルです。
- 独自の DHCP 設定もできますが、基本的には VPC 作成時に生成されるものをそのまま利用します。 

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## VPC 概要図
[https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/VPC_Scenario2.html](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/VPC_Scenario2.html)  
