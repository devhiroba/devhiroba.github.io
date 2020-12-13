# DynamoDB
[https://aws.amazon.com/jp/dynamodb/](https://aws.amazon.com/jp/dynamodb/)

## DynamoDB とは
AWS で提供するフルマネージド型の NoSQL データベースサービスです。
データベースの管理を AWS 側でしてくれるので便利  
高速なのでセッションサーバーとしても問題ない、モバイルゲームアプリでも DynamoDB を利用しているようです。  

## DynamoDB ローカルを Java のユニットテストで使用する方法  
DynamoDB ローカルとは？AWS DynamoDB がローカルの開発環境で使用できる Docker コンテナのことです。  
開発環境 

- Java  
- Gradle  
- IntelliJ  
- JUnit5  
- DynamoDBMapper  
- Docker-compose

### build.gradle
JUnit5 と DynamoDBMapper を使用する為、build.gradle の 設定をします
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
DynamoDBローカルの Docker コンテナを使用する為、docker-compose.yml を定義します
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

### プロジェクトの構成
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

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Map;

import static org.junit.jupiter.api.Assertions.*;

public class DynamoDBMapperTest {

    private static AmazonDynamoDB amazonDynamoDB;
    private static DynamoDBMapper dynamoDBMapper;

    /**
     * DB接続情報はオンコディングしない
     * application.yml 等を参照するようにすること
     */
    private static final String region = "ap-northeast-1";
    private static final String endpointUrl = "http://localhost:8000";
    private static final String accessKey = "dummy";
    private static final String secretKey = "dummykey";

    /**
     * 全テスト実行前に1回のみ実行される
     */
    @BeforeAll
    static void setDynamoDB() {
        AWSCredentials awsCredentials = new BasicAWSCredentials(accessKey, secretKey);
        AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(awsCredentials);
        AwsClientBuilder.EndpointConfiguration endpointConfiguration =
                new AwsClientBuilder.EndpointConfiguration(endpointUrl, region);
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
     * アイテム登録＆アイテム取得
     */
    @Test
    void putItem() {
        // アイテム設定
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
     * アイテム更新
     */
    @Test
    void updItem() {
        // アイテム設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        userModel.setName("yamada");
        userModel.setAge(10);
        // アイテム登録
        dynamoDBMapper.save(userModel);

        // 更新用アイテム設定
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
        // アイテム設定
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

    /**
     * アイテム登録（複数件）、アイテム取得（複数件）
     */
    @Test
    void putItems() {
        // アイテム設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        userModel.setName("yamada");
        userModel.setAge(10);
        UserModel userModel2 = new UserModel();
        userModel2.setId("mori1900");
        userModel2.setGender("man");
        userModel2.setName("morita");
        userModel2.setAge(20);
        List<UserModel> userModelList = new ArrayList<>();
        userModelList.add(userModel);
        userModelList.add(userModel2);

        // アイテム登録
        dynamoDBMapper.batchSave(userModelList);
        // アイテム登録
        //dynamoDBMapper.batchSave(Arrays.asList(userModel, userModel2));

        // 取得するアイテムの条件設定
        UserModel resultUserModel = new UserModel();
        resultUserModel.setId(userModel.getId());
        resultUserModel.setGender(userModel.getGender());
        UserModel resultUserModel2 = new UserModel();
        resultUserModel2.setId(userModel2.getId());
        resultUserModel2.setGender(userModel2.getGender());
        ArrayList<Object> resultUserModelList = new ArrayList<Object>();
        resultUserModelList.add(resultUserModel);
        resultUserModelList.add(resultUserModel2);

        // アイテム取得
        Map<String, List<Object>> items = dynamoDBMapper.batchLoad(resultUserModelList);
        assertEquals(userModelList, items.get("UserModel"));
    }

    /**
     * アイテム削除（複数件）
     */
    @Test
    void delItems() {
        // アイテム設定
        UserModel userModel = new UserModel();
        userModel.setId("yama2020");
        userModel.setGender("man");
        UserModel userModel2 = new UserModel();
        userModel2.setId("mori1900");
        userModel2.setGender("man");
        // アイテム登録
        dynamoDBMapper.batchSave(Arrays.asList(userModel, userModel2));
        // 取得するアイテム設定
        UserModel deleteUserModel = new UserModel();
        deleteUserModel.setId(userModel.getId());
        deleteUserModel.setGender(userModel.getGender());
        UserModel deleteUserModel2 = new UserModel();
        deleteUserModel2.setId(userModel2.getId());
        deleteUserModel2.setGender(userModel2.getGender());
        ArrayList<Object> deleteUserModelList = new ArrayList<Object>();
        deleteUserModelList.add(deleteUserModel);
        deleteUserModelList.add(deleteUserModel2);
        // 結果確認
        assertNotNull(dynamoDBMapper.batchLoad(deleteUserModelList).get("UserModel"));

        // アイテム削除
        dynamoDBMapper.batchDelete(deleteUserModelList);
        // アイテム削除
        //dynamoDBMapper.batchDelete(Arrays.asList(deleteUserModel, deleteUserModel2));

        // 結果確認
        Map<String, List<Object>> resultItems = dynamoDBMapper.batchLoad(deleteUserModelList);
        assertTrue(resultItems.get("UserModel").size() == 0);
    }
}
```

## テスト用シードデータ（JSON）を登録する方法
DynamoDBMapper を使用した CRUD 方法について理解しました。次はテストデータ登録用の共通部品を作成してデータを登録します。
DynamoDB Local では DBUnit が利用できないため、JSON 形式のデータを DynamoDB Local に登録するメソッドを作成します。  

ポイントは GSON を使用して JSON データを Model（Entity） クラスに変換することです。変換した Model クラスを DynamoDBMapper を使用して登録します。

![Test Image 3](/resource/image/dynamodb-local-seeddata-diagram-image.png)

### build.gradle
JSON から Object へ変換するため GSON を gradle に追加します。
```
dependencies {
    
    ・・・

    // GSON
    testImplementation('com.google.code.gson:gson:2.8.6')
}
```

### プロジェクトの構成
![Test Image 3](/resource/image/dynamodb-local-dynamodbmapper-sample-seeddata-image.png)

### DynamoDBUtil
```
package util;

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
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
import models.UserModel;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.lang.reflect.Type;
import java.util.ArrayList;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertTrue;

public class DynamoDBUtil {

    /**
     * DB接続情報はオンコディングしない
     * application.yml 等を参照するようにすること
     */
    private final String region = "ap-northeast-1";
    private final String endpointUrl = "http://localhost:8000";
    private final String accessKey = "dummy";
    private final String secretKey = "dummykey";

    private AmazonDynamoDB amazonDynamoDB;
    public DynamoDBMapper dynamoDBMapper;

    public DynamoDBUtil() {
        AWSCredentials awsCredentials = new BasicAWSCredentials(accessKey, secretKey);
        AWSCredentialsProvider awsCredentialsProvider = new AWSStaticCredentialsProvider(awsCredentials);
        AwsClientBuilder.EndpointConfiguration endpointConfiguration =
                new AwsClientBuilder.EndpointConfiguration(endpointUrl, region);
        amazonDynamoDB = AmazonDynamoDBClientBuilder.standard()
                .withCredentials(awsCredentialsProvider)
                .withEndpointConfiguration(endpointConfiguration).build();
        dynamoDBMapper = new DynamoDBMapper(amazonDynamoDB, DynamoDBMapperConfig.DEFAULT);
        assertNotNull(amazonDynamoDB);
        assertNotNull(dynamoDBMapper);
    }

    /**
     * テーブル作成
     */
    public void createTable() {
        CreateTableRequest createTableRequest = dynamoDBMapper.generateCreateTableRequest(UserModel.class)
                .withProvisionedThroughput(new ProvisionedThroughput(1L, 1L));
        assertTrue(TableUtils.createTableIfNotExists(amazonDynamoDB, createTableRequest));
    }

    /**
     * テーブル削除
     */
    public void deleteTable() {
        DeleteTableRequest deleteTableRequest = dynamoDBMapper.generateDeleteTableRequest(UserModel.class);
        assertTrue(TableUtils.deleteTableIfExists(amazonDynamoDB, deleteTableRequest));
    }

    /**
     * シードデータをロードする。
     * UserModelのみ利用可能
     */
    public void seedDataLoaderForUserModel(String seedFile) {
        Gson gson = new Gson();
        Type modelType = new TypeToken<ArrayList<UserModel>>(){}.getType();
        List<UserModel> modelList = null;
        try {
            modelList = gson.fromJson(new FileReader(seedFile), modelType);
        } catch(FileNotFoundException e) {
            System.out.println(e.getMessage());
        }
        dynamoDBMapper.batchSave(modelList);
    }

    /**
     * シードデータをロードする。
     */
    public <T> void seedDataLoaderForAllModel(String seedFile, Class<T> classType) {
        Gson gson = new Gson();
        Type collectionType = TypeToken.getParameterized(List.class, classType).getType();
        List<T> modelList = null;
        try {
            modelList = gson.fromJson(new FileReader(seedFile), collectionType);
        } catch(FileNotFoundException e) {
            System.out.println(e.getMessage());
        }
        dynamoDBMapper.batchSave(modelList);
    }

    /**
     * シードデータをロードする。
     */
    public <T> void seedDataLoaderForAllModelV2(String seedFile, Class<T[]> classes) {
        Gson gson = new Gson();
        T[] models = null;
        try {
            models = gson.fromJson(new FileReader(seedFile), classes);
        } catch(FileNotFoundException e) {
            System.out.println(e.getMessage());
        }
        dynamoDBMapper.batchSave(models);
    }
}
```

### DynamoDBLoadSeedDataTest
```
import models.UserModel;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import util.DynamoDBUtil;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class DynamoDBLoadSeedDataTest {

    private DynamoDBUtil dynamoDBUtil;

    public DynamoDBLoadSeedDataTest() {
        this.dynamoDBUtil = new DynamoDBUtil();
    }

    /**
     * テーブル作成
     */
    @BeforeEach
    void beforeEach(){
        dynamoDBUtil.createTable();
    }

    /**
     * テーブル削除
     */
    @AfterEach
    void afterEach() {
        dynamoDBUtil.deleteTable();
    }

    /**
     * シードデータを登録する
     * UserModelのみ登録可能
     */
    @Test
    void seedDataLoaderForUserModelTest() {
        /**
         * アイテム登録
         */
        dynamoDBUtil.seedDataLoaderForUserModel("src/test/resources/seed-data-usermodel.json");
        /**
         * アイテム取得
         */
        UserModel userModel = new UserModel();
        userModel.setId("yama1010");
        userModel.setGender("man");
        UserModel userModel2 = new UserModel();
        userModel2.setId("mori9910");
        userModel2.setGender("man");
        List<UserModel> userModelList = new ArrayList<>();
        userModelList.add(userModel);
        userModelList.add(userModel2);
        Map<String, List<Object>> items = dynamoDBUtil.dynamoDBMapper.batchLoad(userModelList);
        System.out.println(items.get("UserModel"));
    }

    /**
     * シードデータを登録する
     */
    @Test
    void seedDataLoaderForAllModelTest() {
        dynamoDBUtil.<UserModel>seedDataLoaderForAllModel("src/test/resources/seed-data-v1.json", UserModel.class);
    }

    /**
     * シードデータを登録する
     */
    @Test
    void seedDataLoaderForAllModelV2Test() {
        dynamoDBUtil.<UserModel>seedDataLoaderForAllModelV2("src/test/resources/seed-data-v2.json", UserModel[].class);
    }
}
```

### seed-data-usermodel.json
```
[
  {
    "id": "yama1010",
    "gender": "man",
    "name": "yamada",
    "age": 10
  },
  {
    "id": "mori9910",
    "gender": "man",
    "name": "mori",
    "age": 20
  }
]
```

### GitHub Repository
[https://github.com/devhiroba/dynamodb-local-dynamodbmapper-sample.git](https://github.com/devhiroba/dynamodb-local-dynamodbmapper-sample.git)
