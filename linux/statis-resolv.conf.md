# /etc/resolv.conf を固定化したい

## 動機
手元で cns(unbound) を立てている時に再起動や `NetworkManager restart` により `/etc/resolv.conf` が `127.0.0.1` を向いてしまう
すると、docker container を立てた時の `/etc/resolv.conf` は外に疎通を持つために google public dns `8.8.8.8` を向く
proxy 配下環境にいるときは外に出れなくなるので host に向けたい

## 方法

`/etc/NetworkManager/NetworkManager.conf` の `[main]` セクション以下に  `dns=none` を追加する

`sudo vim /etc/NetworkManager/NetworkManager.conf`
```
[main]
dns=none
```

## 参考
- [https://docs.docker.com/engine/userguide/networking/default_network/configure-dns/]
- [https://askubuntu.com/questions/623940/network-manager-how-to-stop-nm-updating-etc-resolv-conf]
- [https://qiita.com/hirosemi/items/0574a4b044f46c6ac5af]


