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

## 単体テストで DBUnit を使用する方法

### build.gradle
JUnit5 と DBUnit を使用する為、build.gradle の 設定をします
```

```

### プロジェクトの構成
![Test Image 3](/resource/image/dbunit-sample-image.png)

### JUnit5Test
Junit5の基本アノテーション
```

```

### Junit5TestDisplayName
テストメソッドの名前を変更
```

```

### Junit5TestAssertion
データの比較方法
```

```

### Junit5TestAssume
条件によってテストの実行可否を制御
```

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
