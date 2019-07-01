git の tag は branch に紐付かない
===

GitHub 側から release を打つ時に Web から release をすると branch の選択画面があるため、
tag と branch の間でヒモ付けがあると思っていた



drone 1.0 で push event と tag event 両方を trigger としたかったので以下のようにしていたが tag は trigger されずうまくいかない

```yaml
kind: pipeline
name: default
steps:
  - name: build container
    image: plugins/docker
    settings:
      repo: hoge/fuga
      environment:
        - DOCKER_BUILDKIT=1
    when:
      branch:
        - master
        - develop
      event:
        - push
        - tag
```


よくよく考えると refs/tags だよなと思って show-ref を見ると

```console
➜ git show-ref
ed0e23fcba04b46649600a23b46371299f3eebce refs/heads/develop
79578ef701aa507a89b512cb52bc9781769eab29 refs/heads/master
ed0e23fcba04b46649600a23b46371299f3eebce refs/remotes/origin/develop
79578ef701aa507a89b512cb52bc9781769eab29 refs/remotes/origin/master
295322817aeb6386dbf8fba57e32547d80e4aada refs/tags/v0.1.0
3a61d3afba80489c72b649b7940bf58d0ad23d40 refs/tags/v0.1.10
50d0df1d398d3e22b239fbe5a55888e3abee74bd refs/tags/v0.1.11
a2a7d9cb48bb685f6f3589e85f598e76d964c3ff refs/tags/v0.1.2
```
とのことなので


```yaml
    when:
      ref:
        include:
          - refs/heads/develop
          - refs/heads/master
          - refs/tags/*
      event:
        - push
        - tag
```
でうまくいった
