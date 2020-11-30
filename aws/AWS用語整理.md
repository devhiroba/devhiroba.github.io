# AWSとは
Amazon Web Services
Amazonにより提供されているクラウドコンピューティングサービスのこと

## AWSサービス領域に関する用語
### Region
> AWSサービスが提供されるサーバーの物理的な領域のこと  
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
-
### EB
Elastic Beanstalk
### Lambda
-
### Auto Scaling
-
### AMI
Amazon Machine Images

## Storage
### S3
Simple Storage Service
コンテナストレージ
### Glacier
ファイルストレージ
### EBS
Elastic Block Store

## Database
### DynamoDB
NoSQL
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
