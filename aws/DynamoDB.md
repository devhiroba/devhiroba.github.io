# DynamoDB

## DynamoDB とは
AWS で提供するフルマネージド型の NoSQL データベースサービスである  
データベースの管理を AWS 側でしてくれるので便利  
高速なのでセッションサーバーとしても問題ない、モバイルゲームアプリでも DynamoDB を利用しているようだ  

## DynamoDB ローカルを Java のユニットテストで使用する方法  
下記の開発環境で DynamoDB を利用する方法を説明する 
DynamoDB ローカルとは？AWS DynamoDB がローカルの開発環境で使用できる Docker コンテナのこと

- Java  
- Gradle  
- IntelliJ  
- JUnit5  
- DynamoDBMapper  
- Docker-compose

### build.gradle
JUnit5 と DynamoDBMapper を使用する為、build.gradle の 設定をする
```scss
dependencies {
    // JUnit
    testImplementation('org.junit.jupiter:junit-jupiter:5.5.2')
    // dynamodb
    Implementation("com.amazonaws:aws-java-sdk-dynamodb:1.11.563")
}

// use JUnit 5 platform
test {
    useJUnitPlatform()
}
```
### docker-compose.yml
DynamoDBローカルの Docker コンテナを使用する為、docker-compose.yml を定義する
```scss
version: '1.0'
services:
  dynamodb:
    container_name: dynamodb
    image: amazon/dynamodb-local:latest
    ports:
      - 8000:8000
    restart: always
    command: [ "-jar", "DynamoDBLocal.jar", "-sharedDb", "-inMemory" ]
```

## Dockerコンテナ起動と同時にテーブルを作成するdocker-compose.yml

amazon/dynamodb-local:1.13.5のテーブル取得コマンド
AWS_ACCESS_KEY_ID=dummy AWS_SECRET_ACCESS_KEY=dummykey aws dynamodb list-tables --endpoint-url http://localhost:8000 --region ap-northeast-1

