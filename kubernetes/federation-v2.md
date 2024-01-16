federation-v2
======

federation-v2 を体験するために userguide をやってみる

https://github.com/kubernetes-sigs/federation-v2/blob/master/docs/userguide.md

## 準備
minikube https://github.com/kubernetes-sigs/federation-v2/blob/master/docs/environments/minikube.md

```sh
$ minikube start -p cluster1
$ minikube start -p cluster2
```

## install binary

`kubebuilder`, `etcd`, `kubectl`, `kube-apiserver` を install する

```sh
$ mkdir -p $GOPATH/src/github.com/kuberentes-sigs/ ; cd $_
$ git clone https://github.com/kubernetes-sigs/federation-v2.git

$ ./scripts/download-binaries.sh
$ export PATH=$(pwd)/bin:${PATH}
```

# Deployment image

check context
```sh
$ kubectl config use-context cluster1

$ k config current-context
cluster1
```

helm init をしてなかったらする
```sh
$ helm init
```

create namespace
```sh
$ kubectl create ns kube-multicluster-public
```

deploy chart
README では charts/federation-v2 となっているが、global から探すので相対パスにする

```sh
$ helm install ./charts/federation-v2 --name federation-v2 --namespace federation-system
```

## Operations

Join Clusters
```sh
$./bin/kubefed2 join cluster1 --cluster-context cluster1 \
    --host-cluster-context cluster1 --add-to-registry --v=2
$./bin/kubefed2 join cluster2 --cluster-context cluster2 \
    --host-cluster-context cluster1 --add-to-registry --v=2
```

check
できてるっぽい
```sh

$ kubectl -n federation-system describe federatedclusters
Name:         cluster1
Namespace:    federation-system
Labels:       <none>
Annotations:  <none>
API Version:  core.federation.k8s.io/v1alpha1
Kind:         FederatedCluster
Metadata:
  Creation Timestamp:  2019-01-28T06:03:49Z
  Generation:          1
  Resource Version:    11121
  Self Link:           /apis/core.federation.k8s.io/v1alpha1/namespaces/federation-system/federatedclusters/cluster1
  UID:                 7acc2c04-22c2-11e9-9886-0800271c66ad
Spec:
  Cluster Ref:
    Name:  cluster1
  Secret Ref:
    Name:  cluster1-gntm7
Status:
  Conditions:
    Last Probe Time:       2019-01-28T06:31:44Z
    Last Transition Time:  2019-01-28T06:04:22Z
    Message:               /healthz responded with ok
    Reason:                ClusterReady
    Status:                True
    Type:                  Ready
Events:                    <none>


Name:         cluster2
Namespace:    federation-system
Labels:       <none>
Annotations:  <none>
API Version:  core.federation.k8s.io/v1alpha1
Kind:         FederatedCluster
Metadata:
  Creation Timestamp:  2019-01-28T06:04:00Z
  Generation:          1
  Resource Version:    11122
  Self Link:           /apis/core.federation.k8s.io/v1alpha1/namespaces/federation-system/federatedclusters/cluster2
  UID:                 818f130f-22c2-11e9-9886-0800271c66ad
Spec:
  Cluster Ref:
    Name:  cluster2
  Secret Ref:
    Name:  cluster2-82hb7
Status:
  Conditions:
    Last Probe Time:       2019-01-28T06:31:44Z
    Last Transition Time:  2019-01-28T06:04:22Z
    Message:               /healthz responded with ok
    Reason:                ClusterReady
    Status:                True
    Type:                  Ready
Events:                    <none>
```

Enabling federation of an API type

federation 用 CRD を作る

```sh
$ kubefed2 federate enable  federatednamespaceplacement
$ kubefed2 federate enable  federatedtypeconfig
$ kubefed2 federate enable  clusterrolebinding
```

Namespace を作る
```sh
$ kubectl apply -f example/sample1/federatednamespace-template.yaml \
    -f example/sample1/federatednamespace-placement.yaml
```

Resource 当てる
```sh
$ kubectl apply -R -f example/sample1
```

Resource 見る
`cluster1`, `cluster2` 両方に Resource が出来ているのが分かる
```sh
for r in configmaps secrets service deployment serviceaccount job; do
    for c in cluster1 cluster2; do
        echo; echo ------------ ${c} resource: ${r} ------------; echo
        kubectl --context=${c} -n test-namespace get ${r}
        echo; echo
    done
done
```

nginx が両方で動いている
```sh
for c in cluster1 cluster2; do
    NODE_PORT=$(kubectl --context=${c} -n test-namespace get service \
        test-service -o jsonpath='{.spec.ports[0].nodePort}')
    echo; echo ------------ ${c} ------------; echo
    curl $(echo -n $(minikube ip -p ${c})):${NODE_PORT}
    echo; echo
done
```


pach で `cluster2` を外す
```sh
$ kubectl -n test-namespace patch federatednamespaceplacement test-namespace \
    --type=merge -p '{"spec":{"clusterNames": ["cluster1"]}}'
```

`cluster2` の Resource が全て消されている
```sh
for c in cluster1 cluster2; do
    NODE_PORT=$(kubectl --context=${c} -n test-namespace get service \
        test-service -o jsonpath='{.spec.ports[0].nodePort}')
    echo; echo ------------ ${c} ------------; echo
    curl $(echo -n $(minikube ip -p ${c})):${NODE_PORT}
    echo; echo
done
```

cluster 設定を両方に戻すと

```sh
$ kubectl -n test-namespace patch federatednamespaceplacement test-namespace \
    --type=merge -p '{"spec":{"clusterNames": ["cluster1", "cluster2"]}}'
```

`cluster1`, `cluster2` で動いている

```sh
for r in configmaps secrets service deployment serviceaccount job; do
    for c in cluster1 cluster2; do
        echo; echo ------------ ${c} resource: ${r} ------------; echo
        kubectl --context=${c} -n test-namespace get ${r}
        echo; echo
    done
done
```
