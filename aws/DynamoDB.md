# DynamoDB

## DynamoDB とは
AWS で提供するフルマネージド型の NoSQL データベースサービスである  
データベースの管理を AWS 側でしてくれるので便利  
高速なのでセッションサーバーとしても問題ない、モバイルゲームアプリでも DynamoDB を利用しているようだ  

## DynamoDB を Java のユニットテストで使用する方法  
下記の開発環境で DynamoDB を利用する方法を説明する  

- Java  
- Gradle  
- IntelliJ  
- Junit5  
- DynamoDBMapper  
- Docker-compose

### build.gradle
Junit5 と DynamoDBMapper を使用する為、下記を build.gradle の dependencies に設定する


testImplementation("com.amazonaws:aws-java-sdk-dynamodb:1.11.563")
 




## Dockerコンテナ起動と同時にテーブルを作成するdocker-compose.yml

amazon/dynamodb-local:1.13.5のテーブル取得コマンド
AWS_ACCESS_KEY_ID=dummy AWS_SECRET_ACCESS_KEY=dummykey aws dynamodb list-tables --endpoint-url http://localhost:8000 --region ap-northeast-1

