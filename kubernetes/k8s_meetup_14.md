Kubernetes Meetup Tokyo #14
=====

https://k8sjp.connpass.com/event/104450/

2018/11/08 @Google

# Quipper のマイクロサービス化への道のり (30min)


- Quipper
    - 教育サービス
    - 先生が宿題を出して、生徒がとくとその進捗をオンラインで管理する
    - 日本ではスタディサプリ
    - 受験サプリをリブランドした
- Quipper SRE
    - 東京 3
    - マニラ 1
    - ロンドン1

- Kubernetes への移行
    - CTO のトップダウンで移行が始まった
    - Deis(Heroku clone) から Kubernetes へ
    - Microservice 化はまだ

- Quipper
    - 2012~ heroku
    - 2018/02~ Deis Workflow(Deis v2)
    - 2018/08~ Kubernetes(本番も)

- モチベーション
    - Deis が EOS
    - Deis V2 は k8s 上で動いてるのでそれほど苦じゃない
    - fork された Hephy もあったが...
        - Deis Workflow は k8s のラッパー定期位置づけ
        - 機能が足りない部分もあった

- 移行の流れ
    - Dockerfile
        - Heroku Buildpackだからそんな難しくない
        - Twelve Factor App だから難しくない
    - Monorepo 化
        - subtree で入れてった

- 移行方針
    - できるだけ GitHub 管理
    - 大きなツールやミドルウェアは必要になるまで控える
        - YAMl + kustomize
        - ちょっと Helm
    - 移行しきるまでは移行に集中する
        - チューニングしないでバンバン追加した

- kubernetes-clusters
    - kube-aws で定義したインフラ周り
    - 各環境のファイル
    - クラスタ共通の manifest
    - Bash のデプロイスクリプト

- monorepo
    - サービスのほぼ全てを含むアプリケーションコード
    - アプリケーションに近い部分は SRE から開発者に移譲(nginx の設定とか)

- cluster 構成
    - production は 1つ
    - staging
        - develop: develop branch用 cluster
        - release: release branch 用
        - pr-XXX: PR を作るたびに生成される
        - branch-router という pod (ngnx?) をおいて、各namespace にルーティングさせる

- 課題
    - クラスタの継続アップデート
    - rails c を audit ありで取る
    - リソース最適化
    - Service Mesh


- 質疑
    - k8s cluster を世代管理したのは?
        - EKS に Tokyo region がない
        - kube-aws を使ってるから

# CacooはなぜKubernetesによるmicroservicesへの道を選んだのか？ (30min)

https://nulab-inc.com/ja/blog/cacoo/cacoo-microservices-with-kubernetes/

- Nulab
- cacco
    - オンラインで図とか絵がかける
    - 共有できるし、コメントもつけれる

- 課題
    - モノリシックなアプリケーション
        - 1アプリ1インスタンス
        - build/deploy が大変
        - 依存関係が強い
    - EOS なライブラリ

- microservice 化
    - サービスごとに技術選定
    - 部分的な変更が容易
    - リソース最適化
    - 疎結合
    - 目標: "変更を容易に"

- 方針
    - オーナーシップ
    - 小さいほどよい
    - スクラッチ
    - サービス間は強い片付け. protocol buffers
    - 開発環境はサービスごとに自由

- Backend/Middleware
    - Kubernetes
    - Protocol Buffers
    - gRPC
    - RabbitMQ
    - golang
- Monitoring/Alerting
    - Elasticsearch + Kibana
    - Prometheus + Grafana
    - Zipkin


- 構成

![img](https://nulab-inc.com/app/uploads/sites/2/2018/09/architecture-overview-for-blog1.png)


- 移行
    - EC2
    - ECS
        - 新しい機能を別サービス
    - k8s
        - ダッシュボードサービス

- cluster
    - 本番用 cluster
        - cacoo
        - cacoo-beta (stging)
        - monitoring
    - 開発用 cluster
        - cacoo
        - e2e
        - bench

- Protocol Buffers
    - サービス間の型定義
    - 1つのレポジトリで管理
    - 各レポジトリで submodule
    - gRPC でやりとり

- Zipkin
    - distributed tracing


# Athenzを用いたKubernetes Webhook Authorization (30min)

Yahoo

https://github.com/yahoo/athenz

- 大規模なOpenStack
- Kubernetes as a Service, PaaS 両方
- めっちゃ cluster ある


- 問題
    - 多数の k8s cluster のアクセス制御
    - マルチクラウドのアクセス制御
    - Athenz RBAC と kubernetes RBAC の考え方が違う

- Kubernetes Authrization の Webhook を使う

- data model
    - https://github.com/yahoo/athenz/blob/master/docs/data_model.md

- Why Athenz
    - それぞれのクラウド環境でのアクセス絵制御を共通の認証で通せる
    - k8s webhook authorization を利用することで Athenz に Authorization を移譲する

- Garm


# Usernetes: Kubernetes installable under $HOME, without the root privileges- AkihiroSuda

https://github.com/rootless-containers/runrootless

- rootless-container
    - コンテナの中では root で動くが、実行環境としては rootless
    - apk add とかも使える

# kubernetes/sig-docs-jaについて - CS_Toku
# Ingress-GCEとバグの話 - tjun
# たとえば Role を自動生成する - muni
# ConfigMap と Secret 実装差異 - potsbo
