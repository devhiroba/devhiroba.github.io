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
JUnit5 と DBUnit を使用する為、build.gradle の 設定をする
```
dependencies {
    // JUnit
    testImplementation('org.junit.jupiter:junit-jupiter:5.5.2')

    // DBUnit
    testImplementation('org.dbunit:dbunit:2.7.0')

    // Oracle JDBC
    runtimeOnly("com.oracle.database.jdbc:ojdbc8:19.7.0.0")
}

// use JUnit 5 platform
test {
    useJUnitPlatform()
}
```

### docker-compose.yml
Oracle Database の Docker コンテナを使用する為、docker-compose.yml を定義する
```
version: '2.0'
services:
    oracle:
      container_name: oracle11g
      image: "jaspeen/oracle-xe-11g"
      ports:
        - "1521:1521"
```

### プロジェクトの構成
![Test Image 3](/resource/image/dbunit-sample-image.png)
