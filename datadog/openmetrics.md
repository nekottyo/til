DataDog Agent による OpenMetrics 形式の AutoDiscovery

DD 公式の doc ははちゃめちゃにわかりにくい

## OpenMetrics integration
https://docs.datadoghq.com/ja/integrations/openmetrics/
Pod が OpenMetrics(Prometheus) 形式で metrics export してくれている場合、Pod Annotation を書くだけで DataDog Agent が吸って投げてくれる。

## 基本的な書き方
https://docs.datadoghq.com/ja/agent/kubernetes/prometheus/
pod annotation に `ad.datadoghq.com/<CONTAINER NAME>.*` を基本として埋める
実際には deployment とか StateFullSets とかの `spec.template.metadata.annotations` に書くことになると思う

```yaml
    annotations:
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.check_names: |
                        ["openmetrics"]
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.init_configs: |
                        [{}]
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.instances: |
            [
              {
                "prometheus_url": "http://%%host%%:%%port%%/<PROMETHEUS_ENDPOINT> ",
                "namespace": "<METRICS_NAMESPACE_PREFIX_FOR_DATADOG>",
                "metrics": [{"<METRIC_TO_FETCH>":"<NEW_METRIC_NAME>"}]
              }
            ]            
```
ここの `CONTAINER_IDENTIFIER` は `spec.template.spec.containers.name` と一致させる必要がある
`check_names` は DD integration の名前なので `openmetrics` 固定（ほかに integration 入れるなら足す 
```yaml
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.check_names: |
                        ["openmetrics"]
```
ここはほぼ多分固定
```yaml
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.init_configs: |
                        [{}]
```
ここは自由にかける。

これ以外にも以下の url にある option を書ける。agent にわたす config の reference はあり、annotation 形式に書けるとはどこにも書いていないが書ける。
https://docs.datadoghq.com/ja/integrations/guide/prometheus-host-collection/#parameters-available

`prometheus_url` は agent から探しに行く path
port がない場合は pod の default port が補完されるかもしれん。
`namespace` は dd に投げるときにつける prefix
例えば openmetrics 形式で `foo` が export  されていて、 `namespace: bar` にした場合は、 DD 上では `bar.foo` として見える

```yaml
        ad.datadoghq.com/<CONTAINER_IDENTIFIER>.instances: |
            [
              {
                "prometheus_url": "http://%%host%%:%%port%%/<PROMETHEUS_ENDPOINT> ",
                "namespace": "<METRICS_NAMESPACE_PREFIX_FOR_DATADOG>",
                "metrics": [{"<METRIC_TO_FETCH>":"<NEW_METRIC_NAME>"}]
              }
            ]            
```


`metrics` は list と dict  両方がかける
以下のようにすると export されている metrics を全部拾うが、openmetrics は custom metrics なので普通にお金がかかるため必要な metrics に **絶対に** 絞ったほうがよい。
```
"metrics": ["*"] 
```
以下のような感じ。
```
"metrics": ["foo*"]
```

metrics mutation したい場合は mapping を書く
`foo` が `<NAMESPACE>.bar_hoo` で投げられる
```
"metrics" {
  "foo": "bar_foo"
```

## Advanced

また、OpenMetrics 側で、例えば `hoo{name="hoge"}` みたいな metrics を投げていて、DD agent がつける `name` (ここでは EC2 の instance name の `name`) と conflict するような場合、`labels_mapper` で label 側を張り替えれる。
2 つ tag (label) がついていると、sum() とか rollup とかがうまく動かないので極力ばらしたほうが良い。

例えば `hoge{name="bar"}` の metrics を吐いていた場合以下のような config は DD 上では
`foo.hoge{app_name="bar}` として投げられる。
```yaml
        ad.datadoghq.com/application-controller.instances: |
          [
            {
              "prometheus_url": "http://%%host%%:8082/metrics",
              "namespace": "foo",
              "metrics": [
                 "hoge"
              ],
              "labels_mapper": {
                "name": "app_name"
              }
            }
          ]
```

