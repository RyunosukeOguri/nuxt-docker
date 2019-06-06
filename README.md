## 環境

* docker 2.0.0.3
-   └ node v11.1

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



# NUXT × Docker環境の作成方法

※PORTは5001を使っています。自由に変えてOK

### 1.NUXTのソースディレクトリを作成する
```
$ mkdir docker/nuxt.js/src/app
```

### 2.Dockerfileファイルを作成する
```
$ touch docker/nuxt.js/Dockerfile
```

Dockerfileに追記
```
FROM node:11.1

# locale & timezone (Asia/Tokyo)
# https://github.com/moby/moby/issues/12084
ENV LANG C.UTF-8
ENV TZ Asia/Tokyo

# system update
RUN apt-get update && \
    apt-get install -y vim less

WORKDIR /root

# install yarn command.
# - https://yarnpkg.com/lang/ja/docs/install/#alternatives-stable
# - https://github.com/yarnpkg/yarn/releases
ARG CMD_YARN_VERSION=1.13.0
RUN npm install --global yarn@$CMD_YARN_VERSION && \
    chmod +x /usr/local/bin/yarn

# install direnv command.
# - https://github.com/direnv/direnv
# - https://github.com/direnv/direnv/releases
ARG DEV_DIRENV_VERSION=v2.19.0
RUN wget -O direnv https://github.com/direnv/direnv/releases/download/$DEV_DIRENV_VERSION/direnv.linux-amd64 && \
    mv direnv /usr/local/bin/ && \
    chmod +x /usr/local/bin/direnv && \
    echo 'eval "$(direnv hook bash)"' >> ~/.bashrc

# install vue-cli
RUN npm install --global @vue/cli @vue/cli-init

# copy application code from host.
ADD src /src
WORKDIR /src/app
```

### 3.docker-compose.ymlを作成する
```
$ touch docker-compose.yml
```

docker-compose.ymlに追記

```
version: '3'
services:
  app:
    container_name: project_app
    build:
      context: ./docker/nuxt.js
      dockerfile: Dockerfile
    ports:
      - 5001:5001
    volumes:
      - ./docker/nuxt.js/src:/src:cached
      # exclude volumes
      - /src/app/node_modules
    tty: true
    stdin_open: true
    # Hot Module Replacement (HMR) is enable for virtual box.
    environment:
      - CHOKIDAR_USEPOLLING=true
```


### 4.dockerをビルドする
```
$ docker-compose build
```

### 5.nuxt.jsをインストールする

※docker-compose run でdocker内でnuxtをインストールできます。

```
$ docker-compose run app npx create-nuxt-app .
```

### 6.package.jsonのscript `dev: “nuxt”` の部分を `HOST=0.0.0.0 PROT=5001 nuxt`と修正する

```
{
  "name": "app",
  "version": "1.0.0",
  "description": "Nuxt.js project",
  "author": "Ryunosuke Oguri",
  "private": true,
  "scripts": {
    "dev": "HOST=0.0.0.0 PORT=5001 nuxt", //追記
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate",
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
    "precommit": "npm run lint",
    "test": "jest"
  },
  ...
```

### 7.Dockerfileを修正

下記をDockerfileの一番下に追記します

docker/nuxt.js/Dockerfile
```
## install packages.
RUN yarn install

EXPOSE 5001
CMD ["yarn", "run", "dev"]
```

### 8.dockerイメージ再構築

```
$ docker-compose build
```

### 9.サーバー起動
```
# コンテナ起動
$ docker-compose up -d

# サーバの状態確認
$ docker-compose ps

# サーバのログ確認
$ docker-compose logs -f --tail 10 app

# ブラウザで確認
$ open http://localhost:5001
```


## その他

#### ESLintのconsole.log許可
```
module.exports = {
  ...
  extends: [
    '@nuxtjs',
    'standard', //追記
  ],
  
  ...
  
  // add your custom rules here
  rules: {
    'comma-dangle': ['error', 'only-multiline'], //追記
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off', //追記
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off' //追記
  }
}
```
