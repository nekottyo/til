# GitLab Meetup Tokyo #6

## GitLab概説、GitLab.comのインフラと運用について

- GitLab Inc. 本家
- git -> github 誕生
	- github は Public だからエンプラ使いにくい
	- github EE はブラックボックスでわからん！
- OSS な github clone として gitlab の立ち上げ
	- 初めは code management + MR(PR)
	- + CI/CD , monitoring というアプリケーションすべてのマネジメントに
- (質問）なんでたぬきのロゴ？
	- 最初のロゴはたぬきで怖かった（娘もびびってた）
	- デザイナにいい感じな tanuki を依頼したら、google translate にタヌキをかけると Japanese Fox になるので今のキツネになった

## State of Community Contributions to GitLab CE

- GitLab Inc.
- [CE,EE architecture](https://docs.gitlab.com/ce/development/architecture.html)
- [Gitlab.com architecture](https://about.gitlab.com/handbook/infrastructure/production-architecture/)
	 DB
	 	- Master/Slave 構成
		- 基本 Master で読み書きしてるけど負荷が高いときは Slavek に流す
	 スケール感
	 	- Azure に乗ってる
	 	- Consul + terraform + chef 管理
	 	- 120 コンポーネント位ある
	 	- sidekick が大変（MR 遅くなったり）
	 	- 今は Azure 1 本だけどそのうち GCP にも載せたい
- モニタリング
	- Kubernetes
		- フロントは Graphana
		- Prometheus alert manager
	- Prometheus
- GitLabl.com の運用自体は 5 人位で運用してる
	- infra 設計、 構築は 5-10 人くらい
- 100M 単位のユーザ
- (質問）サイジング目安は？
	- (https://docs.gitlab.com/ce/install/requirements.html)
	- 自動化でガンガン API 叩くならその限りではないよ

## Cloud Native GitLab ChartでGitLabをk8sにデプロイする

- gitlab omnibus package はデカすぎ
- 今ある chart は詰め込んだだけ
- 横展開も辛い
- [Cloud Natice GitLab Chart](https://gitlab.com/charts/helm.gitlab.io/blob/master/README.md) へ
	- microservice 化
