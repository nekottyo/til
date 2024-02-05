# State of Kubernetes Cost Optimization Report

https://inthecloud.withgoogle.com/state-of-kubernetes-cost-optimization-report/dl-cd.html


---


## Executive Summary

Kubernetes は豊富な機能とスケジューリング機能を提供するが、エンドユーザや関連アプリケーション全体の信頼性に影響を与えることなく Kubernetes の使用を最適化するには、プラットフォームが提供する機能を深く理解する必要がある。

これは、Google が Kubernetes コスト最適化の現状を調査し、定量的に分析したレポートである。
ワークロードのパフォーマンスと信頼性を維持しながら、コスト効率の高い方法でパブリッククラウドの Kubernetes cluster を管理するベストプラクティスを、管理者、アプリケーションオペレータ、開発者などに提供する。

クラウドコストの最適化は組織全体の様々なチームの関与を必要とする継続的なプロセスであるため、rightsizing、demand-based downscaling , bin packing, discount coverage という **ゴールデンシグナル** と呼ぶものを使用して、チームがパフォーマンスを測定することを提案する。

本レポートでは、これらのシグナルをクラスタのコスト最適化のパフォーマンスレベル別に分類している。


## 主な調査結果
### Kubernetes コスト最適化は適切な resource request を設定することから始まる

Kubernetes は bin packing, scheduling, cluster autoscaling, horizontal workload scaling に CPU / memory request を利用することに加え、リソースリクエストを使用して Pod の [QoS(Quality of Service) class](https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/) を分類する。
この分類に基づいて、特定の node の利用率がその容量に近づいたときにどの Pod を強制終了するかを決定する。 request を設定しない場合、間接的に `BestEffort` として分類される。

[node pressure](https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/) により resource を再要求する場合、graceful termination や警告なしに BestEffort Pod が最初に強制終了される。これは要求した以上のメモリを常に使用する `Burstable` ワークロードでも発生する。

これらのワークロードを無差別にデプロイすると、Kubernetes の bin packing, scheduling autoscaling に影響を与え、エンドユーザ、 ビジネスにも悪影響を及ぼす可能性がある。

### workload rightsizingは最も重要なゴールデンシグナル
調査の結果、ワークロードのリソース要求は平均してオーバープロビジョニングされていた。memory request を効率的に設定するエリートパフォーマでさえ、CPU request に関しては改善の余地がある。 また、workload rightsizing がリソースの無駄を削減する最大の機会であることが判明した。

最も重要なことは、workload rightsizing に取り組まず、discount coverage, cluster bin packing に注力しているクラスタオーナは、ワークロードが適切なサイズになったとき、再び rightsizing に取り組まなければならない可能性がある。

過剰なプロビジョニングを削減するためには、まず技術チームに resource request の重要性を認識させ、その後に workload rightsizing に注力すべきである。


### 信頼性とコストのバランス
大きな clsuter で、 BestEffort と Burstable Pod が意図せず使用されている場合、clsuter の node は過負荷に陥る事がある。このような状況では、断続的でデバックが難しいパフォーマンスと信頼性の問題が発生するリスクがある。

クラスタ管理者は、BestEffort と Burstable Pod を大量に実行していることを知らないか、それらをデプロイした結果を理解していなかった。

### 信頼性が考慮されていないコスト最適化は信頼性を損なう可能性がある
大量の BestEffort や Burstable Pod を実行しているクラスタは、cluster bin packing が低い傾向にある。このシグナルを、クラスタのスケールダウンやノードのサイズ適正化によるコスト削減の間違った機会と解釈する可能性がある。これをの変更を適用すると、警告や eviction なしに Pod が終了する可能性が高まり、アプリケーションの中断が発生する可能性がある。

### 需要ベースのダウンスケーリングはワークロードの autosclaing に大きく依存している
調査の結果エリートパフォーマはローパフォーマよりも 4 倍スケールダウンを実行している。これは、エリートパフォーマがどのセグメントよりも既存のオートスケーリング機能（Cluster Autoscalier, Horizontal Pod Autoscaler, Vertical Pod Autoscaler) を活用している。
エリートパフォーマはローパフォーマに比べ、CA を 1.4 倍、 HPA を 2.3 倍、 VPA を 18 倍有効にしている。つまり CA を有効にするだけではオフピークにスケールダウンできない。クラスタをオートスケールするには、HPA, VPA などのワークロードのオートスケールを適切に設定する必要がある。

オフピーク時にスケールダウンするワークロードが多いほど、CA による node の削除効率は高まる。

### ハイパフォーマはクラウド割引を利用する
エリート、ハイパフォーマはローパフォーマよりも 16.2 倍、 5.5 倍も多くクラウド割引を採用している。これらのセグメントは、Kubernetes 専門知識を社内に持ち、コスト最適化活動に注力するチームを持つ傾向にある。
これにより、大幅に割引された Spot VM の利用や、長期的なコミットメントの予測が可能になる。

### Pod が request を適切に設定しない場合 Chargeback が損なわれる可能性がある
ほとんどの Kubernetes コスト割当ツールは resource request を使用してコストを割り当てる。したがって、BestEffort や Burstable Pod などを使用し request を適切に設定しない場合、[Showback と Chargeback](https://www.finops.org/framework/capabilities/chargeback/) の精度が損なわれる可能性がある。
例えば、BestEffort Pod で大量の CPU とメモリを消費するシナリオでも、その Pod はリソース要求をしていないためコストに反映されない可能性がある。

> [!NOTE]
> Showback は、組織内の部門やチームに対して、クラウドリソースの使用量に応じてコストを表示すること。  
> https://www.finops.org/framework/capabilities/analysis-showback/
>
> Chargeback は、組織内の部門やチームに対して、クラウドリソースの使用量に応じてコストを請求すること。  
> https://www.finops.org/framework/capabilities/chargeback/


## Kubernetes コスト最適化のゴールデンシグナル
[2023年の State of FinOps Report](https://data.finops.org/) では、コンテナコストの増加と無駄の削減が最重要課題として挙げられている。[Flexera の 2023年 State of Cloud Report](https://info.flexera.com/CM-REPORT-State-of-the-Cloud) でも、7 年連続でクラウドの既存利用の最適化が重要課題として挙げられている。

Kubernetes は多くの機能を備えた複雑な分散システムであり、正しく使用しないと過剰なプロビジョニングにつながる。必要のないリソースを請求されないようにするには、可観測性が重要であり、見えないものは管理できない。

Google は長年のフィードバックの結果、パブリッククラウド上の Kubernetes クラスタのコスト最適化パフォーマンスを測定するのに役立つ 4 つのゴールデンシグナルを特定した。
ゴールデンシグナルは 2 つのグループに分かれている。Resources グループは、料金を支払っている CPU, Memory の使用能力に重点を置き、 Cloud discounts は、クラウドプロバイダーの割引を利用する能力に重点を置いている。



- Resources group
    - Workload rightsizing
        - 実際の resource 使用率 vs Reqequested resource
        - 開発者がアプリケーションに要求した CPU とメモリを使用する能力を測定する
    - Demand based downscaling
        - 需要が低い場合の cluster のスケールダウン
        - 開発者とプラットフォーム管理者がオフピーク時にクラスタをスケールダウンする能力を測定する
    - Cluster bin packing
        - Requested resrouce vs Allocated resource
        - 開発者とプラットフォーム管理者が Pod の配置によって各 node の CPU と memory を割り当てる能力を測定する
- Coud discounts group
    - Discount coverage
        - Spot やクラウドプロバイダによるディスカウントのカバレッジ
        - Spot VM のような大幅なディスカウントを活用するプレアットフォーム管理者の能力を測定する
        - クラウドプロバイダーが提供する長期利用のディスカウントを活用する予算管理者の能力を測定する

## エリートパフォーマはどのようにしてコストを最適化しているか
本レポートでは Kubernetes コスト最適化のゴールデンシグナルを使用して、クラスタをセグメントにグループ化した。Low パフォーマと比較し、Medium, High, Elite パフォーマンスを発揮するクラスタのメトリクスを比較した結果をまとめている。
例えば、エリートパフォーマは、ローパフォーマに比べて 2.8 倍もの CPU 適正化を実現している。

エリートパフォーマは Kubernetes プラットフォームをより理解し、長期的なコミットメントを予測する準備ができている。これにより、他のセグメントと比較して、低価格のワークロードを実行することが可能になる。

| Performers | CPU Workload rightsizing | Memory Workload rightsizing | Demand based downscaling | CPU Cluster bin packing | Memory Cluster bin packing | Discount coverage |
|:-----------|:-------------------------|:----------------------------|--------------------------|:------------------------|:---------------------------|:------------------|
| Medium     | 1.3x                     | 1.3x                        | 2.0x                     | 1.6x                    | 1.8x                       | 1.7x              |
| High       | 2.2x                     | 2.1x                        | 3.2x                     | 1.9x                    | 2.4x                       | 5.4x              |
| Elite      | 2.8x                     | 2.7x                        | 4.0x                     | 2.1x                    | 2.9x                       | 16.2x             |



ワークロードの適切なサイジングに関しては、エリートパフォーマはローパフォーマに比べて  CPU で 2.8 倍、 memory で 2.7 倍優れている。これはエリートパフォーマのクラスタにデプロイする開発者が各環境（テスト、 ステージング、 本番環境など）でアプリケーションが必要とするリソースをより良く理解していることを示している。

需要ベースのダウンスケーリングに関しては、エリートパフォーマはローパフォーマに比べて 4.0 倍ものスケールダウンを実行している。クラスタのスケールダウンは、開発者とプラットフォームチーム間で共有される責任であることを覚えておくことが重要である。
開発者がワークロードをスケールダウンしなければ、プラットフォームチームがクラスタをスケールダウンすることに制限がある。一方で、開発者がオフピーク時にスケールダウンできれば、プラットフォーム管理者は Cluster Autoscaler を調整してビジネスニーズに応じて需要を満たすことができる。

クラスタビンパッキングでは、エリートパフォーマはローパフォーマに比べて 2.1 倍、 2.9 倍もの CPU クラスタビンパッキングを実現し、CPU と memory 両方で node により Pod をうまく詰め込めている。クラスタビンパッキングは、開発者とプラットフォームチームで共有されるもう 1 つの責任であり、ワークロードにより適合する適切なマシンを見つけるために両者が協力する必要がある。



## リスクのある(At Risk)セグメント
上記の Highe, Medium, Low セグメントに加え、 At Risk セグメントと呼ぶ別のセグメントを特定した。これは、 Kubernetes が [node pressure による eviction](https://kubernetes.io/docs/concepts/scheduling-eviction/node-pressure-eviction/) によって断続的で debug 困難な信頼性の問題が発生するリスクが高いこ状態である。

node resource 使用率が容量に近づくと、[kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) は "self-defense mode" に入り、Pod を即座（警告や猶予のある終了なしに）に終了させ、リソースを取り戻す。
この状況は、BestEffort と Burstable Pod が意図せず使用されている場合に発生し、またこれらの Pod は kubelet により最初に強制終了される。

デフォルトの Kubernetes の動作では、bin packing の低い node に新たな Pod をスケジュールされる事が多い。schedule する基準は実際の resource 使用量は考慮せず、resource request のみを考慮する。そのため、BestEffort や Burstable Pod を空いている node にスケジュールし続けるため、node が要求された resource よりも利用率が高くなる原因となる。その結果 kubelet の "self-defense mode" を引き起こす可能性がある。

このため、BestEffort Pod と Burstable Pod の利用は慎重になる必要がある。これらの Pod は node に負荷がかかるたびに Pod に強制的に終了される可能性があるため、アプリケーション開発者とオペレータはこれらの影響を十分に理解する必要がある。


調査の結果、At Risk セグメントは BestEffort Pod をローパフォーマの 1.6 倍、 エリートパフォーの 2.7 倍多く配置しており、 resource request 以上のリソースを使用していた。
BestEffort Pod も Burstable Pod もクラスタの不使用リソースを活用するが、これらの種類は立ち退き時間なしでいつでも強制終了できるベストエフォートなワークロードのみに採用するべきである。

At Risk セグメントは中規模クラスタで構成されており、一連の機能を通じてコスト最適化の意識が高い（クラウド割引がローパフォーマーよりも 3.9 倍、 コスト分配ツールの採用が 1.5 倍、VPA のレコメンデーションモードが 3.1 倍多い）。
最適化を意図しているにも関わらず、ほとんどの Kubernetes コストアロケーションツールは、コスト計算に resource request を活用しているため、BestEffort Pod が大量にある場合は適切なコストの割当ができない。

さらに、At Risk セグメントではクラスタのビンパッキングが相対的に低い（Medium パフォーマよりも CPU が 1.3 倍、 memory が 1.7 倍悪い）。プラットフォームチームによっては、これをコスト最適の誤った機会とみなされる可能性がある。
これに基づいて行動すると、BestEffort や Burstable Pod が警告や立ち退きなしに強制終了される可能性が高まるため、混乱が生じる可能性がある。

クラスタビンパッキングの低さと、少ないコスト最適化機能（Cluster Autoscaler の採用が 1.3 倍、 Cluster Autoscaler の downscaling 調整が 2.2 倍、 HPA の採用が 1.7 倍 Low パフォーマよりも少ない）により、人為的に過剰にプロビジョニングすることで、信頼性の問題を緩和しようとしていると考えられる。



## 信頼性とコスト効率のバランス
前述のように、At Risk セグメントはアプリケーションの水平スケーリングが難しく、コスト配分ソリューション、 bin packing, scheduling, Cluster Autoscaler に影響を与えるだけでなく、BestEffort, Bustable Pods の過剰利用によりエンドユーザエクスペリエンスに悪影響を及ぼす可能性がある。

調査の結果、すべてのセグメントに BestEffort と Buestable Pods の使用を減らす機会があり、その結果 kubelet による強制終了を減らすかなくす事ができる事がわかった。

これを達成するために企業は技術チームにトレーニング、 可視化、 ガードレールを提供することに投資しなければならない。
トレーニングでは、企業は開発者とオペレータが [Kubernetes の QoS(Quality of Service)](https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/) モデルを理解し、node が resource pressure に晒されているとき、アプリケーションの信頼性にどのような影響を与えるかを確認する必要がある。

BestEffort Pod は、コンテナに対して request, limit を設定していない場合に分類される。Kubernetes は node が memory を使い果たしたときに最初に強制終了する。
これは名前の通りベストエフォートなワークロードを実行するだけのもので、ワークロードがすぐに実行されなくても、終了までに時間がかかっても、勝手に再起動や強制終了されても問題にならない場合に利用する。


```yaml
apiVersion: v1
...
spec:
  containers:
    - name: qos-example
      image: nginx
```

Burstable Pod は resource limit が設定されていない resource request を持つ Pod である。node のリソースが圧迫されている場合、Pod のメモリを圧縮できないため強制終了される可能性がある。 Burstable Pods は要求された量以上を常に実行するためのものではない。例えば、Pod の起動時間を高速化したり、HPA が新しい replica を作成している間にバーストするなど、時折 resource を必要するワークロードに適している。

```yaml
apiVersion: v1
...
spec:
  containers:
    - name: qos-example
      image: nginx
      resources:
        requests:
          cpu: "500m"
          memory: "100Mi"
        limits:
          cpu: "1"
          memory: "200Mi"
```

Guaranteed Pod は resource request と limit を同じ量もつか、limit のみを指定する（kubernetes が自動的に request にコピーされる）Pod である。これらは、リソースを厳密に必要とするワークロードを実行するためで、バーストできないため Pod の優先順位が高くなり、BestEffort や Burstable Pod よりも先に強制終了されないことが保証される。

```yaml
apiVersion: v1
...
spec:
  containers:
    - name: qos-example
      image: nginx
      resources:
        requests:
          cpu: "500m"
          memory: "100Mi"
        limits:
          cpu: "500m"
          memory: "100Mi"
```

BestEffort や Burstable Pod はワークロードが中断され可能性があるため、以下のベストプラクティスを適用することが重要である：:

- 最低限の信頼性が必要なワークロードは BestEffort Pod を使用しない
- すべての Burstable Pods のコンテナに memory request と limit を同じ値にする
    - CPU はスロットルできるため、memory によるワークロードの終了を発生させず、node から利用していない CPU resource を使用できる


```diff
 apiVersion: v1
 ...
 spec:
   containers:
     - name: qos-example
       image: nginx
       resources:
         requests:
           cpu: "500m"
-           memory: "100Mi"
+           memory: "200Mi"
         limits:
           cpu: "1"
           memory: "200Mi"
```

可観測性のために、DevOps とプラットフォーム管理者両方が、アプリケーション所有者に推奨される適正化を提供し、すべての BestEffort Pod やメモリ不足の Burstable Pod など信頼性リスクのあるワークロードの使用を追跡する必要がある。

ダッシュボード、アラート、警告、自動的な issue の作成、自動的な Pull Request のような実行可能な戦略をとることが重要である。これらは、開発者 IDE, 開発者ポータル、 CI/CD パイプラインなど開発者のストリームに統合されると効果的になる。これにより、アプリケーション全体での信頼性向上だけでなく、コストを意識した文化を構築するためにも有効であることが示されている。

ガードレールについては、DevOps チームとプラットフォームチーム両方が、このセクションで説明するベストプラクティスを実施するソリューションを構築することが出来る。
[Validation, Mutating admission webhook](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/) のような Kubernetes の機能を使用するか、[Open Policy Agent (OPA) Gatekeeper](https://github.com/open-policy-agent/gatekeeper) のようなポリシーコントロールフレームワークを使用することで行える。
また、 Kubernetes の QoS とその結果を理解しているチームがアイドル状態のクラスタリソースを利用できるように、break glass process を導入するのも重要である。
例えば、開発者が resource を設定せずに Pull Request を作成した場合、CI で専門チームにレビューを要求するとか、警告用のアノテーションを付与するなどである。
これが本番環境で実行されている場合、チームがその結果を理解し組織の policy をバイパスするというアノテーションを強制することが出来る。

> [!NOTE]
> break glass process  
> 緊急時にガラスのドアやストッパーを壊して火災警報器を作動させることに由来して、通常の運用手順を無視した手順を実行することを指す  
> https://help.xtontech.com/content/getting-started/architecture/break-glass-procedure.htm

チームが突然の終了に備えてワークロードを準備し、BestEffort Pods の作用を十分に理解している状況では、アイドル状態のクラスタリソースを活用し Spot VM で BestEffort ワークロードを実行することでコスト効率を高めるチャンスになる。
Spot VM は多くの場合、クラウドプロバイダで急な終了やリソースの再利用を許可する場合に割引価格で提供される。プラットフォームチームは mutating admission webhook を利用し、 request を設定しないすべての BestEffort Pod に [node affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/) を追加できる。
これにより、高い信頼性を必要とするワークロードのために標準ノードを残すことができる。

プラットフォームチームがベストプラクティスを回避させたくない場合、準拠していないワークロードを検証し拒否することを推奨する。それが不可能な場合、graceful restart に耐性があるワークロードには、[Auto mode を使用した Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#quick-start) の採用を推奨または強制することもできる。

> [!NOTE]
> VPA でリソースを更新するためには Pod の再起動が必要だった  
> [KEP #1287: In-Place Update of Pod Resources](https://github.com/kubernetes/enhancements/issues/1287) により、将来の VPA では Pod を再起動せずに resource を更新できるようになる

最後に、[Kubernetes LimitRange API](https://kubernetes.io/docs/concepts/policy/limit-range/) を使用して、コンテナリソースのデフォルトを設定することも出来る。デフォルトではワークロードのリソースが最小または最大になる可能性があるため、Pod の適正化に VPA を採用するという推奨事項に代わるものではない。
Limit Range を使用する利点は、ワークロードが最初にデプロイされるとき、 [VPA の低信頼性モード](https://github.com/kubernetes/autoscaler/blob/vertical-pod-autoscaler-0.13.0/vertical-pod-autoscaler/pkg/apis/autoscaling.k8s.io/v1/types.go#L280)の間にリソースを適用できる事である。このモードでは十分なデータがないため VPA は Pod resource を更新しない。

## ハイパフォーマ, エリートパフォーマはどのようにしてコストを最適化しているか

ハイパフォーマとエリートパフォーマは、すべてのセグメントで、最大のクラスタを実行している（ハイパフォーマはローパフォーマーより 2.3 倍、 エリートパフォーマは 6.2 倍の node 数で実行）。クラスタを大きくすることでリソースをより有効に活用できる。例えば、少数のワークロードがアイドル状態である間に、他のワークロードがバースト状態になることがある。

また、大規模クラスタは[マルチテナントクラスタ](https://kubernetes.io/docs/concepts/security/multi-tenancy/) になる傾向がある。テナント数が多ければ多いほど、より多くの運用上の課題を管理する必要がある。
このようなパターンでは、企業のコスト最適化方針、ベストプラクティス、ガードレールの導入をする専門のプラットフォームチームが必要となる。

また、ハイパフォーマとエリートパフォーマのプラットフォームチームは、ローパフォーマよりもクラスタオートスケーリング（1.3 倍、 1.4 倍）やコストアロケーションツール（3 倍、 4.6 倍）など、クラスタレベルのコスト最適化機能を有効にする傾向がある。
コスト割当ツールは [show back や chargeback](https://www.finops.org/framework/capabilities/chargeback/) プロセスにより、マルチテナント Kubberntes クラスタから各部門または部門の仕様に関連するコストの割り当て、 請求を行う。

ハイパフォーマやエリートパフォーマと同じように（ローパフォーマの 3.2 倍、 4 倍）オフピーク時にクラスタのスケールダウンを可能にするには、需要に応じてワークロードをスケールする必要がある。

調査によると主に 2 つのアプローチを採用している。第一にワークロードの自動スケーリング API (Auto/Init モードの VPA, HPA)の採用で、ハイパフォーマとエリートパフォーマはローパフォーマーよりもそれぞれ 1.5 倍、 2.3 倍の高い採用率を示している。

次に、ハイパフォーマとエリートパフォーマのクラスタはローパフォーマーに比べてそれぞれ 3.6 倍、 4 倍多くの Job を実行している。セグメントには様々なアプローチがあるため、どの戦略が最もインパクトが大きいかは測定はできなかった。
この調査では、Cluster Autoscaler を最適化してより高速かつ積極的にスケールダウンさせるよりも、ワークロードの自動スケジュリーリングと完了までの Job の導入のほうが遥かに重要であった。

言い換えれば、開発者が需要に応じて適切にスケールするような構成にしない場合、プラットフォームチームがクラスタをスケールダウンする能力には限界がある。
エリートパフォーマを含む全てのセグメントで、HPA, VPA の採用を増やすことで需要ベースのオートスケーリングの効率を改善出来ることがわかった。

また、ハイパフォーマとエリートパフォーマは、ワークロードのベストプラクティスをより遵守している。
例えば、ハイパフォーマとエリートパフォーマはローパフォーマーと比べ最大で 2.2 倍、 2.8 倍ワークロードの適正化をしており、BestEffort Pod のデプロイは At Risk セグメントと比べ 50%, 70% 少なくなっている。

これらの結果は、開発者のアプリケーションに関するより優れた知識を反映したものであり、ツールやソリューションを構築できるプラットフォームチームに特化したハイ/エリートセグメントの価値を実証している。

その結果、継続的にポリシーを実施し、ゴールデンパスを提供し、ワークロードの適正化をし（例えばローパフォーマよりも VPA の Recommend mode が 12 倍、 16 倍多くデプロイ）、開発チームにベストプラクティスを提供する。

最後に、Kubernetes 上でデータ中心のアプリケーションを構築運用することの難しさにもかかわらず、コミュニティではステートフルなワークロードが 1.8 倍、 2 倍多くの StatefulSet を実行している。

[Data on Kubernetes 2022 research](https://dok.community/wp-content/uploads/2022/10/DoK_Report_2022.pdf) によると、データ中心のアプリケーションの利点は以下の通りである：
- スケーラビリティの容易さ
- ワークロードの管理を標準化する機能
- 開発から本番まで一貫した環境
- メンテナンスの容易さ
- セキュリティの向上
- リソースの利用改善
- レイテンシの影響を受けやすいワークロードの co-location
- 導入の容易さ


## まとめ
Kubernetes の多くの機能と外部ツールは、組織がプラットフォームから最高の信頼性、パフォーマンス、コスト効率を実現できるように、resource request を適切に設定することに依存している。

request を設定しない、request を超えるメモリを常に使用する Pod を意図せず過剰に利用すると、エンドユーザに悪影響を及ぼすなど予期せぬ動作が発生することがある。

Kubernetes コスト最適化のゴールデンシグナルが示すことに対処することが、継続的に優先されることであるのが重要である。
また、bin packing やクラウドプロバイダのディスカウントに主眼を置くのではなく、アプリケーションのサイジングを正しく行い、アプリケーションが需要に基づいて適切にスケールできることを有線すべきである。

Google は顧客とのコスト最適化の取り組みを通じて、大規模なマルチテナントクラスタに組織の Kubernetes 専門知識の大部分が集まる傾向にあることを特定した。その結果、ロングテールクラスタが、プラットフォームの管理ではなく、サービスの提供を主なお目的とするチームによって管理されるようになった。

このレポートでも、最大規模のクラスタが運用が最も難しい一方で、コスト最適化のパフォーマンスが最も優れていることが示されている。
課題を克服するために、組織は全社的なポリシー、パターン、事前承認パスの定義と実施に投資し、制御性、拡張性、柔軟性のバランスを見つける必要がある。

最後に、プラットフォームチームは cluster の  bin packing  だけでは全体像を把握できないことに注意する必要がある。
At Risk セグメントを見ると、bin packing の値が低く、ワークロード適正化の値が高い。

これは BestEffort Pods の過剰仕様と、BurstabE Pods のプロビジョニング不足を反映したもので、必要以上のリソースを使用するため結果的に両方のシグナルが歪んでいる。
このような状況で bin packing によりコスト節約をすると、信頼性の問題が発生する可能性がある。
