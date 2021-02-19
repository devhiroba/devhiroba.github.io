# AWS用語

## AWS とは
Amazon Web Services  
Amazonにより提供されているクラウドコンピューティングサービスのこと

## AWS サービス領域に関する用語
### Region
> AWS サービスが提供されるサーバーの物理的な領域のこと  
> 日本にも東京リージョンがある
> 
> 耐障害性(安全性)  
> 世界を複数のリージョンに分離してリージョンごとにサービスを提供している  
> リージョン毎に完全に分離されているため、他のリージョンで障害が発生しても影響を受けない
> 
> 速度UP  
> サービスを提供する地域と近いリージョンを利用することによってより早い速度が期待できる  
> 日本向けのサービスはアメリカのバージニア北部より東京リージョンの方が早いでしょう

### Availability Zone - AZ
> リージョンの中にあるデータセンタを意味する  
> 基本的に各リージョンの中には二つ以上のAZを持っている  
> 同じリージョン内のAZでもそれぞれの物理的な場所は離れている  
> 同じリージョン内の各AZは専用線(Direct connect)で繋がっているためタイムラグはほぼない  

### Edge Location
> AWSのCDNサービスであるCloud Frontのキャッシュサーバがある場所を意味する

## Computing
### EC2
> Elastic Compute Cloud  
> AWS上のクラウド環境で利用できる仮想サーバーのこと  
> インスタンス単位でサーバーを構築して利用できる  

### ECS
> EC2 Container Service  
> コンテナオーケストレーションサービス  
> EC2とFargate上で運用するDockerコンテナをコントロールできる  

### EKS
> Elastic Kubernetes Service  
> AWS で Kubernetes を簡単に実行できるマネージド型の Kubernetes サービス  
> Kubernetes とは Docker コンテナオーケストレーションソリューション

### EB
> Elastic Beanstalk
> EC2、LB、DB、AutoScallingなどインフラの構築を自動でやってくれるサービス
> Elastic beanstalk でサービス可能なインフラ環境を構築してサービスするアプリケーションをアップロードするだけ

### Lambda
> サーバーレスコンピューティングサービス
> サーバーレスなのでサーバーを管理する必要ない

### Auto Scaling
> リソースの容量を自動で調整してくれるサービス
> 例えば、EC2 の場合インスタンスの数を自動で増減できる

### AMI
> Amazon Machine Images  
> Amazon マシンイメージ (AMI) には、インスタンスの起動に必要な情報が用意されている  
> インスタンスを起動するときは、AMI を指定する必要がある  
> 同じ設定で複数のインスタンスが必要な場合は、1 つの AMI から複数のインスタンスが起動できる

## Storage
### S3
> Simple Storage Service
> クラウド型のオブジェクトストレージサービス

### Glacier
> クラウド型のオブジェクトストレージサービス
> S3 の中で長期保存向けのサービス

### EBS
> Elastic Block Store  
> Amazon EC2インスタンスに接続できるブロックレベルのストレージ  
> 簡単に言うとハードディスクみたいなもの  

## Database
### DynamoDB
> NoSQL
> 

### RDS
Relational Database Service
### ElastiCache
key,valueデータストア

## Networking
### CloudFront
a
### Route 53
a
### GatewayAPI
a
### VPC
Amazon Virtual Private Cloud

### Security Group - SG
a

## Management Tools
### CloudWatch
a
### CloudFormation
a
### AWS Config
毎日JSON形式でサーバーの設定情報をS3に保存する。

### GuardDuty

### AWS KMS
AWS Key Management Service


### Amazon Pinpoint
AWSによるカスタマーエンゲージメントプラットフォーム  
メール配信システムとしても利用可能  
Amplify Framework(SDK)を利用してデータの取得が可能?

### QuickSight
AWSで利用した料金の可視化
