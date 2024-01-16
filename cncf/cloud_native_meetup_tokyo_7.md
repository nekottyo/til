Cloud Native Meetup Tokyo #7 @Abema Towers
=====
2019/03/29 @Abema Towers
https://cloudnative.connpass.com/event/123324/

https://twitter.com/hashtag/cloudnativejp?f=tweets&vertical=default&src=hash

## Telepresence ではじめる k8s 時代のローカル開発 @_shiro16 

https://www.telepresence.io/

- telepresence
    - telepresence が pod を hook して local に捻じ曲げる
    - 1 pod が local マシンに置き換わる
- build/push/ship が省ける
    - local で docker 上げるみたいなのがいらない
    - telepresence が k8s の一部として動くので、ネットワーク経路が同じににある
- require
    - http(s)
- local で起動したり docker コンテナでとして起動したり
- ハマりポイント
    - デフォルトで telepresence が port 80 を使うのでそれ以外で bind しなきゃいけない
    - 1 pod で複数コンテナが動いていると競合する
        - `--swap-deployment` で置き換えるコンテナを指定できる
    - `--docker-run` を使う場合
        - `-v` で mount すると結構遅い
        - docker-sync, cached option, docker メモリを増やすと改善する

## Introduction to Consul Kubernetes Integration and Consul Connect @r_takaishi

- Consul
    - Service Discovery
        - Heaclth check, DNS, Http interface
    - Service Segmentation
        - Consul Connect
    - Service Configration
        - KVS, transaction, watch
- ペパボ consul 使ってる
    - k8s 移行してるが、 いい感じに既存 consul と連携したい
- Consul Connect
    - 通信の許可/禁止
    - sidecar proxy
        - dataplane
        - Envoy にも置き換えれる
            - https://www.consul.io/intro/vs/proxies.html
    - CA もいい感じに管理してくれる
    - アプリケーション自身に proxy を組み込める
    - 簡単に service-to-service の暗号化、制御ができる
- consul kubernetes integration
    - consul cluster は k8s 跨いでクラスタを作れる
    - daemonset として consul client をデプロイすると、k8s node がそれぞれ Consul member となる
    - helm を使うと pod IP になって node IP じゃないので注意
    - 既存インフラに Consul cluster があるとよさそう
    - k8s <-> Consul で同期ができる
- istio + Envoy じゃなくて consul connect でいける？

## 分散イメージレジストリの検討 〜Beiran & Dragonfly〜 @yupeji @furkanmustafa
- Docker pull 遅すぎ問題
- Dragonfly
	- https://github.com/dragonflyoss/Dragonfly
	- Alibaba
	- P2P based image delivery
	- dfget
		- dragonfly client
	- supernode
		- Tracker
		- Scheduler
		- Cache server
	- document が中国語
- Kraken
	- https://github.com/uber/kraken
	- Uber
	- P2P based image registry
- Beiran
	- https://gitlab.beiran.io/beiran/beiran
		- クリエーションラインだから GitLab なのか
	- 隣の人が image 持ってたらそれを引っ張れる

