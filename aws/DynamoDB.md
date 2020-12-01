# DynamoDB

## DynamoDBとは
AWS で提供するフルマネージド型の NoSQL データベースサービスである  
データベースの管理を AWS 側でしてくれるので便利  
高速なのでセッションサーバーとしても問題ない、モバイルゲームアプリでもDynamoDBを利用しているようだ  

## DynamoDBをJavaのユニットテストで利用する方法  
下記の開発環境でDynamoDBを利用する方法を説明する  
Java  
Gradle  
IntelliJ  
Junit5  
DynamoDBMapper  




## Dockerコンテナ起動と同時にテーブルを作成するdocker-compose.yml

amazon/dynamodb-local:1.13.5のテーブル取得コマンド
AWS_ACCESS_KEY_ID=dummy AWS_SECRET_ACCESS_KEY=dummykey aws dynamodb list-tables --endpoint-url http://localhost:8000 --region ap-northeast-1

