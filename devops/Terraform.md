# Terraform
[https://www.terraform.io/](https://www.terraform.io/)

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## IaC とは
- Infrastructure as Code（コードとしてのインフラストラクチャー）
- インフラ（サーバー、ミドルウェア、サービスなど）をコードベースで構築・管理を行うこと
- 再利用、履歴管理、運用保守などの観点からメリットがある

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Terraform とは
- IaCツールの一つ
- AWS、Azure、GCP、他、に対応
- .tfファイル形式を使用

{:.bg-gray-dark.text-white.p-2.box-shadow-large}
## Terraform の基本構成要素


### provider
- 普通は provider.tf の名前で作成します。
- provider の中には色んな Arguments があります。
- AWS resource を利用するためのファイルをダウンロードする役割です。（例：SDKなど）
```
provider "aws" {
  region = "us-east-1"
  version = "~> 3.0"
}
```

### resource
- 決まったルールはありませんが、main.tf、vpc.tf など分かりやすい名前を使いましょう。
- resource の中には色んな Arguments があります
- resource "リソース種類" "リソース名"
- リソース種類：aws_lb、aws_amiなどTerraformで予め定義されています。
- リソース名：任意の名前を設定します。
```
# VPC 作成
resource "aws_vpc" "sampleVPC" {
  cidr_block = "10.0.0.0/16"
}
```
