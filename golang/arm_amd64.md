# arm64 な Mac で amd64(x86_64) Go を使う


https://twitter.com/ch11aki/status/1393828888325746690


好きな version の `darwin-arm64..tar.gz` を選ぶ
https://go.dev/dl/


```
❯ wget https://go.dev/dl/go1.17.11.darwin-amd64.tar.gz
❯ tar zxvf go1.17.11.darwin-amd64.tar.gz
❯ mkdir /Users/shimizu-kotaro/.anyenv/envs/goenv/versions/<VERSION>
❯ mv go/* /Users/shimizu-kotaro/.anyenv/envs/goenv/versions/<VERSION>
```

これでいい感じに goenv にも読める

```
❯ goenv version
1.17.11 (set by /Users/shimizu-kotaro/.anyenv/envs/goenv/version)
❯ go version
go version go1.17.11 darwin/amd64
```

https://github.com/kubernetes-sigs/controller-runtime/issues/1657#issuecomment-988484517
