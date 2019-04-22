BuildKit の cache 機能を使う
=====
apt add とか apk instal とか bundle install とか npm install とか go mod download を早くしたい

[Docker 18.09 新機能 (イメージビルド&セキュリティ)](https://medium.com/nttlabs/docker-v18-09-%E6%96%B0%E6%A9%9F%E8%83%BD-%E3%82%A4%E3%83%A1%E3%83%BC%E3%82%B8%E3%83%93%E3%83%AB%E3%83%89-%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3-9534714c26e2)


`DOCKER_BUILDKIT=1` を入れると、各 stage 毎はキャッシュが効くが、1 stage で COPY 後に RUN とかすると、RUN の以前の cache は使われない。

そこで、`--mount-type=cache,targer=/PATH/TO/CACHEDIR` を入れると、1つの RUN に対して cache が作られ、2回目以降の build でその cache が mount される。

---
## 使い方
1行目に呪文を入れる

cache したい RUN で、頭に `--mount=type=cache,target=/app/node_modules` (node の npm install なので WORKDIR/node_modules) を入れる

Dockerfile

```dockerfile
# syntax = docker/dockerfile:experimental

FROM node:latest as node
 
FROM node as npm
WORKDIR /app
 
COPY package.json package-lock.json .
RUN --mount=type=cache,target=/app/node_modules \
    npm install
```

## 実行してみる
初回は普通にかかる

```sh
$ docker build .
(snip)
 => [npm 4/4] RUN     npm install                                                                                                 185.8s
```

cache を使わない状態で、package.json の scripts 辺りを書き換えると、npm module の依存には直接関係ないのに、だいたい同じ時間の build が必要になる。

ところが、cache 機能を使うと 12 倍位早くなる。

```sh
$ docker build .
 => [npm 3/4] COPY package.json  package-lock.json .                                                                                1.1s
 => [npm 4/4] RUN --mount=type=cache,target=/app/node_modules     npm install                                                      15.1s
```


BuildKit 最高！！
