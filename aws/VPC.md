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
VPC 作成時に一番基本的なリソースです。  
名前と IPv4 CIDR ブロックで構成されます。  
VPC の範囲内ですべての IP が割り当てされるとそれ以上リソースの作成はできません。  
一つの VPC で利用可能な IP の数は2^(32-16)=65536個です。  

#### CIDR ブロックとは？
IP の範囲を指定する方式のことです。  
IP + / + ネットマスク、例)192.168.0.0/32  




