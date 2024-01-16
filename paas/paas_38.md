第38回 PaaS勉強会
=====

- 2018/08/28
- GMO Yours
- https://paas.connpass.com/event/96015/

## Nutanixによる今どきのオンプレミスとDB as a Service - @smzksts, @hanakara_milk

- Nutanix
    - ハイパーコンバージ度インフラ
    - サーバ筐体 + Software defined Storage & 管理ツール
    - もとはアプライアンスで売っていたが、OEM とかソフトウェアのみの販売もある
    - node に Nutanix Controller VM を載せてそいつが制御する
- Nutanix Era
    - Amazon RDS と同じ位置づけ
    - オンプレでも楽にしたい（クラウドっぽい使い方をしたい）
    - Virtual Appliance が任意のスナップショットから DB 込み VM を生成してくる
        - DB の Clone とかもしてくれる
        - Time Machine がよろしくやってくれる
        - トランザクションログと組み合わせて任意時点のバックアップが取れる
    - oracle, postgresql
    - 未リリースだよ
        - Mysql と SQL Server も
- Demo
    - デプロイ時に SLA が選べて、スナップショットの間隔が選ばれる
    - 世代が古くなるほどトランザクションログ、デイリーバックアップ、 ウイークリーバックアップと粒度が変わってくる


## IaaSをいじっている人がPaaSを調べてやったこと - @tnmt

- GMO ペパボ
- ペパボの歴史
    - ~2015
        - ロリポレンタルサーバ
        - 物理サーバ調達から
        - リードタイム 2 か月
        - オンプレ課題
            - リードタイム長すぎ問題
            - 管理大変問題
    - プライベートクラウド Nyah(かわいい）
        - OpenStack
        - 開発者が環境用意できる
        - コスト削減
            - minne 60%減
        - スピーディー
        - 集約管理
    - 最近出てきたサービスでも Heroku 使ってたりする
    - コンテナはまだ使ってない
        - 社内で K8s 検証
        - PaaS, FaaS はまだわからん

## もうひとつのコンテナ実行環境 runq のご紹介 - @ibmamnt

- コンテナランタイム
    - OCI 準拠ならお着替え可能
        - runc がリファレンス
    - runq
        - https://github.com/gotoz/runq
        - コンテナの代わりに qemu/kvm が動く
        - モチベーションはセキュリティ
            - ブロックチェーンとか
        - 類似
            - kata Container
            - gVisor
    - > runq runs Qemu and Linux Kernel from the /var/lib/runq/qemu directory on the host. This directory is populated by make -C qemu. For simplicity Qemu and the Linux kernel are taken from the Ubuntu 18.04 LTS Docker base image.

## 最強のクラウドネイティブ基盤？ Knativeのあれこれ - @jacopen

- みんなで筋肉体操をやろう！
- Kubernetes 難しい。..
- さらに Istio も難しい。..
- サーバレスめっちゃ盛り上がってる
- Kubernetes + Serverless = Knative
	- ケイは発音するんだぞ［kay-nay-tiv]
	- Google, Pivotal, IBM, Red Hat, SAP
	- Kubernetes + Knative + Istio
	- ソースコードデプロイ
	- Blue green
    - 使われていないコンテナは消えて、アクセスされると生えてくる
    - サーバレスとは違う。..
        - Knative でいう Serverless != FaaS
            - ステートレス
            - アプリケションレベルからリクエストされるトラフィックで駆動
- Riff
    - Lambda っぽくいける
- Kwsk
    - Open Kwsk
- 所感
    - 現時点では成熟してない
    - Istio 必須なので敷居が高い
    - Knative だけの知識だけではない
        - Kubernetes + Istio + Knative + etc
    - ドキュメントにはある knative コマンドがあればいいかも
        - 現状では `knctl` がある

