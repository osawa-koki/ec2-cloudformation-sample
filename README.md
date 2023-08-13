# ec2-cloudformation-sample

📛📛📛 CloudFormationでEC2を作成するサンプルです！  

![成果物](./docs/img/fruit.webp)  

## 開発環境の構築方法

最初にAWS CLIをインストールします。  
<https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/install-cliv2.html>  

以下のコマンドを実行して、AWS CLIのバージョンが表示されればOKです。  

```shell
aws --version
```

認証情報を設定します。  

```shell
aws configure
```

以下のように聞かれるので、適宜入力してください。

```shell
AWS Access Key ID [None]: アクセスキーID
AWS Secret Access Key [None]: シークレットアクセスキー
Default region name [None]: リージョン名
Default output format [None]: json
```

---

タグをプッシュすると、GitHub Actionsが実行されて、スタックがデプロイされます。  

| Name | Value |
| --- | --- |
| STACK_NAME | プロジェクト名 |
| AWS_ACCESS_KEY_ID | アクセスキーID |
| AWS_SECRET_ACCESS_KEY | シークレットアクセスキー |
| AWS_REGION | リージョン名 |
| SSH_PUBLIC_KEY | SSHの公開鍵 |

## 実行方法

```shell
# Ref: https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/using-cfn-cli-creating-stack.html
aws cloudformation deploy \
    --stack-name <stack-name> \
    --template <template> \
    --parameter-overrides <parameter-overrides>

# 例)
aws cloudformation deploy \
    --stack-name ec2-cloudformation-sample \
    --template ./template.yml \
    --parameter-overrides SSHPublicKey='hogehoge'
```

`SSHPublicKey`は、EC2にログインするための公開鍵です。  
以下のコマンドで作成できます。  

```shell
ssh-keygen -t rsa -b 4096
```

生成された`~/.ssh/id_rsa.pub`の中身を`SSHPublicKey`に指定します。  
ファイル名が異なる場合は、適宜読み替えてください。  

以下のコマンドで、EC2にログインできます。  

```shell
ssh -i ~/.ssh/id_rsa -l <UserName> <EC2のパブリックIP>
```

UserNameはデフォルト(Ubuntu系)では、`ubuntu`です。  
<https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/managing-users.html>を参考に、適宜読み替えてください。  

IPアドレスは、Outputで指定した`EC2PublicIP`を指定します。  
Outputで指定した値の取得方法は、後述します。  

---

`Output`で指定した値を取得するには、以下のコマンドを実行します。  

```shell
aws cloudformation describe-stacks --stack-name <stack-name> --query <query> --output <output> --no-cli-pager

aws cloudformation describe-stacks --stack-name ec2-cloudformation-sample --query "Stacks[].Outputs[?OutputKey=='SSHPublicKey'].OutputValue" --output text --no-cli-pager
aws cloudformation describe-stacks --stack-name ec2-cloudformation-sample --query "Stacks[].Outputs[?OutputKey=='EC2PublicIP'].OutputValue" --output text --no-cli-pager
```

削除するには、以下のコマンドを実行します。  

```shell
aws cloudformation delete-stack --stack-name <stack-name>

# 例)
aws cloudformation delete-stack --stack-name ec2-cloudformation-sample
```
