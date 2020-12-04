# ArchUnit
[https://github.com/TNG/ArchUnit](https://github.com/TNG/ArchUnit)

## ArchUnit とは
アーキテクチャのテストを行うライブラリです。  
複数人で開発するプロジェクトの場合、特にベンダーさんに開発を依頼した場合、結果物が設計時に想定したアーキテクチャ通りにならない可能性があります。その時に使用できるのが ArchUnit です。ArchUnit ライブラリを使用して下記のようなチェックができます。すごく便利だと思います。
- パッケージやクラス間の依存関係チェック
- クラスのパッケージングのチェック
- 循環参照のチェック←特にこれは絶対にやめでほしい！

## 単体テストで ArchUnit を使用する方法
開発環境
- Java
- Gradle
- IntelliJ
- JUnit5
- ArchUnit

### build.gradle
JUnit5 と ArchUnit を使用する為、build.gradle の 設定をする
```
```

### プロジェクトの構成
![Test Image 3](/resource/image/archunit-sample-image.png)

### UserModel
```

```

### UserModel
```

```

### GitHub Repository
[https://github.com/devhiroba/archunit-sample.git](https://github.com/devhiroba/archunit-sample.git)
