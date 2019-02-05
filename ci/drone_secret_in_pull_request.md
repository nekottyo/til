Drone の secret を pull_request 時にも使う
=====

Drone v0.8 の場合

```bash
$ drone -version
drone version 0.8.6
```

---

WebUI から追加出来る secret はデフォルトで PUSH, TAG, DEPLOYMENT で有効だが、 PULL_REQUSET では有効ではない。

PR で有効にしてあげるには WebUI からは出来ないようであり、 cli tool で設定し直す必要がある

remote を設定する
```bash
export DRONE_SERVER=https://example.drone.com
export DRONE_TOKEN=eyJYOURTOKEN
```

secret の event ごと上書きする

```bash
$ drone secret update \
  --repository your/repository \
  --name secret_name \
  --value secret_value \
  --event push \
  --event tag \
  --event deployment \
  --event pull_request
```
