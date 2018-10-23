alpine の apk updage && apk add と apk add --no-cache
===

```dockerfile
FROM alpine

RUN apk update && apk upgrade && apk add nginx && rm /var/cache/*
```

みたいな Dockerfile を書いていたが、https://github.com/hadolint/hadolint/wiki/DL3017 を見ると、

`apk upgrade` は 本質的ではないパッケージが多数アップグレードされるので非推奨


また、https://github.com/hadolint/hadolint/wiki/DL3019 では

`apk update && apk add hoge && rm /var/cache/*` ではなく `apk add --no-cache` が推奨である。

