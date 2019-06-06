## 環境

* docker 2.0.0.3
   └ node v11.1

## Started 

```
# git clone
$ git@github.com:RyunosukeOguri/nuxt-docker.git

# dockerイメージを構築する
$ docker-compose build

# パッケージをインストールする (ホストのフォルダをマウントする場合、初回必要)
$ docker-compose run app yarn install

# コンテナを起動する
$ docker-compose up -d

# サーバのログ確認
$ docker-compose logs -f --tail 10 app

# ブラウザで確認
$ open http://localhost:5001
```

#### Lintチェック
```
$ docker-compose run app yarn run lint --fix
```
