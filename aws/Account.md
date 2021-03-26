# Account
[https://aws.amazon.com/jp/iam/](https://aws.amazon.com/jp/iam/)

## AWS アカウントの種類
- ルートユーザーと IAM ユーザーがあります。
- 「新しい AWS アカウントの作成」にてルートユーザーを作成します。
- ルートアカウントでログインして IAM ユーザーを作成します。

## ルートユーザー 
- 複数人でルートユーザーを共有するとセキュリティーの問題が発生します。
![Test Image 3](/resource/image/AWS_Account_RootUser.png)

## IAM ユーザー
- メンバー毎に各 IAM ユーザーを作成してそれぞれ必要な権限のみ不要することができます。 
![Test Image 3](/resource/image/AWS_Account_IAMUser.png)
