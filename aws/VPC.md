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

### プライベートIPアドレスの範囲
クラスA：10.0.0.0～10.255.255.255 （10.0.0.0/8）  
クラスB：172.16.0.0～172.31.255.255 （172.16.0.0/12）  
クラスC：192.168.0.0～192.168.255.255 （192.168.0.0/16）

#### CIDR ブロックとは？
IP の範囲を指定する方式のことです。  
IP + / + ネットマスク、例)192.168.0.0/32  

### Subnet





