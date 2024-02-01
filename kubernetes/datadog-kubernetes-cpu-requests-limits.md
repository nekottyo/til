A deep dive into CPU requests and limits in Kubernetes
===

URL: https://www.datadoghq.com/blog/kubernetes-cpu-requests-limits/


----


## Kubernetes での Linux scheduling

デフォルトでは [CPU manageement policy](https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/#cpu-management-policies) は [Completely Fair Scheduler(CFS)](https://www.kernel.org/doc/Documentation/scheduler/sched-design-CFS.txt) が設定されている。
CFS のかわりに `static` を設定することで [Linux CPUSet](https://www.kernel.org/doc/Documentation/cgroup-v1/cpusets.txt) を利用することも出来る。これは、特定の特性をもつ pod に CPU の排他的な利用ができるようになる。

> ![NOTE]
> kubecet option の `cpuManagerPolicy` で設定する
> `none`　が defatult(CFS) で、`static` が Linux CPUSet を利用する
> https://kubernetes.io/docs/reference/config-api/kubelet-config.v1beta1/

## CPU manager の default policy (`none`)

### CPU request

kubernetes sceduler は、node に pod を配置するときに各 container の CPU request を考慮する。node で CPU が利用される場合、CFS は CPU request を利用して CPU 時間を各 container に割り当てる。

1 CPU コアを持つ 1 node と 3 つの pod (1 container 1 thread)があり、それぞれ pod1 が 200, pod2 が 400, pod3 が 200 millicore の CPU request を持つとする。request の合計は 1 CPU コア未満なため、スケジューラはすべての pod を node に配置できる。

100 ミリ秒の time slice では、pod1 は 20 ミリ秒、pod2 は 40 ミリ秒、pod3 は 20 ミリ秒の CPU が利用出来ることが保証される。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-1-final.png?auto=format&fit=max)

例えば、pod1 がアイドル状態の場合、node には CPU サイクルの余裕があるため、pod2 が 50 ミリ秒、 pod3 が 40 ミリ秒でアイドルが 10 ミリ秒といった割当になる。
![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-2-final.png?auto=format&fit=max)

CPU 利用の競合が発生した場合、CFS は CPU request を利用して、request に比例して割り当てる。例えば、全ての CPU が node の CPU を超える CPU の利用要求をした場合、 1:2:1 の割合（pod1 200, pod2 400, pod3 200 のため）で割り当てられることとなり、100 ミリ秒の time slice では、pod1 25 ミリ秒、 pod2 50 ミリ秒、 pod3 25 ミリ秒と振り分けられる。
![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-3-final.png?auto=format&fit=max)

### 監視すべき metrics: CPU usage, CPU request

CPU request は、サイクルあたりの最小の CPU 時間を保証する。また、コンテナに CPU 時間を比例的に割当てるために CFS によっても使用される。
サービスの CPU 使用率を継続的に監視し、 CPU リクエストが使用率に近いことを確認することが重要である。

`kubernetes.cpu.usage.total` は、使用されているナノコアの数を報告し、`kubernetes.cpu.requests` は、要求された CPU コアの数報告する。

Datadog では [Kubernetes Resource Utilization](https://app.datadoghq.com/orchestration/resource/pod?groups=kube_deployment%2Ckube_namespace%2Ckube_cluster_name&panel_tab=metrics&pg=0&resource_mode=cpu&resource-na-groups=false) で簡単に調べることが出来る。
各 pod の平均 CPU 使用率が CPU request と limit の割合として色分けして表示される。例えば、黄色の値はリクエストに対する CPU 使用率の比率が低い pod を表し、過剰な resource が割り当てられている可能性がある。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-resource-utilization-view.png?auto=format&fit=max)


### CPU limit
CPU request はスケジューリング、 CPU 時間の保証、 CPU 時間の比例分配に利用されていることがわかった。

対して、CPU limit は、node の状況に関わらずコンテナが特定の time slice で利用できる CPU 時間の量の制限を設定する。
前の例に以下のような CPU limit が設定された場合を考える。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-4-final.png?auto=format&fit=max)

100 ミリ秒の期間で、 pod1 が最大 20 ミリ秒、pod2 が最大 40 ミリ秒、 pod3 が最大 20 ミリ秒の CPU を利用できる。


CPU limit がなかった例を再度見ると、pod2, pod3 は request 以上の CPU 利用がある場合以下のような状態になる。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-2-final.png?auto=format&fit=max&w=698)

CPU limit を設定した場合、node に十分な CPU が利用可能であっても、 pod2, pod3 はスロットルされ、pod2 が 40 ミリ秒、pod3 が 20 ミリ秒のみ使用できる。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-5-final.png?auto=format&fit=max)

コンテナに CPU limit を設定していて、アプリケーションの p95, p99 latency の急増が見られる場合は、CPU limit が原因である可能性がある。

### CPU throttle

Datadog Agent では、次の 2 つの metrics で container がスロットルされているかどうかを確認できる。
- `container.cpu.throttled`: container がスロットルされたナノ秒（gauge)
- `container.cpu.throttled.period` container がスロットルされた CFS 期間の数（gauge)


### CPU limit の設定
CFS では、CPU request は最小 CPU 時間を保証し、CPU 時間を比例的割り当てる。CPU limit は余裕がある場合でもアプリケーションがスロットルされる可能性がある。

以下のような状況で、CPU limit を設定することが有効である。

#### ベンチマーク
CPU request, CPU limit を同じ値に設定する事で、CPU ニーズが request よりも大きい場合にアプリケーションがスロットルされるようにできる。スロットルに関する metrics を見ることで、頻度を特定実稼働環境の前に CPU リクエストを調整できる。

#### マルチテナント環境
複数のテナントを管理する場合、通常 CPU を含む[ResourceQuota](https://kubernetes.io/docs/concepts/policy/resource-quotas/)を設定することで、namespace の総リソース消費量を制限できる。

#### 予測可能性
コンテナの CPU request が低く設定され、CPU limit が request よりも大きい場合、もしくは limit が設定されていないケースで、アプリケーションが request 以上の CPU を定期的に利用する場合 node の利用可能な CPU 量が低下するパフォーマンスが低下する。

アプリケーションの CPU limit を CPU request に等しいか近くなるようにすれば、node の CPU の取り合いが発生してからではなく、新しい CPU の需要について早く気付けるようになる。

### 保証されたサービス品質
Kubernetes のすべての pod は 3 つの [QoS クラス](https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/) のいずれかに属する。これは、`Guaranted`, `Burstable`, `BestEffort` のいずれかに割り当てられる。
QoS クラスは、node pressure が発生した場合にどの pod が削除されるかを決定するために利用される。

`Guaranted` クラスは pod のすべての container(init contianer を含む）が、CPU と Memory で request と limit の値が同じである場合に割り当てられる。



## CPU manager の static policy (`static`)
ここまでは、CPU manager の default policy である `none` について説明した。このポリシーでは CPU request を使用して各 container に CPU を割り当て、CPU limit は CFS quota によて矯正される。ただし、この policy は node のどの CPU が特定の pod のワークロードを実行するかは指定されない。時間とともにワークロードの task, thread はある CPU から別の CPU に移行する可能性がある。多くのワークロードでは問題にはならない。

特定のワークロードは、コンテキストスイッチ、 CPU のキャッシュミス、 ソケット間のメモリトラフィックの影響を受けやすい可能性がある。このような場合 CPU manager の policy を `static` に設定し、[Linux CPUSet](https://www.kernel.org/doc/Documentation/cgroup-v1/cpusets.txt) を利用して container を特定のコアのセットに固定出来るようになる。

`static` policy を使用すると、`Guaranted` QoS クラスの pod は、CPU request と limit が同じ値である場合にのみ割り当てられ、整数の CPU コアをリクエストする pod 内の container は、そのコアへの排他的アクセスを取得する。

`static` policy を有効する場合、CPU resource を system daemon に予約する必要がある。

例えば、4 コアを持つ 1 つのノードがあるとする。
![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-6-final.png?auto=format&fit=max)

kubelet の CPU manager を `static` policy に設定し、`kube-reserved` を `cpu=250m`, `system-reserved` を `cpu=250m` に設定するとする。

これにより、この node には 3.5 コア分の CPU が利用可能になる。ただし、排他的ワークロードは CPU request の整数値が必要なため、残りの 3 コア（コア 1〜3) が排他的ワークロードで利用できる。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-7-final.png?auto=format&fit=max)

ここに、pod1, pod3 が CPU request のみ、pod 2 は CPU request と limit が 2 コアとすると、pod1,3 は共有プール（コア 0, 1) で実行され、pod2 はコア 2,3 を排他利用できる。

![](https://imgix.datadoghq.com/img/blog/kubernetes-cpu-requests-limits/kubernetes-cpu-requests-limits-diagram-10-final.png?auto=format&fit=max)

## 注目すべきメトリクス
`static` の　CPU manager を使用する場合、`container.cpu.limit` が便利なメトリクスとなる。これは、コンテナで利用可能な最大の CPU 時間（ナノコア）の gauge で、CPU limit が設定されている場合常に同じ値が報告される。
ただし、pod 内のコンテナに limit が設定されていない場合同じ node 上の割当可能な CPU の数によって値が変わる。

## CPU request と CPU limit に関するデバッグ
Kubernetes では、コンピューティングリソースの管理を container の CPU request, limit などを通じて抽象化される。これらがアプリケーションにどのような影響を与えるかを理解することは、潜在的なパフォーマンスの問題をデバッグするのに役立つ。
