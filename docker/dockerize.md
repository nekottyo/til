Dockerize で依存コンテナを待つ
===

[dockerize](https://github.com/jwilder/dockerize) を使うと、他のコンテナのポートが listen するまでコマンド(CMD)を叩かない、ということが簡単に実現できる

Dockerfile
```Dockerfile
FROM: debian:latest

ENV DOCKERIZE_VERSION v0.6.1

RUN apt update && apt -y install \
    wget \
  && wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
  && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
  && rm dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
  && apt prune -y wget

COPY docker/docker-entrypoint.sh ./docker-entrypoint.sh
ENTRYPOINT ["./docker-entrypoint.sh"]
```

例えばこいつが mysql に依存している場合

docker-entrypoint.sh
```bash
#!/bin/bash

DOCKERIZE_TIMEOUT=${DOCKERIZE_TIMEOUT:-60}
MYSQL_HOST=${MYSQL_HOST:-mysql}
MYSQL_PORT=${MYSQL_PORT:-3306}

if ! hash dockerize 2>/dev/null; then
  echo "require dockerize" >&2
  exit 1
fi

dockerize -timeout "${DOCKERIZE_TIMEOUT}s" -wait-retry-interval 5s -wait "tcp://${MYSQL_HOST}:${MYSQL_PORT}"

exec "$@"
```

という entrypoint 用 script を作ると dockerize によって `tcp://mysql:3306` が listen するまで待って CMD を実行できる

```yaml
version: '3'

service:
  mysql:
    image: mysql

  sample:
    build:
      context: .
    depends_on:
    - mysql
    command: tail -f /dev/null
```
