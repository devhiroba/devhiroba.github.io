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

JAVA の main クラスでもないのに下記のようなメソッドの実行ができるのは JUnit Platform のお陰です。

![JUnit5-testclass-image](/resource/image/junit5-testclass-image.png)

## 単体テストで JUnit5 を使用する方法

### build.gradle
JUnit5 を使用する為、build.gradle の設定をします
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

### JUnit5Test.java
Junit5のライフサイクル
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.*;

public class JUnit5Test {

    @Test
    void test1() {
        System.out.println("JUnit5Test test1");
    }

    @Test
    void test2() {
        System.out.println("JUnit5Test test2");
    }

    /**
     * "@Disabled"
     * テストを実行しない
     * JUnit4 では @Ignore
     */
    @Test
    @Disabled("テスト不要")
    void test3() {
        System.out.println("JUnit5Test test2");
    }

    /**
     *
     */
    @DisplayName("aaa")
    @Test
    void test4() {
        System.out.println("JUnit5Test test4");
    }

    /**
     * JUnit5Test クラスのテストが実行される前に1回のみ実行
     * 必ず static
     * JUnit4 では @BeforeClass
     */
    @BeforeAll
    static void beforeAll() {
        System.out.println("JUnit5Test beforeAll");
    }

    /**
     * JUnit5Test クラスのテストが実行された後に1回のみ実行
     * 必ず static
     * JUnit4 では @AfterClass
     */
    @AfterAll
    static void afterAll() {
        System.out.println("JUnit5Test afterAll");
    }

    /**
     * 各テストが実行される前に実行
     * Junit4 では @Before
     */
    @BeforeEach
    void beforeEach() {
        System.out.println("JUnit5Test beforeEach");
    }

    /**
     * 各テストが実行された後に実行
     * JUnit4 では @After
     */
    @AfterEach
    void afterEach() {
        System.out.println("JUnit5Test afterEach");
    }
}
```

### Junit5TestDisplayName.java
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
        System.out.println("JUnit5TestDisplayName test_no1");
    }

    @Test
    void test_no2(){
        System.out.println("JUnit5TestDisplayName test_no2");
    }

    /**
     * "@DisplayName"
     * クラス名の代わりに指定文字列を表示する
     */
    @Test
    @DisplayName("JUnit5TestDisplayName test_no3")
    void test_no3(){
        System.out.println("JUnit5TestDisplayName test_no3");
    }
}
```
#### @DisplayName を付けても名前が変更されない。。。
これは IntelliJ がテスト時に Gradle(Default) を利用するようになっているためです。IntelliJ IDEA を使用するように変更しましょう！  
File > Settings > Build, Excution, Deployment > Gradle > Gradle projects > Run tests using  
Run tests using を Gradle(Default) から IntelliJ IEDA に変更！

### Junit5TestAssertion
Assertを使用してテストする
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
        System.out.println("JUnit5TestAssume true です!");
    }

    /**
     * 条件を満たした場合のみテストを実行する
     * 下記の場合は second test の中のテストは実行されない
     */
    @Test
    void test2() {
        // first test
        assumingThat(1 > 0, () -> {
            assertTrue(true, () -> "JUnit5TestAssume test2 first!");
            System.out.println("JUnit5TestAssume test2 first!");
        });
        // second test
        assumingThat(1 < 0, () -> {
            assertTrue(true, () -> "JUnit5TestAssume test2 second!");
            System.out.println("JUnit5TestAssume test2 second!");
        });
    }

    /**
     * 指定した OS のみテストを事項する
     */
    @Test
    @EnabledOnOs(OS.MAC)
    void test3() {
        System.out.println("JUnit5TestAssume test3!");
    }

    /**
     * 指定した OS ではテストを実行しない
     */
    @Test
    @DisabledOnOs({OS.MAC, OS.LINUX})
    void test4() {
        System.out.println("JUnit5TestAssume test4!");
    }

    /**
     * 指定した JRE バージョンの場合のみ実行
     */
    @Test
    @EnabledOnJre({JRE.JAVA_11, JRE.JAVA_12})
    void test5() {
        System.out.println("JUnit5TestAssume test5!");
    }

    /**
     * 取得した OS の環境変数 ENV の値が "DEV" の場合のみテストを実行する
     */
    @Test
    @EnabledIfEnvironmentVariable(named = "ENV", matches="DEV")
    void test6() {
        System.out.println("JUnit5TestAssume test6!");
    }
}
```

### Junit5Tag
テストケースを分離する
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

public class JUnit5TestTag {

    /**
     * "@Tag" を付けてテストの実行可否を制御できる
     * 例えば、時間のかかるテストには "slow" という文字列を設定してci環境でのみ実行させることも可能
     * IntelliJ でテストする時は Edit Configurations の　Test kind を　Tag に変更してテストする
     * Build 時のテストの時には build.gradle に設定が必要
     */
    @Test
    @Tag("fast")
    void test1() {
        System.out.println("JUnit5TestTag test1");
    }

    @Test
    @Tag("fast")
    void test2() {
        System.out.println("JUnit5TestTag test2");
    }

    @Test
    @Tag("fast")
    void test3() {
        System.out.println("JUnit5TestTag test3");
    }

    @Test
    @Tag("slow")
    void test4() {
        System.out.println("JUnit5TestTag test4");
    }
}
```

### Junit5TestAssertion
カスタムアノテーションを作成する
```
package devhiroba.junit5.test;

public class JUnit5TestCustomAnnotation {
    /**
     * "@Test" と "@Tag("fast")" を付けたのと同じ効果
     */
    @CustomAnnotationFastTest
    void test1() {
        System.out.println("JUnit5TestCustomAnnotation test1");
    }

    @CustomAnnotationFastTest
    void test2() {
        System.out.println("JUnit5TestCustomAnnotation test2");
    }

    @CustomAnnotationFastTest
    void test3() {
        System.out.println("JUnit5TestCustomAnnotation test3");
    }

    /**
     * "@Test" と "@Tag("slow")" を付けたのと同じ効果
     */
    @CustomAnnotationSlowTest
    void test4() {
        System.out.println("JUnit5TestCustomAnnotation test4");
    }
}
```

#### CustomAnnotationFastTest.java
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * "@Test" と "@Tag" をメタアノテーションとして使用した CustomAnnotationFastTest アノテーションを作成する
 */
@Target(ElementType.METHOD) // このアノテーションをメソッドで利用する
@Retention(RetentionPolicy.RUNTIME) // このアノテーション情報を RUNTIME まで維持する
@Test // @Test アノテーションを利用する
@Tag("fast") // "fast" Tag を付ける
public @interface CustomAnnotationFastTest {
}
```

#### CustomAnnotationSlowTest.java
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * "@Test" と "@Tag" をメタアノテーションとして使用した CustomAnnotationFastTest アノテーションを作成する
 */
@Target(ElementType.METHOD) // このアノテーションをメソッドで利用する
@Retention(RetentionPolicy.RUNTIME) // このアノテーション情報を RUNTIME まで維持する
@Test // @Test アノテーションを利用する
@Tag("slow") // "slow" Tag を付ける
public @interface CustomAnnotationSlowTest {
}
```

### JUnit5TestRepeat.java
テストを繰り返して実行する
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.RepeatedTest;
import org.junit.jupiter.api.RepetitionInfo;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

public class JUnit5TestRepeat {

    /**
     * テストを3回実施する
     */
    @RepeatedTest(3)
    void test1() {
        System.out.println("JUnit5TestRepeat test1");
    }

    /**
     * RepetitionInfo をパラメータで取得して現在の実行回数とトータル実行回数を取得できる
     */
    @RepeatedTest(3)
    void test2(RepetitionInfo repetitionInfo) {
        System.out.println("JUnit5TestRepeat test2");
        System.out.println(repetitionInfo.getCurrentRepetition() + "/" + repetitionInfo.getTotalRepetitions());
    }

    /**
     * "@RepeatedTest" の属性を利用してテストクラス名を変更できる
     */
    @DisplayName("JUnit5TestRepeat")
    @RepeatedTest(value = 3, name = "{displayName}, {currentRepetition}/{totalRepetitions}")
    void test3() {
        System.out.println("JUnit5TestRepeat test3");
    }

    /**
     * パラメータを渡してテストができる
     * パラメータの数分テストを実行する
     */
    @DisplayName("JUnit5TestRepeat")
    @ParameterizedTest(name = "{index} {displayName} testMsg={0}")
    @ValueSource(strings = {"A", "B", "C"})
    void test4(String testMsg) {
        System.out.println(testMsg);
    }

    /**
     * パラメータを渡してテストができる
     * パラメータの数分テストを実行する
     * Null と 空白をパラメータとして渡すこともできる
     */
    @DisplayName("JUnit5TestRepeat")
    @ParameterizedTest(name = "{index} {displayName} testMsg={0}")
    @ValueSource(strings = {"A", "B", "C"})
    @NullAndEmptySource
//    @NullSource
//    @EmptySource
    void test5(String testMsg) {
        System.out.println(testMsg);
    }

    /**
     * パラメータを渡してテストができる
     * パラメータの数分テストを実行する
     * CSV 形式でパラメータを渡すこともできる
     */
    @DisplayName("JUnit5TestRepeat")
    @ParameterizedTest(name = "{index} {displayName} testMsg={0}")
    @CsvSource({"1, 'A'", "2, 'B'", "3, 'C'"})
    void test6(Integer i, String s) {
        System.out.println(i + "/" + s);
    }
}
```

### JUnit5TestInstance.java
テストインスタンスを一つのみ作成する
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestInstance;

/**
 * JUnit は各テスト毎にインスタンスを作成する
 * ただ、@TestInstance(TestInstance.Lifecycle.PER_CLASS)をつけると
 * 各テスト単位ではなくクラスに一つだけインスタンスを作成する
 * 下記のテストだと count を共有することになるため
 * テスト毎に count が増加していく
 */
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class JUnit5TestInstance {

    int count = 1;

    /**
     * クラスインスタンスを一つだけ作るため BeforeAll と AfterAll が static ではなくてもいい
     */
    @BeforeAll
    void beforeAll() {
        System.out.println(this);
        System.out.println(count++);
        System.out.println("JUnit5TestInstance beforeAll");
    }

    @AfterAll
    void afterAll() {
        System.out.println(this);
        System.out.println(count++);
        System.out.println("JUnit5TestInstance afterAll");
    }

    @Test
    void test1() {
        System.out.println(this);
        System.out.println(count++);
        System.out.println("JUnit5TestInstance test1");
    }

    @Test
    void test2() {
        System.out.println(this);
        System.out.println(count++);
        System.out.println("JUnit5TestInstance test2");
    }
}
```

### JUnit5TestMethodOrder.java
テストの順番を決める
```
package devhiroba.junit5.test;

import org.junit.jupiter.api.*;

/**
 * "@TestMethodOrder" と　"@Order" を使用して各テストの実行順番を制御できる
 */
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
public class JUnit5TestMethodOrder {
    @Order(3)
    @Test
    void test1() {
        System.out.println(this);
        System.out.println("JUnit5TestMethodOrder test1");
    }
    @Order(2)
    @Test
    void test2() {
        System.out.println(this);
        System.out.println("JUnit5TestMethodOrder test2");
    }
    @Order(1)
    @Test
    void test3() {
        System.out.println(this);
        System.out.println("JUnit5TestMethodOrder test3");
    }
}
```

### junit-platform.properties
アノテーション定義をテスト全体に一括適用する
```
# ここの定義はすべてのテストに一括適用される

# テストインスタンスのライフサイクル設定(@TestInstance(TestInstance.Lifecycle.PER_CLASS)と同じ)
junit.jupiter.testinstance.lifecycle.default = per_class

# @Disabled を無視して @Disabled が宣言されていても実行する
junit.jupiter.conditions.deactivate = org.junit.*DisabledCondition

# テストメソッドの表示名を変更する(@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)と同じ)
junit.jupiter.displayname.generator.default = \
  org.junit.jupiter.api.DisplayNameGenerator$ReplaceUnderscores
```

### GitHub Repository
[https://github.com/devhiroba/junit5-sample.git](https://github.com/devhiroba/junit5-sample.git)
