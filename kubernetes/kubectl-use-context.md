kubectl config set-context と use-context
=====

kubectl config get-context で current context が取れる

```
 ~> kubectl config get-contexts
CURRENT   NAME                CLUSTER             AUTHINFO   NAMESPACE
          k8s.example.jp      k8s.example.jp      nekottyo   nekottyo
*         minikube            minikube            minikube
```

context 変えるときは use-context を使う

```
~> kubectl config use-context k8s.example.jp
Switched to context "k8s.example.jp".
```


get-context で取れるから set-context だろうな〜と雰囲気でやってたら間違ってた

ちゃんと help を読む！

```
 ~> kubectl config -h
Modify kubeconfig files using subcommands like "kubectl config set current-context my-context"

The loading order follows these rules:

  1. If the --kubeconfig flag is set, then only that file is loaded.  The flag may only be set once and no merging takes
place.
  2. If $KUBECONFIG environment variable is set, then it is used a list of paths (normal path delimitting rules for your
system).  These paths are merged.  When a value is modified, it is modified in the file that defines the stanza.  When a
value is created, it is created in the first file that exists.  If no files in the chain exist, then it creates the last
file in the list.
  3. Otherwise, ${HOME}/.kube/config is used and no merging takes place.

Available Commands:
  current-context Displays the current-context
  delete-cluster  Delete the specified cluster from the kubeconfig
  delete-context  Delete the specified context from the kubeconfig
  get-clusters    Display clusters defined in the kubeconfig
  get-contexts    Describe one or many contexts
  rename-context  Renames a context from the kubeconfig file.
  set             Sets an individual value in a kubeconfig file
  set-cluster     Sets a cluster entry in kubeconfig
  set-context     Sets a context entry in kubeconfig
  set-credentials Sets a user entry in kubeconfig
  unset           Unsets an individual value in a kubeconfig file
  use-context     Sets the current-context in a kubeconfig file
  view            Display merged kubeconfig settings or a specified kubeconfig file

Usage:
  kubectl config SUBCOMMAND [options]
```
