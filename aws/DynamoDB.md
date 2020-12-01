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
```
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
```
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
```
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

### DynamoDBMapperTest
```
import com.amazonaws.auth.AWSCredentials;
import com.amazonaws.auth.AWSCredentialsProvider;
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.client.builder.AwsClientBuilder;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapperConfig;
import com.amazonaws.services.dynamodbv2.model.CreateTableRequest;
import com.amazonaws.services.dynamodbv2.model.DeleteTableRequest;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;
import com.amazonaws.services.dynamodbv2.util.TableUtils;
import models.UserModel;
import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.*;

public class DynamoDBMapperTest {

    private static AmazonDynamoDB amazonDynamoDB;
    private static DynamoDBMapper dynamoDBMapper;

    /**
     * 全テスト実行前に1回のみ実行される
     */
    @BeforeAll
    static void setDynamoDB() {
        AWSCredentials awsCredentials = new BasicAWSCredentials("dummy", "dummykey");
        AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(awsCredentials);
        AwsClientBuilder.EndpointConfiguration endpointConfiguration =
                new AwsClientBuilder.EndpointConfiguration("http://localhost:8000", "ap-northeast-1");
        amazonDynamoDB = AmazonDynamoDBClientBuilder.standard()
                .withCredentials(awsCredentialsProvider)
                .withEndpointConfiguration(endpointConfiguration).build();
        dynamoDBMapper = new DynamoDBMapper(amazonDynamoDB, DynamoDBMapperConfig.DEFAULT);
    }

    /**
     * テーブル作成
     * 各テストの実行前に実行される
     */
    @BeforeEach
    void createTable() {
        CreateTableRequest createTableRequest = dynamoDBMapper.generateCreateTableRequest(UserModel.class)
                .withProvisionedThroughput(new ProvisionedThroughput(1L, 1L));
        assertTrue(TableUtils.createTableIfNotExists(amazonDynamoDB, createTableRequest));
    }

    /**
     * テーブル削除
     * 各テストの実行後に実行される
     */
    @AfterEach
    void deleteTable() {
        DeleteTableRequest deleteTableRequest = dynamoDBMapper.generateDeleteTableRequest(UserModel.class);
        assertTrue(TableUtils.deleteTableIfExists(amazonDynamoDB, deleteTableRequest));
    }

    /**
     * アイテム登録
     */
    @Test
    void putItem() {
        // データ設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        userModel.setName("yamada");
        userModel.setAge(10);
        // アイテム登録
        dynamoDBMapper.save(userModel);
        // 登録したアイテムを取得
        UserModel getUserModel = dynamoDBMapper.load(UserModel.class, userModel.getId(), userModel.getGender());
        // log
        System.out.println("putItem result : " + getUserModel.getId());
        System.out.println("putItem result : " + getUserModel.getGender());
        System.out.println("putItem result : " + getUserModel.getName());
        System.out.println("putItem result : " + getUserModel.getAge());
        // 結果確認
        assertEquals(userModel.getId(), getUserModel.getId());
    }

    /**
     * アイテム取得
     */
    @Test
    @Disabled
    void getItem() {
        // putItem()の下記の登録したアイテムを取得するところで確認できるのでスルーする
        // UserModel getUserModel = dynamoDBMapper.load(UserModel.class, userModel.getId(), userModel.getGender());
    }

    /**
     * アイテム更新
     */
    @Test
    void updItem() {
        // データ設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        userModel.setName("yamada");
        userModel.setAge(10);
        // アイテム登録
        dynamoDBMapper.save(userModel);

        // 更新用データ設定
        UserModel updUserModel = new UserModel();
        updUserModel.setId("yama2020");
        updUserModel.setGender("man");
        updUserModel.setName("nakamura");
        updUserModel.setAge(15);
        // アイテム登録
        dynamoDBMapper.save(updUserModel);
        // 登録したアイテムを取得
        UserModel getUserModel = dynamoDBMapper.load(UserModel.class, userModel.getId(), userModel.getGender());
        // log
        System.out.println("updItem result : " + getUserModel.getId());
        System.out.println("updItem result : " + getUserModel.getGender());
        System.out.println("updItem result : " + getUserModel.getName());
        System.out.println("updItem result : " + getUserModel.getAge());
        // 結果確認
        assertEquals(updUserModel.getAge(), getUserModel.getAge());
    }

    /**
     * アイテム削除
     */
    @Test
    void deleteItem() {
        // データ設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        userModel.setName("yamada");
        userModel.setAge(10);
        // アイテム登録
        dynamoDBMapper.save(userModel);
        assertNotNull(dynamoDBMapper.load(UserModel.class, userModel.getId(), userModel.getGender()));

        // アイテム削除
        dynamoDBMapper.delete(userModel);
        assertNull(dynamoDBMapper.load(UserModel.class, userModel.getId(), userModel.getGender()));
    }
}
```

### GitHub Repository
