Docker Meetup Tokyo #26
=====

https://dockerjp.connpass.com/event/106358/

2018/11/21 @PLAID, Inc.


## Sponsor session(@algas)

- LOCUST https://locust.io/
- 負荷テスト
    - pros
        - 複数台から簡単に実行できる
        - レポート機能
    - cons
        - HTTP(S) しか対応できない
- k8s で LOCUST で負荷かけれるよ


## KubeCon China参加報告 (KubeCon China Recap) @superbrothers

https://speakerdeck.com/superbrothers/kubecon-plus-cloudnativecon-china-2018-recap

- Kubecon Recap
    - CNCF サーベイ
        - 58% の企業が kubernetes を本番利用
        - そのうち 40% が 5000人移譲の会社
    - kubernetes is boring
    - Harbor
        - cloud native registory
        - CNCF incubating project
        - コンテナイメージと Helm Chart のレジストリ
    - TiKV
    - Dragonfly
        - p2p ベースのコンテナ配信
        - 元 etcd 開発者
        - 必要な人
            - > 10GB 以上のコンテナとか
            - +1000 node
        - グルーピングで配信する node を
        - Java で書かれているけど、Go で書き直すよ
    - Kubernetes as a Service も多くなってる

## いま話題のいろいろなコンテナランタイムを比較してみた (Comparison of several container runtimes) @TokunagaKohei

- critools でパフォーマンス測定
    - 作成から破棄までのライフサイクル

- 高レイヤランタイム
    - CRI
    - containerd
        - Moby project / CNCF プロジェクト
        - 開発盛ん
        - CRI 対応プラグインにより CRI 経由で操作
        - shim を挟んで OCI を操作
    - cri-o
        - Node SIG Project, Red Hat 主導
        - 機能は最小限
        - CRI, Pot 指向
        - connmon デーモンが監視
    - rkt
        - CNCF project, CoreOS 主導
        - 最近盛り下がり
        - 低レイヤランタイムも内包
        - rkt は単発で実行するバイナリ
        - rktlet が kubelet を待つ
- 低レイヤランタイム
    - OCI
    - runc
        - OCI のリファレンス的実装
        - Linux のリソース分離, セキュリティ機能をフル活用
        - rootless もある
    - runsc(gVisor)
        - syscall を sentry というプロセスを挟んでハンドルする
        - 限定的なシステムコールを発行, 50種程度
        - 各アプリケーションは Go routeine として動作
        - スケジューリングも Go のランタイムがやる
    - runnc(Nabla Containers)
        - コンテナランタイム上で unikernel を実行する
        - systemcall が コンテナランタイムで裁く
        - 突き抜けるのも 7 種類しかない
        - unikernel ベースの image が実行可能
    - kata-runtime(Kata Containers)
        - pod 毎に VM を作る
        - カーネルの隔離は強い


## Docker Swarm-mode @_inductor_

- Swarm
    - CE 使いづらい
        - CNI 使えない
    - 管理画面も使いにくい
    - クラウドプロバイダの対応がほとんどない
    - Kubernetes 強すぎ問題
    - kubernetes 難しすぎ問題
        - Docker の知識だけじゃ足りなさすぎる
    - Docker だけの学習コストでだいたい足りる

## Docker v18.09の新機能の紹介 (Docker v18.09 new features) @_AkihiroSuda_

- Docker 18.09
    - BuildKit 正式採用
    - リモート Docker ホストの SSH
        - Docker socket の設定は大変
            - TLS の設定
            - 正しく設定できていないと root と同じ
        - Docker を install する前に SSH 設定されてるよね
    - devicemapper, overlay 非推奨
        - overlay2 推奨
    - json-file に変わる local log driver
        - Protocol Buffer を使う
- Docker 19.03, 19.09 予想
    - non root で Docker デーモンの実行
        - user namespace を用いる
        - コンテナだけではなくデーモンもユーザ権限で実行

## Multi-stage Docker Build @orisano

- https://speakerdeck.com/garethr/advanced-docker-image-build-patterns
- https://medium.com/@tonistiigi/advanced-multi-stage-build-patterns-6f741b852fae


- https://speakerdeck.com/orisano/multi-stage-builds-patterns-and-practice
- build 中に test を通す
- base image に alias を貼る
- 重い処理を 分けて cache を効かせる
- CI に Multistage Build はつらい

## LT: 中国AlibabaのManaged Containerのあれこれ @mosuke5

- https://www.slideshare.net/mosuke5/dockermeetup26-lt-alibaba-cloud

- 中の人の話

## LT: KubeCon China 関連 @mosuke5

- 中国 Ranchar 勢いある
- 金融でコンテナ使ってる

## LT: Web-Frontend deploy-flow with buildkit (experimental) @izumin5210

- https://speakerdeck.com/izumin5210/deploy-web-frontend-app-with-buildkit
- Web Architecture
    - kubernetes が index.html 返す
        - cloudfront 経由で S3 から js とか css を返している
- build 時間長い
    - webpack build とか
    - QA 用と production
- 環境毎に stage を分けている
- 単純に layer 分けるだけだとマシンパワーが足りない
