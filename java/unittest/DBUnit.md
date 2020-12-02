# DBUnit

## DBUnit とは
単体テスト利用するデータベースアクセスツール  
データベースへの CRUD 処理のテストを行うためには初期データの登録が必要だが、  
その時に使用できるのが DBUnit だ！

## 単体テストで DBUnit を使用する方法
下記の開発環境で DBUnit を利用する方法を説明する  

- Java  
- Gradle  
- IntelliJ  
- JUnit5  
- DBUnit  
- Docker-compose

### build.gradle
JUnit5 と DynamoDBMapper を使用する為、build.gradle の 設定をする

### docker-compose.yml
DynamoDBローカルの Docker コンテナを使用する為、docker-compose.yml を定義する

### プロジェクトの構成
![Test Image 3](/resource/image/dynamodb-local-dynamodbmapper-sample-image.png)
