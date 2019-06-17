外部 IP を service で解決する
=====


[Services without selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors)


```yaml
---
apiVersion: v1
kind: Endpoints

metadata:
  name: mysql
subsets:
  - addresses:
      - ip: 192.168.1.1
    ports:
      - port: 80

---
apiVersion: v1
kind: Service

metadata:
  name: mysql
spec:
  type: ClusterIP
  ports:
    - port: 8080
      targetPort: 80
```


https://qiita.com/toshihirock/items/7b292218ce82427a40db
