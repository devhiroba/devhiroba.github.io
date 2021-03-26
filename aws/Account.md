# Account
[https://aws.amazon.com/jp/iam/](https://aws.amazon.com/jp/iam/)

## AWS アカウントの種類
- ルートユーザーと IAM ユーザーがあります。
- 「新しい AWS アカウントの作成」にてルートユーザーを作成します。
- ルートアカウントでログインして IAM ユーザーを作成します。
- ルートユーザーは IAM ユーザーを作成する時にだけ利用し、AWS リソースを利用する作業は IAM ユーザーを利用するようにしましょう。

## ルートユーザー 
![Test Image 3](/resource/image/AWS_Account_RootUser.png)

- 複数人でルートユーザーを共有するとセキュリティーの問題が発生します。

## IAM ユーザー
![Test Image 3](/resource/image/AWS_Account_IAMUser.png)

- メンバー毎に各 IAM ユーザーを作成してそれぞれ必要な権限のみ付与することができます。 
