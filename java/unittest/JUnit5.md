# JUnit5
[https://junit.org/junit5/docs/current/user-guide/](https://junit.org/junit5/docs/current/user-guide/)  
[https://oohira.github.io/junit5-doc-jp/user-guide/](https://oohira.github.io/junit5-doc-jp/user-guide/)

## JUnit とは
Javaで書かれたプログラムのテストを自動化してくれるテスティングフレームワークです。  
テストケースを作って置けばビルドするたびにテストしてくれます。

## JUnit5 の構造
![JUnit5-diagram-image](/resource/image/junit5-diagram-image.png)
- JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage
- JUnit Platform：テストを実行させるランチャーを提供、TestEngine API を提供
- Jupiter：JUnit Platform の TestEngine API を実装して JUnit5 用 TestEngine API を提供
- Vintage：JUnit Platform の TestEngine API を実装して JUnit3 と JUnit4 用 TestEngine API を提供

JAVA の main クラスでもないのに下記のようなメソッドの実行ができるのは JUnit Platform があるからです。  
![JUnit5-testclass-image](/resource/image/junit5-testclass-image.png)

## 単体テストで JUnit5 を使用する方法

### build.gradle
JUnit5 を使用する為、build.gradle の 設定をします
```
dependencies {
    // JUnit
    testImplementation('org.junit.jupiter:junit-jupiter:5.5.2')
}

// use JUnit 5 platform
test {
    useJUnitPlatform()
}
```

### プロジェクトの構成
![Test Image 3](/resource/image/dbunit-sample-image.png)

### JUnit5Test
Junit5の基本アノテーション
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.*;

public class JUnit5Test {

    @Test
    void test1() {
        System.out.println("test1");
    }

    @Test
    void test2() {
        System.out.println("test2");
    }

    /**
     * "@Disabled"
     * テストを実行しない
     * JUnit4 では @Ignore
     */
    @Test
    @Disabled("テスト不要")
    void test3() {
        System.out.println("test2");
    }

    /**
     *
     */
    @DisplayName("aaa")
    @Test
    void test4() {
        System.out.println("test4");
    }

    /**
     * JUnit5Test クラスのテストが実行される前に1回のみ実行
     * 必ず static
     * JUnit4 では @BeforeClass
     */
    @BeforeAll
    static void beforeAll() {
        System.out.println("beforeAll");
    }

    /**
     * JUnit5Test クラスのテストが実行された後に1回のみ実行
     * 必ず static
     * JUnit4 では @AfterClass
     */
    @AfterAll
    static void afterAll() {
        System.out.println("afterAll");
    }

    /**
     * 各テストが実行される前に実行
     * Junit4 では @Before
     */
    @BeforeEach
    void beforeEach() {
        System.out.println("beforeEach");
    }

    /**
     * 各テストが実行された後に実行
     * JUnit4 では @After
     */
    @AfterEach
    void afterEach() {
        System.out.println("afterEach");
    }
}

```

### Junit5TestDisplayName
テストメソッドの名前を変更
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.DisplayNameGeneration;
import org.junit.jupiter.api.DisplayNameGenerator;
import org.junit.jupiter.api.Test;

/**
 * "@DisplayNameGeneration"
 * 全てのテストクラス名の _ を空白に変換する
 */
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
public class JUnit5TestDisplayName {

    @Test
    void test_no1(){
        System.out.println("test_no1");
    }

    @Test
    void test_no2(){
        System.out.println("test_no2");
    }

    /**
     * "@DisplayName"
     * クラス名の代わりに指定文字列を表示する
     */
    @Test
    @DisplayName("テスト3")
    void test_no3(){
        System.out.println("test_no3");
    }
}

```

### Junit5TestAssertion
データの比較方法
```
package devhiroba.junit5.test;

import devhiroba.junit5.domain.ExceptionModel;
import devhiroba.junit5.domain.TimeoutModel;
import devhiroba.junit5.domain.UserModel;
import org.junit.jupiter.api.Test;
import java.time.Duration;

import static org.junit.jupiter.api.Assertions.*;

public class JUnit5TestAssertion {

    /**
     * test1()の場合、複数のテストを実行しているが、
     * 途中でエラーなった場合、その次のテストは実行しない
     */
    @Test
    void test1(){
        UserModel userModel_null = null;
        UserModel userModel = new UserModel();

        /**
         * Null の場合 OK
         */
        assertNull(userModel_null);
        /**
         * NotNull の場合 OK
         */
        assertNotNull(userModel);
        /**
         * 期待値と実際の結果が一致する場合 OK
         * テストエラーの場合に出力するメッセージの設定の可能
         */
        assertEquals("japan2020", userModel.getId(), () -> "id が japan2020 ではない");
        /**
         * 期待値と実際の結果が一致しない場合 OK
         */
        assertNotEquals("japan2019", userModel.getId());
        /**
         * 条件または値が True の場合 OK
         */
        assertTrue(userModel.getAge() > 10);
        /**
         * 想定した Exception が発生した場合 OK
         */
        assertThrows(NullPointerException.class, () -> new ExceptionModel());
        NullPointerException ex = assertThrows(NullPointerException.class, () -> new ExceptionModel());
        System.out.println(ex.getMessage());
        /**
         * 指定した時間を超過した場合 NG
         * TimeoutModel の中の処理が30ミリセカンドを超えた場合 NG
         * TimeoutModel の処理か終わるまで待機して処理が終わった後に処理時間を比較する
         */
        assertTimeout(Duration.ofMillis(30), () -> new TimeoutModel(20));
        /**
         * 指定した時間を超過した場合 NG
         * 指定した時間になると TimeoutModel の処理を待たずに NG にする
         */
        assertTimeoutPreemptively(Duration.ofMillis(30), () -> new TimeoutModel(20));
    }

    /**
     * test2()の場合、assertAll の中テストの途中でエラーがあっても全てのテストを実行する
     */
    @Test
    void test2(){
        UserModel userModel_null = null;
        UserModel userModel = new UserModel();

        assertAll(
            () -> assertNull(userModel_null),
            () -> assertNotNull(userModel),
            () -> assertEquals("japan2021", userModel.getId(), () -> "id が japan2020 ではない"), // ←エラーになる
            () -> assertNotEquals("japan2019", userModel.getId()),
            () -> assertTrue(userModel.getAge() > 10)
        );
    }
}

```

### Junit5TestAssume
条件によってテストの実行可否を制御
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.condition.*;

import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assumptions.assumeTrue;
import static org.junit.jupiter.api.Assumptions.assumingThat;

public class JUnit5TestAssume {

    /**
     * assumeTure() が True ではない場合、assumeTure() 以下は実行しない
     * この場合テスト結果も failed ではなく ignored になる
     * 環境変数を参照して開発環境でのみ実行するなどで利用できる
     */
    @Test
    void test1() {
        assumeTrue(true);
        System.out.println("true です!");
    }

    /**
     * 条件を満たした場合のみテストを実行する
     * 下記の場合は second test の中のテストは実行されない
     */
    @Test
    void test2() {
        // first test
        assumingThat(1 > 0, () -> {
            assertTrue(true, () -> "test2 first!");
            System.out.println("test2 first!");
        });
        // second test
        assumingThat(1 < 0, () -> {
            assertTrue(true, () -> "test2 second!");
            System.out.println("test2 second!");
        });
    }

    /**
     * 指定した OS のみテストを事項する
     */
    @Test
    @EnabledOnOs(OS.MAC)
    void test3() {
        System.out.println("test3!");
    }

    /**
     * 指定した OS ではテストを実行しない
     */
    @Test
    @DisabledOnOs({OS.MAC, OS.LINUX})
    void test4() {
        System.out.println("test4!");
    }

    /**
     * 指定した JRE バージョンの場合のみ実行
     */
    @Test
    @EnabledOnJre({JRE.JAVA_11, JRE.JAVA_12})
    void test5() {
        System.out.println("test5!");
    }

    /**
     * 取得した OS の環境変数 ENV の値が "DEV" の場合のみテストを実行する
     */
    @Test
    @EnabledIfEnvironmentVariable(named = "ENV", matches="DEV")
    void test6() {
        System.out.println("test6!");
    }
}

```

### Junit5Tag
テストケースを分離する
```

```

### Junit5TestAssertion
データの比較方法
```

```
### GitHub Repository
[https://github.com/devhiroba/dbunit-sample.git](https://github.com/devhiroba/dbunit-sample.git)
