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

### DBUnitTest
```
import org.dbunit.IDatabaseTester;
import org.dbunit.JdbcDatabaseTester;
import org.dbunit.dataset.IDataSet;
import org.dbunit.dataset.xml.FlatXmlDataSetBuilder;
import org.dbunit.operation.DatabaseOperation;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.io.File;

public class DBUnitTest {

    private static IDatabaseTester databaseTester;

    public static void loadData(String seedFile) {
        try {
            IDataSet dataSet = new FlatXmlDataSetBuilder().build(new File(seedFile));
            DatabaseOperation.CLEAN_INSERT.execute(databaseTester.getConnection(), dataSet);
        } catch (Throwable e) {
            throw new RuntimeException(e);
        }
    }

    @BeforeAll
    static void setOracleDB() throws ClassNotFoundException {
        databaseTester = new JdbcDatabaseTester(
                "oracle.jdbc.driver.OracleDriver",
                "jdbc:oracle:thin:@127.0.0.1:1521/xe",
                "system",
                "oracle",
                "HR");
    }

    @BeforeEach
    void setSeedDataEach() {
        loadData("src/test/resources/seed-data-each.xml");
    }

    @Test
    void test() {

    }
}
```

### seed-data-each.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<dataset>
    <job_history employee_id="102" start_date="2020-10-10 00:00:03" end_date="2020-11-10 00:00:04" job_id="IT_PROG" department_id="60" />
    <job_history employee_id="101" start_date="2020-11-01 00:00:03" end_date="2020-12-01 00:00:04" job_id="AC_ACCOUNT" department_id="110" />
</dataset>
```

### GitHub Repository
[https://github.com/devhiroba/dbunit-sample.git](https://github.com/devhiroba/dbunit-sample.git)
