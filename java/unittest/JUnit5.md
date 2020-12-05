# JUnit5
[https://junit.org/junit5/docs/current/user-guide/](https://junit.org/junit5/docs/current/user-guide/)  
[https://oohira.github.io/junit5-doc-jp/user-guide/](https://oohira.github.io/junit5-doc-jp/user-guide/)

## JUnit とは
Javaで書かれたプログラムのテストを自動化してくれるテスティングフレームワークです。  
テストケースを作って置けばビルドするたびにテストしてくれます。

## JUnit5 の構成
![JUnit5-diagram-image](/resource/image/junit5-diagram-image.png)
- JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage
- JUnit Platform：テストを実行させるランチャーを提供、TestEngine API を提供
- Jupiter：JUnit Platform の TestEngine API を実装して JUnit5 用 TestEngine API を提供
- Vintage：JUnit Platform の TestEngine API を実装して JUnit3 と Junit4 用 TestEngine API を提供

## 単体テストで DBUnit を使用する方法

### build.gradle
JUnit5 と DBUnit を使用する為、build.gradle の 設定をします
```

```

### docker-compose.yml
Oracle Database の Docker コンテナを使用する為、docker-compose.yml を定義します
```

```

### プロジェクトの構成
![Test Image 3](/resource/image/dbunit-sample-image.png)

### DBUnitTest
```

```

### seed-data-each.xml
```

```

### GitHub Repository
[https://github.com/devhiroba/dbunit-sample.git](https://github.com/devhiroba/dbunit-sample.git)
