# Japan Datadog User Group Meetup#3

https://datadog-jp.connpass.com/event/309899


## 開発者とマニアのための Datadog

https://github.com/DataDog/datadog-sync-cli

Workflow automation
- 手動で event だして overlay

time slice SLO
- monitor less でいけるらしい
- gauge でもいける

Datadog integration, UI の widget を開発して公開できるらしい

https://docs.datadoghq.com/developers/integrations/?tab=integrations

## Datadog による自己完結的アプリケーションモニタリング
https://speakerdeck.com/recruitengineers/datadog_mopp

Datadog dashboard を daily or weekly でみんなで眺めてる

powerpack

通知は slack の team mention 便利

発生頻度が少なそうな例外処理に custom metrics の tag つきで投げると便利かもしれん

## ■東芝クラウドマネージドサービス ～８つのマルチクラウド・ハイブリッドクラウド～
CoderDojo
- ラズパイに agent ぶっこんで投げてる

日本は半分くらいオンプレ

cloud vendor に依存しないものを導入してる

Azure monitor 最初つかってたけど、Datadog がいい感じだった
newrelic めっちゃ cost discount で引っ張ってくる


## 導入から5年経って見えてきた Datadog APM 運用の課題(bgpat)

trace いつのまにか分断されてる
- フレームワークの変更
- ライブラリアップデート

利用層の２極化
- 使ってる人は使ってる
- 使わない人は全く使わん
- 障害対応で何すればわからん人が増えた

普及活動大事だよね

## 令和最新版！ Datadog&PagerDutyで出来ること紹介

https://www.pagerduty.co.jp/generative-ai/
