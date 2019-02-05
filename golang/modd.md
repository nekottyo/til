modd による hot reload
=====

[cortesi/modd](https://github.com/cortesi/modd) を使うと気軽に hot reload が実現できる

`prep` に実行したいコマンドを書く。`prep` は複数行書け、上から順に実行される
`daemon` には server として上げ続けたり、 debugger 等を設定する

```conf
**/*.go {
    prep: go test ./...
    pgep: go fmt ./...
    daemon +sigterm: go run .
    daemon +sigterm: dlv debug
}
```


