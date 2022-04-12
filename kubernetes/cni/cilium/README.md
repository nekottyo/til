cilium with kind
===

https://piotrminkowski.com/2021/10/25/kubernetes-multicluster-with-kind-and-cilium/


---
## configure kind

別途 image 指定
ref. https://qiita.com/yoichiwo7/items/184cfa25d205ea900a4b

- [./kind-c1-config.yaml](./kind-c1-config.yaml)
- [./kind-c2-config.yaml](./kind-c2-config.yaml)

```console
❯ kind create cluster --name c1 --config ./kind-c1-config.yaml
❯ kind create cluster --name c2 --config ./kind-c2-config.yaml
```


```
❯ d pull quay.io/cilium/cilium:v1.11.1
❯ kind load docker-image quay.io/cilium/cilium:v1.11.1 --name c1
❯ kind load docker-image quay.io/cilium/cilium:v1.11.1 --name c2
```

```
❯ helm install cilium cilium/cilium --version 1.11.1 \
   --namespace kube-system \
   --set nodeinit.enabled=true \
   --set kubeProxyReplacement=partial \
   --set hostServices.enabled=false \
   --set externalIPs.enabled=true \
   --set nodePort.enabled=true \
   --set hostPort.enabled=true \
   --set cluster.name=c1 \
   --set cluster.id=1

W0205 18:14:59.032994   74644 warnings.go:70] spec.template.spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms[1].matchExpressions[0].key: beta.kubernetes.io/os is deprecated since v1.14; use "kubernetes.io/os" instead
W0205 18:14:59.033030   74644 warnings.go:70] spec.template.metadata.annotations[scheduler.alpha.kubernetes.io/critical-pod]: non-functional in v1.16+; use the "priorityClassName" field instead
NAME: cilium
LAST DEPLOYED: Sat Feb  5 18:14:38 2022
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
You have successfully installed Cilium with Hubble.

Your release version is 1.11.1.

For any further help, visit https://docs.cilium.io/en/v1.11/gettinghelp
```
