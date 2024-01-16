microservice.io
===

英語難しい

理解のためい英弱が雰囲気で訳した


-----

Applying the microservice architecture pattern language
-------------------------------------------------------
https://microservices.io/articles/applying.html
パターンらへん怪しい

 

マイクロサービスアーキテクチャパターン・ランゲージは多数のパターンのグループからなります。パターン・ランゲージの価値はパターンの合計を超えます。
それは、パターン間での関係性を定義しているからです：

- Predecessor(先行要素？): “先行要素パターン”はこのパターンの必要性を動機づけるパターンです。 例えば、マイクロサービスアーキテクチャパターンは、モノリシックアーキテクチャパターンを除く残りのパターン・ランゲージの先行要素です。
- Successor(後継）: “後継パターン” はもたらされた問題を解決するパターンです。例えば、マイクロサービスアーキテクチャパターンを適用した時に、サービスディスカバリパターンやサーキットブレイカーパターンなどの多数の後続パターンを適用する必要があります。
- Alternative(代替）: “代替パターン”は代替案を提供します。例えば、モノリシックアーキテクチャパターンとマイクロサービスアーキテクチャパターンはアプリケーションアーキテクチャパターンの代替案です。2 つの要素があるとき、一方を選択しなければなりません。これらの関係は、パターン言語を利用する時に貴重な方向性を提供します。あるパターンを適用すると、後継パターンを適用しなければならない問題が発生します。後継のパターンが存在しなくなるまで再帰的にパターンが選択されます。2 つ以上のパターンがある場合は、1 つを選択して適用しなければならなりませ n。これは、多くの点でグラフ探索に似ています。
それでは、あなたのアプリケーションにどのようにしてマイクロサービスアーキテクチャを適用するかを見てみましょう。この記事では、あなたがしなければならない 3 つの決定を見ていきます。今後の記事では、他の重要なものも見ますが、非常に重要というわけではありません。

## Decision #1: モノリシックアーキテクチャかマイクロサービスアーキテクチャか
1 つ目の決定はモノリシックアーキテクチャパターンかマイクロサービスアーキテクチャパターンかを決める必要があります。マイクロサービスアーキテクチャパターンを選択した場合、他の多数のパターンも決定する必要があります。

![img](https://microservices.io/i/PatternsRelatedToMicroservices.jpg)

ご覧のように、数多くのパターンを適用しなければなりません。いくつかの選択肢を見てみましょう。

## Decision #2: どのようにしてアプリケーションをサービスに分割するか
あなたはマイクロサービスアーキテクチャを使うと決めた時、サービスを定義する必要があります。それには 2 つのオプションがあります。

- ビジネス機能による分割 - ビジネスの機能に対応するサービスを定義する
- サブドメインによる分割 - DDD におけるサブドメインでサービスを分割する

このパターンは技術的な概念ではなく、ビジネスの概念に基づいた一連のサービスと同様の結果をもたらします。

## Decision #3 どのようにしてデータの一貫性の維持ととクエリの実行するか
マイクロサービスの主な機能は、サービスごとのデータベースパターンです。別の方法として、データベース共有パターンがありますが、基本的ににはアンチパターンであり、避けることをおすすめします。
サービスごとのデータベースパターンは、データの一貫性の維持とクエリの実行が動的に変わります。将来的には Saga パターンを使う必要があるでしょう。多くの場合コマンドクエリ責任分離パターン（CQRS パターン）を使用してクエリを実装する必要があります。



Applying the microservice architecture pattern language
-------------------------------------------------------

[https://microservices.io/articles/applying.html](https://microservices.io/articles/applying.html)

パターンらへん怪しい

* * *

microservice architecture
-------------------------

[https://microservices.io/patterns/microservices.html](https://microservices.io/patterns/microservices.html)

疎結合なセットのアプリケーション

*   pros:
    *   メンテナンス性も高いしテストしやすいめっちゃ早く開発とデプロイができる
    *   他のサービスとも粗結合で、影響しないしされない。自分たちの開発が独立してできる
    *   独立したデプロイで他チームとの調整もいらない
    *   ちっちゃいチームで出来る。生産性高い
    *   マイクロサービス 1 つあたりはちっちゃい
        *   IDE も早いよ（なるほど）
    *   障害にも強い
        *   1 サービス落ちても分割されてるから他は生きてる
    *   新規マイクロサービス開発する時は新しいテクノロジスタックを選べる
*   cons:
    *   分散システムは複雑になる
    *   サービス間テストむずい
    *   複数サービスにまたがる機能むずい
        *   (組織とマイクロサービスが 1:1 みたいな文脈）
    *   IDE とかはモノリシック前提だから大変かも
    *   サービスいっぱいあるから全体のデプロイ大変
    *   メモリ効率は良くない

HTTP/REST のような動機的津神か、 AMQP みたいな非同期通信でやりとりする。  
それぞれのサービスは他のサービスから切り離すために自身の DB を持つ。  
サービス間のデータの一貫性は Saga パターンを使う

### いつつかうん?

新規開発は恩恵がないことが多い。大変な分散システムだと開発効率落ちるかも。スタートアップだと問題になるかも？　ただ、サービスを垂直スケールし続けてモノリスになった頃に分割すると大変だぞ

### どうやってサービス分割するの

*   ビジネス機能による分割  [https://microservices.io/patterns/decomposition/decompose-by-business-capability.html](https://microservices.io/patterns/decomposition/decompose-by-business-capability.html)
*   サブドメインによる分割 [https://microservices.io/patterns/decomposition/decompose-by-subdomain.html](https://microservices.io/patterns/decomposition/decompose-by-subdomain.html)
*   動詞、 ユースケース、 特定のアクションに責任を持つサービス定義による分割
    *   Shipping Service
*   名詞、 与えられたエンティティーやリソースの全ての操作責任を持つサービスによる分割
    *   Account Service

### データの一貫性は

*   サービス間を疎結合にするためにはそれぞれで DB を持つ。2 相コミット/分散トランザクションは選択肢にならないため、 Saga pattern を使う。サービスはデータの変更を publish して、 consumer は Event sourcing と Transaction Log Tailing などを使ってデータを更新する

### クエリの実装は

*   Api Composition や CQROS パターン

* * *

Database per Service
--------------------

[https://microservices.io/patterns/data/database-per-service.html](https://microservices.io/patterns/data/database-per-service.html)

DB 隠して API からのみ更新できるようにする

*   pros
    *   サービス間疎結合
    *   それぞれのサービスで好きな DB 使える。DB が Elasticserach でもいいし RDBMS でもいいし Mongo でもいいし cloud でもいい
*   cons
    *   複数サービスに跨ったビジネストランザクションは簡単じゃない。分散トランザクションは CAP 定義により避けるべき。さらにモダンな DB はサポートしてない。 Saga pattern を使うべき
    *   複数 DB に跨った変更加えるのむずい
    *   複数の SQL, NoSQL 管理するの大変
*   solusion:  
    *   API Composison
        *   api gateway 的なの用意する。DB を join するのではなく（多分）データを結合する。最初に customer service からカスタマー情報貰ってから、 order service からカスタマーの最近の注文をもらうみたいな
    *   Command query Responsibility Segregation(CQRS)  
        *   つまり Publish/Subscribe かな？

* * *

Saga
----

[https://microservices.io/patterns/data/saga.html](https://microservices.io/patterns/data/saga.html)

(これは redux-saga の saga と同じ saga?)

### Context

Data base per Service 適用してて、それぞれのサービスが自分の DB を持っている状況で、複数サービスに跨ってデータの一貫性を保ったビジネストランザクションをする。  
例えば、e-commerce store がクレジットカードの上限を持って居る時、アプリケーションは新規注文が顧客のクレジットカード上限に当たらないかを確認する必要がある。  
オーダーとカスタマは別の DB にあるため、単純に local ACID トランザクションは使えない。

### problem

どうやってデータの一貫性を保つか

### 制約

2PC(2 相コミット）は使えない

### solution

saga として複数のサービスにまたがるビジネストランザクションを実装する。saga はローカルトランザクションのシーケンスである。  
それぞれのローカルトランザクションはデータベースのアップデートとメッセージかイベントを発行し、saga の次のローカルトランザクションをトリガする。  
もしトーかるトランザクションがビジネスルールに違反して失敗した場合、saga は前のローカルトランザクションによる変更を戻す一連の補償トランザクションを実行する（つまりロールバック）。

![https://microservices.io/i/data/saga.jpg](https://microservices.io/i/data/saga.jpg)

saga では 2 つの調整方法がある：

*   Choreography: それぞれのローカルトランザクションは他のサービスのローカルトランザクションをトリガするドメインイベントを発行する
*   Orchestration: オーケストレーターは参加者にどのローカルトランザクションを実行するのかを伝える

### Example: Choreography-based saga

![https://microservices.io/i/data/Saga\_Choreography\_Flow.001.jpeg](https://microservices.io/i/data/Saga_Choreography_Flow.001.jpeg)

このプローチを使う e-commerce アプリケーションは次のステップからなる choreography-based saga を利用し注文を作成する

1.  `Order Service` は保留状態の注文を作成し、 `OderCreated` イベントを発行する
2.  `Customer Service` はその注文のクレジットを予約を試みるためのイベントを受信する。`Credit Reserved` イベントか、`CreditLimitExceeded` イベントを発行する
3.  `Orde Service` はイベントを受信し、注文の状態を承認済みかキャンセルに変更する

### Example: Orchestration-based saga

![https://microservices.io/i/data/Saga\_Orchestration\_Flow.001.jpeg](https://microservices.io/i/data/Saga_Orchestration_Flow.001.jpeg)

このプローチを使う e-commerce アプリケーションは次のステップからなる Orchestration-based saga を利用し注文を作成する

1.  `Order Service` は保留状態の注文を作成し、 `CreateOrderSaga` を作成する
2.  `CreateOrderSaga` は `Customer Service` に `ReserveCredit` コマンドを送信する
3.  `Customer Service` は注文のクレジットの予約試み、返信を返す
4.  `CreateOrderSaga` は返信を受信し、`ApproveOrder` か `RejectOrder` のどちらかのコマンドを `Order Service` に送信する
5.  `Order Service` は注文の状態を承認済みかキャンセルに変更する

### Resulting context

*   pros
    *   複数のサービスに跨ったデータの整合性を、 分散トランザクションを使わずに維持できる
*   cons
    *   プログラミングモデルは複雑になる。例えば、開発者は saga の早い段階の変更を明示的に戻す補完トランザクションを設計しなければならない
    *   信頼性を高めるために、サービスはデータベースのアップデートとメッセージ/イベントの発行を自動で行わなければならない。  
        データベースとメッセージブローカにまたがる分散トランザクションは、従来のメカニズムでは利用できない。代わりに、以下のパターンを使う必要がある

### Related patterns

*   Database per Service pattern
*   自動で状態をアップデートしメッセージ/イベントを発行する方法は以下のパターンがある
    *   Event sourcing [https://microservices.io/patterns/data/event-sourcing.html](https://microservices.io/patterns/data/event-sourcing.html)
    *   Application events [https://microservices.io/patterns/data/application-events.html](https://microservices.io/patterns/data/application-events.html)
*   Choreogrphy-based saga は Aggregates や Domain Events を使ってイベントを発行できる
    *   Aggregate [https://microservices.io/patterns/data/aggregate.html](https://microservices.io/patterns/data/aggregate.html)
    *   Domain event [https://microservices.io/patterns/data/domain-event.html](https://microservices.io/patterns/data/domain-event.html)
