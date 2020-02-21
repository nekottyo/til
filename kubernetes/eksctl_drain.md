eksctl で安全に nodegroup を入れ替える
---

`eksctl drain nodegroup --cluster=<cluster> --nodegroup=<nodegroup>`

cordon されたあとに drain される
eksctl が 0.7.0 以上だったら drain が retry される


旧 ndegroup に pod が残っていないかを見るには

```bash
k get po -o wide --all-namespaces | grep -v $(k get no -o name -l alpha.eksctl.io/nodegroup-name=nodegroup3 |xargs -n1 | xargs -n1 -I{} echo -n '-e {} ')
```



ref.
- https://cstoku.dev/posts/2018/k8sdojo-21/
