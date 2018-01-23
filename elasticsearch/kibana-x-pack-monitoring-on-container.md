Kibana X-pack monitoring で "CPU Usage", "CPU Throttling" が 0 になる
=====

# 症状

cpu 使用率とか load average がでない

![monitoring](https://cdn-enterprise.discourse.org/elastic/uploads/default/original/3X/b/7/b720d23c1d2c49348c9dd0ed3a3170bfc55a21c7.jpg)


- kibana は docker: docker.elastic.co/kibana/kibana:5.6.5
- elasticsearch はコンテナではない


# 解決策

だいたいここ

[Kibana Monitoring Elasticsearch nodes doesn’t show CPU usage](https://discuss.elastic.co/t/kibana-monitoring-elasticsearch-nodes-doesnt-show-cpu-usage/86505)

kibanaに `xpack.monitoring.ui.container.elasticsearch.enabled: false` を食わせれば良い

https://www.elastic.co/guide/en/kibana/current/monitoring-settings-kb.html

> xpack.monitoring.ui.container.elasticsearch.enabled
>>  For Elasticsearch clusters that are running in containers, this setting changes the Node Listing to display the CPU utilization based on the reported Cgroup statistics. It also adds the calculated Cgroup CPU utilization to the Node Overview page instead of the overall operating system’s CPU utilization. Defaults to false.

- container 用の設定で Cgroup を見る。ノードの情報を見たかったら `false` にする
- [docker.elastic.co/kibana/kibana:5.6.5](https://github.com/elastic/kibana-docker/blob/5.6/build/kibana/config/kibana.yml#L9) は true
    - container で動かしていたら Environment に `XPACK_MONITORING_UI_CONTAINER_ELASTICSEARCH_ENABLED: false` を与える

