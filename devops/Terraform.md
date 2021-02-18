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
- resource の中には色んな Arguments があります。
- resource "リソース種類" "リソース名"
- リソース種類：aws_lb、aws_amiなどTerraformで予め定義されています。
- リソース名：任意の名前を設定します。  
```
# VPC 作成
resource "aws_vpc" "sampleVPC" {
  cidr_block = "10.0.0.0/16"
}
```

### state
- terraform.tfstate 名前は固定です。
- terraform で作成したコードを実行すると作成されるファイルです。
- terraform コードを実行して作成されたリソース(インフラ)の結果です。
- 現在のインフラの状態ではありません。
- state ファイル状態と実際のインフラ状態を一致させるのが大事です。
- state ファイルは remote repository である backend にも保存できます。  
```
{
  "version": 2,
  "terraform_version": "0.10.10",
  "serial": 1,
  "lineage": "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX",
  "outputs": {},
  "resources": [
    {...},
    {...},
    {...}
  ]
}
```

### output
- 下記の例だと変数名 cidr_block で resource の cidr_block 値を state ファイルい保存します。
- output で定義した変数は remote 経由で利用できます。  
```
resource "aws_vpc" "sampleVPC" {
  cidr_block = "10.0.0.0/16"
}

output "cidr_block" {
  value = aws_vpc.default.cidr_block
}
```
