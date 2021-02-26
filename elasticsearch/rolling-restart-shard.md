# rolling restart 時に status green になるのが遅いとき


## ES 6.x

おもに3つ設定するとめちゃ速になる可能性がある
- `cluster.routing.allocation.node_concurrent_recoveries (default 2)`
    - https://www.elastic.co/guide/en/elasticsearch/reference/current/shards-allocation.html
    - cluster.routing.allocation.node_concurrent_{incoming,outgoing}_recoveries を設定する
    - restart 時の shards relocating の in/out の並列度を設定する
- `indices.recovery.max_bytes_per_sec (default 40mb)`
    - https://www.elastic.co/guide/en/elasticsearch/reference/current/recovery.html
    - index recovery 時の network の limit を設定する
    - (多分)最大で indices.recovery.max_bytes_per_sec * cluster.routing.allocation.node_concurrent_recoveries 分 network 帯域を使える
- `cluster.routing.allocation.node_initial_primaries_recoveries (default 4)`
    - https://www.elastic.co/guide/en/elasticsearch/reference/current/shards-allocation.html
    - 直接的には関係はないが、primary shards 持ちの node が死んだ時に primary shards の recovery 並列を設定する

## ES 7.x
試してないけどこの辺

https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-cluster.html#cluster-shard-allocation-settings

- `cluster.routing.allocation.node_concurrent_recoveries`
- `cluster.routing.allocation.node_initial_primaries_recoveries`
- `cluster.routing.allocation.cluster_concurrent_rebalance`
