NoOps Meetup Tokyo #6
====

https://noops.connpass.com/event/131553/

2019/06/04 @NATIVIME


NoOps の目標
- システム運用保守がユーザー体験を妨げないシステムの実現
- システム運用保守で発生する「トイル」の最小化
- システム運用保守コストの最適化

https://www.slideshare.net/hiromasaoka/15-noops


# Observabilityを支えるStackdriver @ymotongpoo
http://bit.ly/20190604-noops-meetup

- Developer Advocate for Google Cloud
- SRE は DevOps の実践方法
	- 意思決定にデータを用いる
		- データがなければいけない
			- SLI, SLO, SLA
			- エラーバジェット
	- 運用の課題をソフトウェア・エンジニアリングで解決する
	- ビジネス、 開発、 運用も含めて SLO を元に動く必要がある
- そのデータをどうやって取るかが Ovservability
	- 従来ではハードウェア、 OS + 実行環境のモニタリングをしていたのはマネージド側で管理できるようになってきた
		- アプリやサービス全体のオブザーバビリティが重要になってくる
		- **オブザーバ日リティは勝手に上がらない**
	- Stackdriver (GCP)
		- GCP が Stackdriver を提供する意味
			- API より内側はプラットフォームでしか提供できない
			- SRE の実戦経験を共有する近道
- 運用プロセスで必要なデータ、 ツールは異なる
	- 通常運用 (Stackdriver Monitoring)
		- メトリクス
			- 統計的に動いているか
				- インフラ側のメトリクス
				- アプリケーションメトリクス
					- OpenCensus
					- 構造化ログベースからチャート
		- アラート
			- SLO 違反していないか
	- 高付加、 調査
		- プロファイラ
			- 統計情報を取得
			- Ccpu 使用率、 メモリ、 
		- 分散トレース
			- 特定の負荷を見つける
			- 復数のサービスにまたがったトレース
	- 障害対応
		- エラーレポート
			- 種類ごとにグルーピング
				- 似たようなエラーを勝手にまとめてくれる。すごい
				- 頻度、 回数も集計
		- IRM
			- インシデントレポートを一元管理
			- めちゃすごい
	- 振り返り
		- データエクスポート



# 物理データセンターでも NoOps @ymmt2005
https://speakerdeck.com/ymmt2005/wu-li-detasentademo-noops

- 標準 PC メモリ 32GB の人
	- https://twitter.com/ymmt2005/status/833896090155962368
- cybozu.com
	- 規模が単調増加
- サーバ数十台の頃からアーキテクチャが変わらず 
	- 手作業、 Toil が多い
	- スケーラビリティが低い
- 手順書を作る、 レビューは辛い
	- リハーサルも辛い
	- 時間もかかる
- 求めるもの
	- 宣言的オペレーション
	- 継続的デリバリ
- Neco project
	- Kubernetes 中心アーキテクチャ
- 設計原則
	- Be Declarative
		- Kubernetes 意外もすべて宣言的操作
	- Define by Software
	- Test Everything
- 仮想データセンターを活用した CI/CD
- CKE
	- Kubernetes クラスタ運用を自動化
- https://blog.cybozu.io/entry/2018/07/05/080000
- Kubernetes の上と下でデリバリを分割
	- Argo CD で kubernetes 上のアプリはデリバリ
	- Kubernetes の下は宣言的デリバリ
		- 当てる前の状態を作って、 更新をかけるとか、 電源喪失シナリオのテストなどもある
- 今後
	- ストレージ管理
		- 自作 CSI プラグイン

# NoOpsを実現するSREの存在意義と役割 / class SRE implements NoOps @katsuhisa__

https://speakerdeck.com/katsuhisa91/class-sre-implements-noops

- Visual SOP management Platform
	- 標準作業手順書
- NoOps とは
	- コラボレーションを重視する DevOps
	- NoOps は Operation と密なコミュニケーションを取らなくてもオペレーションが回る。..?
	- どこまでを Ops とするか
		- DevOps と NoOps は対立構造ではない
- NoOps is not No Operation
- Toil の計測
	- 作業を分類
	- kanban で管理
	- 現在は 5〜10% の Toil
- Toil の減らし方
	- Engineer Toil Out of the System
		- Toil をへらす前にシステムを変更して Toil が生まれにくようにできないか
		- 不可逆性が高い部分はどこか
		- 性能面で詰まる部分はどこか
		- Design Document を必ず書く
			- Postmortem(障害報告書）を書くのと同じくらい重視
	- より信頼性の高いコンポーネントに置き換える
- SLO
	- ダウンタイムを許容する
- Provide Self-Service Method
	- 開発者から依頼を受けて仕事するのではなく、 開発者自身にやってもらう
	- チケットドリブンでは組織の経済的な無駄を生む
- Self Service
	- Define
	- Execute
	- Govern
	- Define, Execute は開発者が持って、 Govern は SRE が持つのが理想？

