Cloud Native Meetup Tokyo #4
===

- 2018/09/11
- サイバーエージェント



## Istio 1.0とEnvoyの開発最前線

- @lizan(Google Cloud)
    - Istio
    - Envoy
    - Extensible Service Proxy for Cloud Endpoints
- Istio
    - 1.0
        - https://istio.io/about/notes/1.0/
        - コンテナ間の通信を mTLS する
            - permissive mode にすると mTLS されていれば解くし、そうじゃなければそのまま繋げる
        - 細かい認可の仕組みが policy で定義できる
        - gGPC のインタフェースを定義視すると Mixer が使ってくれる
- Envoy
    - performance, L4/L8 network proxy, pluggable filter chain model
    - master is always RC
        - Lyft とか couple は weekly くらいでデプロイされているらしい
    - Envoy のtaggingはあまり気にしなくていいよ
    - Place to watch
        - `DEPLICATED.md`
        - What happend
            - `version_history.rst`
            - data-plane-api repo
        - label "design proposal"
    - xDS v1 config and API turndown
        - 1.8 でなくなるよ
    - xDS v2 API
    - xDS
        - LDS Listener Discovery Service
            - 通信を listen したいやつ
            - 1インスタンスが 1サーバ
        - RDS Route Discovery Service
            - HTTP, HTTPS gRPC だけ
            - RDS 毎に異なるサーバから持ってこれる
        - CDS Cluster Discovery Service
            - Cluster の後ろにいるのを探す
            - 1インスタンスが 1サーバ
        - EDS Endopint
            - それぞれの endopoint を envoy に配布するやつ
        - SDS Secret
            - 暗号化通信をするときに秘密鍵と証明書を配布する
            - 別のルートから 鍵と証明書を配布するしくみ
            - ingress controller の証明書更新されるとIstio が hot restart していたけど、それが再起動無しで適用できるようになる
        - HDS Health
            - envoy 同士で health check する
            - envoy 数 * endpoint でめっちゃ増えるのが問題
            - HDS で envoy が見る Endpoint を分けて、HDS で aggregate する
        - Aggregated ADS
            - 複数の xDS をまとめて一貫性をもたせる
        - UDS Unix Domain Socket(discovery service じゃない)
    - 今後
        - L7 プロトコルサポート
        - scalability from improved xDS
        - Extention mechanism
    - 基本的に Envoy で使えるのは Istio で使える
        - Istio で一段抽象化する場合は時間かかるかも


## Open Policy Agent is 何？

- Open Policy Agent
    - 文脈は k8s とは限らない
    - RBAC is HARD
        - 実際の業務と RBAC の config が違うよね
        - オペレーションも辛い
        - 適用先もいっぱい
            - AD から AWS, GKE, onelogin まである
            - 即適用でミスったら大変
        - team もいっぱいある
    - https://www.openpolicyagent.org/
        - https://www.cncf.io/blog/2018/03/29/cncf-to-host-open-policy-agent-opa/
    - ポリシーの適用とビジネスロジックを分離する
- OPA の動き方
    - Data
        - Base Document
            - In memory and/or Disk
            - JSON
            - Restful API
                - DATA API
                    - ポリシーが適用された結果のデータ
    - Policy
        - Written in Rego
- Deploy
    - Go
    - daemon
    - container
- UseCase
    - Web-Service: 人事の給与サービス
        - 自分は自分の給与が見れる
        - manager は 自分と member が見れる
        - 人事は全部見れる
    - Docker
        - docker の authorization plugin として opa を指定できる
        - docker コマンドが apply される前に、認可処理が走る
        - https://github.com/open-policy-agent/opa-docker-authz
