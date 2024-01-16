helm-docs を使って document の治安維持をする
===


Helm Chart の README を自動生成する。


helm-docs では、`values.yaml` に `# --` で始まるコメントがついていると、その value の key の description と解釈される。
そのため、values でコメントを書く場合は、以下のように書く。

https://github.com/norwoodj/helm-docs

ex)
```yaml
config:
  databasesToCreate:
    # -- default database for storage of database metadata
    - postgres

    # -- database for the [hashbash](https://github.com/norwoodj/hashbash-backend-go) project
    - hashbash
```

なお、type は values から `int`, `string`, `float`, `bool` を類推するが、`# -- (<types>)` で型ヒントを与えることが出来る。

```yaml
controller:
  # -- (int) Number of nginx-ingress pods to load balance between
  replicas:

  # controller.image -- (string) Number of nginx-ingress pods to load balance between
  image:
```
