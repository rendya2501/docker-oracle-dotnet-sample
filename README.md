# docker-oracle-dotnet-sample

[DockerでOracleの環境構築 + .Net接続サンプル Ver2](https://zenn.dev/rendya/articles/docker-oracle-dotnet)  

全てVSCode上で実行する前提とする。  

## プロジェクト構成

``` txt
docker-oracle-dotnet-sample/
├── docker-images/
│   └── etc...
├── src/
│   ├── .gitignore
│   ├── DockerOracle.csproj
│   └── Program.cs
├── docker-compose.yml
└── README.md
```

## リポジトリのクローン

``` bash
git clone https://github.com/oracle/docker-images.git
```

## ORACLE EXPRESS EDITIONのダウンロード

[Oracle Database Express Edition (XE) Downloads | Oracle 日本](https://www.oracle.com/jp/database/technologies/xe-downloads.html)  

`Oracle Database 21c Express Edition for Linux x64 ( OL7 )`を選択してダウンロード。

## リポジトリにダウンロードしたOracleを配置

`oracle-database-xe-21c-1.0-1.ol7.x86_64.rpm`ファイルを以下のディレクトリに配置。  

``` txt
docker-images\OracleDatabase\SingleInstance\dockerfiles\21.3.0
```

## イメージ作成シェルの実行  

``` bash
./docker-images/OracleDatabase/SingleInstance/dockerfiles/buildContainerImage.sh -v 21.3.0 -x -i
```

## イメージの確認  

``` bash
docker images
```

以下のログが確認出来ればOK。  

``` logs
REPOSITORY        TAG         IMAGE ID       CREATED          SIZE
oracle/database   21.3.0-xe   ce44266e6a52   53 seconds ago   6.54GB
```

## ymlファイル作成

プロジェクトのルートディレクトリに `docker-compose.yml` を作成します。

``` yml
version: '3'

services:
  db:
    image: oracle/database:21.3.0-xe
    container_name: oracle21c
    ports:
      - 1521:1521
    volumes:
      - db-store:/opt/oracle/oradata
    environment:
      - ORACLE_PWD=passw0rd
volumes:
  db-store:
```

## 作成 & 起動  

``` bash
docker-compose up -d
```

因みに `docker-compose.yml` を生成しないで、直接で実行する場合は以下のコマンドになります。  

``` bash
docker run -d \
  --name oracle21c \
  -p 1521:1521 \
  -v db-store:/opt/oracle/oradata \
  -e ORACLE_PWD=passw0rd \
  oracle/database:21.3.0-xe
```

各オプションの説明  

- -d: コンテナをデタッチモードで実行します（バックグラウンドで実行）。  
- --name oracle21c: コンテナの名前を oracle21c に設定します。  
- -p 1521:1521: ホストのポート 1521 をコンテナのポート 1521 にマッピングします。  
- -v db-store:/opt/oracle/oradata: ボリューム db-store をコンテナの /opt/oracle/oradata ディレクトリにマウントします。  
- -e ORACLE_PWD=passw0rd: 環境変数 ORACLE_PWD を passw0rd に設定します。  
- oracle/database:21.3.0-xe: 使用するDockerイメージを指定します。  

## 動作確認

``` bash
docker-compose logs
```

以下のログが確認出来ればOK。  

``` log
#########################
DATABASE IS READY TO USE!
#########################
```

こちらのコマンドでも確認可能。  

``` bash
docker logs oracle21c
```

## プログラム実行  

``` bash
dotnet run --project ./src/DockerOracle.csproj
```

以下の出力を確認できればOK。  

``` logs
Connecting to Oracle...
Connection successful!

Create Table:
Create Table successful!

Insert Data:
ID: 1, Name: John Doe, Age: 30
ID: 2, Name: Hoge Fuga, Age: 25
ID: 3, Name: Piyo Piyo, Age: 20

Update Data:
ID: 1, Name: UPDATEEEEEEEEEEEEEEE, Age: 35
ID: 2, Name: Hoge Fuga, Age: 25
ID: 3, Name: Piyo Piyo, Age: 20

Delete Data:
ID: 1, Name: UPDATEEEEEEEEEEEEEEE, Age: 35
ID: 2, Name: Hoge Fuga, Age: 25

Drop Table:
Drop Table successful!
```

## 後始末  

``` bash
# サービス(コンテナ)を停止
docker stop oracle21c
# コンテナの削除
docker rm oracle21c
# イメージの削除
docker rmi oracle/database:21.3.0-xe
# ボリュームの削除
docker volume rm docker-oracle-dotnet-sample_db-store
```

サービス(コンテナ)の停止は `docker-compose stop` でも可。  

- `docker-compose stop`  
docker-compose.yml ファイルに定義されたすべてのサービスを停止します。  
例えば、docker-compose.yml に複数のサービスが定義されている場合、これらすべてのサービスを一度に停止します。  
- `docker stop oracle21c`  
名前が oracle21c の特定のコンテナを停止します。  
docker-compose.yml に関係なく、指定されたコンテナのみを停止します。  
