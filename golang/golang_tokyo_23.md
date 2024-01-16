golang.tokyo #23
=====
https://golangtokyo.connpass.com/event/126673/


# 絶対に分かるポインタ / tenntenn
https://docs.google.com/presentation/d/1u93oMe7QForbqrCwGE2lHbu99t4I9_m5IzfeYETD7BU/edit

- ポインタ怖くない
- `&` でポインタ値を取得
- `*` でポインタ先を取得
- ポインタの加算減算が無い

- スライス
    - `append` は新たに作り直すから代入する必要ある

# Goをはじめるあたって知っておいてほしいツールやテスト/mom0tomo, micchiebear
https://speakerdeck.com/mom0tomo/golang-tokyo-go-tools

- golang.org を見よう
- golang weekly を読もう
- gophers slack 入ろう
- `go fmt`
    - `gofmt` 
        - `go fmt` のラッパー
- `go vet`
- `golint`

- test 周り
    - `testing`
    - `_test.go` は build 時には使われない
    - Table Driven Test いいぞ

# Delveを用いたデバッグ & pprofを用いたプロファイリング / 渡邉 光

- ヘッドフォン沼勢
- Delve
    - `runtime.Breakpoint()` で breakpoint できる
- pprof
    - `runtime/pprof`
    - `net/http/pprof`

# LT

- Buffalo https://gobuffalo.io/en
    - Rails ちっく
- goconvery https://github.com/smartystreets/goconvey
    - TDD で自分の変更が他のコードへの影響がないかわかるよね

- `go generate` はいいぞ
- `goreleaser` はいいぞ




