static link な binary を build する
=====

https://github.com/golang/go/issues/26492#issuecomment-435462350
(Go 1.13 からは `go build -static` でよくなるかも？)


基本的には `-tags netgo -ldflags '-extldflags "-static"'` があれば良さそう。

```bash
$ CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
  go build -tags netgo -ldflags '-s -w -extldflags "-static"'
```

単一環境ではなくクロスコンパイルは [mitchellh/gox](https://github.com/mitchellh/gox) を使う。
ちなみにコンテナ環境での `FROM scratch` へのバイナリデプロイは、上記のように linux_amd64 で static link にすればよい。
