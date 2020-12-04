# ArchUnit
[https://github.com/TNG/ArchUnit](https://github.com/TNG/ArchUnit)

## ArchUnit とは
アーキテクチャのテストを行うライブラリです。  
複数人で開発するプロジェクトの場合、特にベンダーさんに開発を依頼した場合、結果物が設計時に想定したアーキテクチャ通りにならない可能性があります。その時に使用できるのが ArchUnit です。ArchUnit ライブラリを使用して下記のようなチェックができます。すごく便利だと思います。
- パッケージやクラス間の依存関係チェック
- クラスのパッケージングのチェック
- 循環参照のチェック ←特にこれは絶対にやめでほしい！

## 単体テストで ArchUnit を使用する方法
開発環境
- Java
- Gradle
- IntelliJ
- ArchUnit
- JUnit5

JUnit5 からは ArchUnit でアノテーションが利用できるようになり、コードかもっと綺麗に書けるようになりました！

- @AnalyzeClasses：クラスを読み込んでチェックするパッケージを設定
- @ArchTest：チェックするルールを定義

### build.gradle
JUnit5 と ArchUnit を使用する為、build.gradle の 設定をする
```
dependencies {
    // ArchUnit
    testImplementation('com.tngtech.archunit:archunit-junit5-engine:0.14.1')
}

// use JUnit 5 platform
test {
    useJUnitPlatform()
}
```
### プロジェクトの構成
![Test Image 3](/resource/image/archunit-sample-image.png)

### プロジェクトのアーキテクチャ
![Test Image 3](/resource/image/archunit-diagram-image.png)

### ArchUnitPackageTest
```
package devhiroba.archunit.test;

import com.tngtech.archunit.junit.AnalyzeClasses;
import com.tngtech.archunit.junit.ArchTest;
import com.tngtech.archunit.lang.ArchRule;
import devhiroba.archunit.Application;

import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.classes;
import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.noClasses;
import static com.tngtech.archunit.library.dependencies.SlicesRuleDefinition.slices;

/**
 * Application.class がある場所のパッケージをテスト対象にする
 * ここでは devhiroba.archunit パッケージの直下のすべてのパッケージがテスト対象になる
 */
@AnalyzeClasses(packagesOf = Application.class)
public class ArchUnitPackageTest {

    /**
     * repository パッケージにあるクラスを参照できるのは repository, service パッケージのみ
     * 必ず参照しなけらばならないわけではない（参照しなくてもよい）
     */
    @ArchTest
    ArchRule repositoryPackageRule = classes().that().resideInAPackage("..repository..")
            .should().onlyBeAccessed().byClassesThat()
            .resideInAnyPackage("..repository..", "..service..");

    /**
     * domain パッケージにあるクラスは controller パッケージを参照禁止
     */
    @ArchTest
    ArchRule domainPackageRule = noClasses().that().resideInAPackage("..domain..")
            .should().accessClassesThat().resideInAPackage("..controller..");

    /**
     * controller 以外のパッケージからは controller パッケージを参照禁止
     * controller パッケージは controller パッケージのみ参照可能
     */
    @ArchTest
    ArchRule controllerPackageRule = noClasses().that().resideOutsideOfPackage("..controller..")
            .should().accessClassesThat().resideInAnyPackage("..controller..");

    /**
     * 循環参照チェック
     * devhiroba.archunit パッケージの直下にあるパッケージ( controller, domain, repository, service )は循環参照禁止
     */
    @ArchTest
    ArchRule freeOfCycles = slices().matching("..archunit.(*)..")
            .should().beFreeOfCycles();

}
```

### ArchUnitClassTest
```
package devhiroba.archunit.test;

import com.tngtech.archunit.junit.AnalyzeClasses;
import com.tngtech.archunit.junit.ArchTest;
import com.tngtech.archunit.lang.ArchRule;
import devhiroba.archunit.Application;

import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.classes;
import static com.tngtech.archunit.lang.syntax.ArchRuleDefinition.noClasses;

/**
 * Application.class がある場所のパッケージをテスト対象にする
 * ここでは devhiroba.archunit パッケージの直下のすべてのパッケージがテスト対象になる
 */
@AnalyzeClasses(packagesOf = Application.class)
public class ArchUnitClassTest {
    /**
     * クラス名が Controller で終わるクラスは Service, model で終わるクラスを参照可能
     * 必ず参照しなければならない（各 Controller クラスで Service 又は model クラスを参照しないとエラーになる）
     */
    @ArchTest
    ArchRule controllerClassRule = classes().that().haveSimpleNameEndingWith("Controller")
            .should().accessClassesThat().haveSimpleNameEndingWith("Service")
            .orShould().accessClassesThat().haveSimpleNameEndingWith("domain");

    /**
     * クラス名が Repository で終わるクラスは Service, Controller で終わるクラスを参照不可
     */
    @ArchTest
    ArchRule repositoryClassRule = noClasses().that().haveSimpleNameEndingWith("Repository")
            .should().accessClassesThat().haveSimpleNameEndingWith("Service")
            .orShould().accessClassesThat().haveSimpleNameEndingWith("Controller");

    /**
     * クラス名が Controller で終わるクラスは controller パッケージに作成する
     */
    @ArchTest
    ArchRule studyClassesRule = classes().that().haveSimpleNameStartingWith("Controller")
            .should().resideInAnyPackage("..controller..");
}
```

### GitHub Repository
[https://github.com/devhiroba/archunit-sample.git](https://github.com/devhiroba/archunit-sample.git)
