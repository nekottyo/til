Cookpad Tech Kitchen #20 クックパッドのマイクロサービスプラットフォーム現状
====


# 小野大器 サービスメッシュの構築と運用(仮)


- cookpad
	- 200+ developer
	- prd service +100
	- 複数チームが複数サービスを作っている
		- 中央の SRE チームがそれぞれを管理している
	- だいたい RoR
	- ELB -> ECS -> internal ELB -> ECS みたいな構成

- 問題
	- トラブルシュート、 debug が大変
	- capacity planning
- 解決策 1
	- Expeditor
	- aws-xray
		- distributed tracing
	- Ruby はいいけど他の言語は大変
	- からの network proxy

- service mesh
	- library でリトライとか circuit braking を envoy でやる

- Envoy proxy
	- data-plane
	- 採用時には istio はまだなかった
		- k8s 前提な節もある
	- 自分たちで control-plane を作る

- Goals
	- resiliency setting
		- 各ライブラリで行っていたものを中央管理する
	- 全部 Prometheus に入れる

- ServiceDiscoveryService API
	- lyft/discovery を使ってたが cookpad/sds を作った

- +1000 envoy instance

- Resiliency
	- Retry とかをよろしくやってくれたので楽になった
	- Review 速度が上がる

- Observability
	- Grafana dashboard でサービスのヘルスチェックが一覧できる

- next
	-v1 to v2 xDS migration


# 岩間 雄太 gRPC in Cookpad(仮)

- これまでの cookpad
	- Garage, GarageClient で API 共通化
	- Autodoc でドキュメンテーション
	- つらみ
		- IDL, schema ほしい
		- REST への mapping がむずい
	- Ruby 以外の言語への対応
- gRPC
	- IDL として Protocol Buffers
	- generate code

- 運用
	- ECS の hako(自作）上で動作
	- cookpad/sds + Envoy で Client side Loadbalancing
	- Envy 経由で受ける

- Protocol Buffers は 1 つのレポジトリ
	- 各サービスに submodule で配布
	- CI でドキュメント生成

- Prometheus with envoy
	- mtail が envoy のアクセスログを Prometheus 用に流す

- Ruby in gRPC
	- grpc gem
	- GIL があるのでシングルプロセス
	- grpc-tool gem で generate する
- ruby use grpc in cook[pad
	- アクセスログを取るために interceptor を自作
	- 起動/停止を行うライブラリ
	- newrelic 用 interceptor
- RoR use grpc in cookpad
	- app/ 以下にサービス定義
	- lib 以下に grpc 定義
	- connection pool が上限になる
	- grpc gem の問題
		- CPU 使い切れない
		- Graceful Shutdown がない
			- Envoy でサービスアウトしてから止めてる
			- stream 系の graceful shutdown もない
			- 作ってる
				- https://github.com/ganmacs/griffin


# 鈴木 康平 Amazon ECS の安定運用のために (仮)

- ほとんどのアプリケーションが ECS
	- 40 cluster 500 service

- コンテナインスタンスの管理
	- Fargate?
		- 基本は自前管理
		- スポットインスタンスが安い
		- Fargate の起動が遅い
			- バッチジョブが辛い
		- ECS cluster 操作するのは Fergate を使う

- log は多すぎて CloudWatch logs に入らない
	- s3 に Fluentd で入れる
	- Amazon Athena で検索

- コンテナモニタリングは cAdvisor

- AWS, ECS に最適化されすぎてない。..?
- スケールイン、 スケールアウトを自分でやってるのがつらそう

