# Docker Meetup Tokyo #20 @speee
- 2017/12/14

## nvidia-docker 2.0について，大嶋悠司，NTT

k8s で gpu cluster 管理したい!

https://www.slideshare.net/Oshima0x3fd/kubernetesgpu

- nvidia-docker2, nvidia-container-runtimeを使う
	- OCI runtime spec 準拠の container runtime
- k8s -> docker -> containerd -> nv-runtime
	- resources に指定してあげれば k8s でのスケジューリングができる
- k8s -> cli-containerd -> containerd -> nv-runtime

## KubeCon/CNCon NA 2017 Recap, by Takuya Noguchi, iRIdge, Inc.

- KubeCon/CNCon の話
- orchestration tool は kubernetes 一択な雰囲気
- trend
	- service mesh!!
	- security
	- serverless

## Docker for Mac beta with Kubernetes, by Akihiro Suda, NTT

- mac の docker で kubernetes が操作できる
- swarm 的に `docker stack deploy` とかすると kubernetes deploy できるようになっちゃう
	- docker-compose.yml で kubernetes に deploy できる
	- `docker stack ls` と `kubectl get stack` は同じ使い方ができる

## About Moby Project, by Kunal Kushwaha, NTT

https://speakerdeck.com/kunalkushwaha/moby-project

- about moby
	- container running のための components
	- runC
		- low level container runtime
		- OCI specification
		- config.json + rootfs
		- create, exec, checkpoint, start,pause,exit
	- Containerd
		- high level container runtime
		- simplicity
		- robustness
		- portabilly(OS, container-runtime)
	- LinuxKit
		- minimal で imutable な linux が yaml で作れる
## LT

- "OK Google, Create Kubernetes Clusters"
- 時代は service mesh!!
