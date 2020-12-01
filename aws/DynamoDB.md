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
    implementation("com.amazonaws:aws-java-sdk-dynamodb:1.11.563")

    // lombok
    implementation("org.projectlombok:lombok:1.18.12")
    annotationProcessor("org.projectlombok:lombok:1.18.12")
}

// use JUnit 5 platform
test {
    useJUnitPlatform()
}
```
### docker-compose.yml
DynamoDBローカルの Docker コンテナを使用する為、docker-compose.yml を定義する
```scss
version: '2.0'
services:
  dynamodb:
    container_name: dynamodb
    image: amazon/dynamodb-local:latest
    ports:
      - 8000:8000
    restart: always
```

### パッケージ構成
![Test Image 3](/resource/image/dynamodb-local-dynamodbmapper-sample-image.png)

### UserModel
```scss
package models;

import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBAttribute;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBHashKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBRangeKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBTable;
import lombok.Data;

@Data
@DynamoDBTable(tableName = "UserModel")
public class UserModel {
    /**
     * Partition key
     */
    @DynamoDBHashKey(attributeName = "id")
    private String id;
    /**
     * Range key
     */
    @DynamoDBRangeKey(attributeName = "gender")
    private String gender;

    @DynamoDBAttribute(attributeName = "name")
    private String name;

    @DynamoDBAttribute(attributeName = "age")
    private Integer age;
}
```
