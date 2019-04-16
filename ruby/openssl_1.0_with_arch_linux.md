Arch Linux で openssl 1.0 系を使う ruby とか gem を install する
=====

ひとまず openssl-1.0 を入れる

```sh
$ yarout openssl-1.0
```

## Ruby (rbenv)

```sh
$ PKG_CONFIG_PATH=/usr/lib/openssl-1.0/pkgconfig rbenv install 2.3.4
```

## Bundle

引っかかったのは古い Puma

https://github.com/puma/puma/issues/1136#issuecomment-363783318


gem 直なら

```
$ gem install puma -v '2.16.0' -- --with-cppflags=-I/usr/include/openssl-1.0 --with-ldflags=-L/usr/lib/openssl-1.0
```

bundler 経由なら

```
$ bundle config --local build.puma "--with-cppflags=-I/usr/include/openssl-1.0 --with-ldflags=-L/usr/lib/openssl-1.0"
$ bundle install
```
