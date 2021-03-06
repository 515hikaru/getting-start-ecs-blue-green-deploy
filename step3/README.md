# Step 3
## ECR
リポジトリを作成します。  
今回は2つ作成してください。  

* tomcat10
* nginx1192

```
export AWS_DEFAULT_REGION=ap-northeast-1

aws ecr create-repository --repository-name tomcat10
aws ecr create-repository --repository-name nginx1192
```

以下のコマンド実行し **Login Succeeded** と表示されれば成功です。  

```
eval $(aws ecr get-login --no-include-email --region ap-northeast-1)
```

## Dockerfile
作業用の任意のディレクトリを作成します。  
そのディレクトリ配下を以下のような構成にします。  
ファイルはこれから作成します。ファイル作成時に Cloud9 の素晴らしさが実感できるはずです。ssh で作業することとの違いを感じてもらえれば嬉しいです。  

```
.
├── docker-compose.yml
├── nginx
│   ├── Dockerfile
│   ├── conf
│   │   └── default.conf
│   └── html
│       └── index.html
└── tomcat
    ├── Dockerfile
```

### docker-compose.yml
[docker-compose.yml](docker-compose.yml) ファイルを作成します。  
(account_id はご自身のものへ変更)    

### nginx/Dockerfile
Docker Hub の公式イメージを使用します。  
[nginx/Dockerfile](nginx/Dockerfile) ファイルを作成します。  

### nginx/conf/default.conf
フロント用 nginx です。  
80番ポートで受付、裏側の tomcat に流しています。  

[nginx/conf/default.conf](nginx/conf/default.conf) ファイルを作成します。  

### nginx/html/index.html
動作確認用の index.html です。  
[nginx/html/index.html](nginx/html/index.html) ファイルを作成します。  

### tomcat/Dockerfile
Docker Hub の公式イメージを使用します。  
サンプルの war をデプロイします。  
[tomcat/Dockerfile](tomcat/Dockerfile) ファイルを作成します。  

### ローカルで動作確認
ファイル一式の作成が完了したらローカルで動作確認をします。  
docker-compose.yml が存在するディレクトリで以下のコマンドを実行します。  

```
docker-compose up -d --build

docker-compose ps
  Name                 Command               State           Ports         
---------------------------------------------------------------------------
web_1       /docker-entrypoint.sh ngin ...   Up      0.0.0.0:80->80/tcp    
tomcat_1    catalina.sh run                  Up      0.0.0.0:8080->8080/tcp
```

上記のように2つのコンテナが State = Up と表示されれば OK です。  

アクセスしてみましょう。  

```
curl http://localhost/index.html
～～省略～～
<body>
    <h1>HTML Sample</h1>
</body>
～～省略～～


curl http://localhost:8080/sample/
～～省略～～
<h1>Sample "Hello, World" Application</h1>
<p>This is the home page for a sample application used to illustrate the
source directory organization of a web application utilizing the principles
outlined in the Application Developer's Guide.
～～省略～～
```

うまく動かない、コンテナ内部を調べたい、そんな場合は以下のコマンドです。  

```
docker exec -it container_name  bash
```

特に問題がなければ停止しておきます。  
```
docker-compose down
```

### ECR へプッシュ
正常に動作するイメージは ECR へプッシュします。  
後ほど ECS 上で起動してもらいます。  

```
eval $(aws ecr get-login --no-include-email --region ap-northeast-1)
docker-compose build
docker-compose push
```

----

<< [Step2](../step2/README.md)  |  [Step4](../step4/README.md)  >>
