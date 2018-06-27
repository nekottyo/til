スリムな Rails image を Docker Multi Stage Build で作る
=====

Rails 5.1 + webpack, Ruby 2.5.1 な imageを作る

bundle と asset pipeline & webpack 用の builder image と その成果物を COPY --from=builder で必要な部分だけ抜き、小さめな 実行用 imageを multi stage build で作る

- bundle install の成果物は /usr/local/bundle
- nodoe_modules は precompile にしか使わないので  /public/asseert だけ引き抜く
- webpack の成果物も /public/packs だけ持つ
- 実行用 image は js runtime としての node と mysql-client 位しか入れない

```Dockerfile
FROM ruby:2.5.1-slim-stretch as builder

RUN apt update \
    && apt -y install \
        build-essential \
        libmariadbd-dev \
        curl \
        git \
    && apt clean

RUN apt install apt-transport-https -y \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt update \
    && apt install -y \
        yarn \
        nodejs \
    && apt upgrade -y

WORKDIR /app
COPY Gemfile ./Gemfile
COPY Gemfile.lock ./Gemfile.lock
RUN bundle install -j4 --without test development

COPY package.json ./package.json
RUN npm install
COPY yarn.lock ./yarn.lock
RUN yarn

COPY ./ .
RUN bin/webpack \
    && RAILS_ENV=production rake assets:precompile  # precompile assets


FROM ruby:2.5.1-slim-stretch
ENV LANG C.UTF-8

RUN apt update && \
    apt install -y --no-install-recommends\
        default-libmysqlclient-dev \
        gnupg2 \
        curl \
    && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt install -y \
        nodejs \
    && apt remove -y --auto-remove\
        gnupg2 \
        curl \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /usr/local/bundle  /usr/local/bundle
COPY --from=builder /app/public/packs /app/public/packs
COPY --from=builder /app/public/assets /app/public/assets

WORKDIR /app
COPY ./ .
```
