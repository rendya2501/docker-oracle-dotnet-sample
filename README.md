# docker-oracle-dotnet-sample

[DockerでOracleの環境構築 + .Net接続サンプル](https://zenn.dev/rendya/articles/docker_oracle_dotnet)  

## イメージ作成シェルの実行  

``` bash
./docker-images/OracleDatabase/SingleInstance/dockerfiles/buildContainerImage.sh -v 21.3.0 -x -i
```

## イメージの確認  

``` bash
docker images
```

## 作成 & 起動  

``` bash
docker-compose up -d
```

## 動作確認

``` bash
docker-compose logs
```

以下のログが確認出来ればOK  

``` log
#########################
DATABASE IS READY TO USE!
#########################
```

## プログラム実行  

``` bash
dotnet run --project ./src/DockerOracle.csproj
```

こんな出力がされたらOK。  
何回も実行するとIDが加算されていく。  

``` logs
ID: 1, Name: John Doe, Age: 30
ID: 2, Name: Jane Doe, Age: 25
```

## 後始末  

``` bash
# docker-compose stop or docker stop oracle21c
# docker-compose stop
docker stop oracle21c
docker rm oracle21c
docker rmi oracle/database:21.3.0-xe
docker volume rm docker-oracle-dotnet-sample_db-store
```
