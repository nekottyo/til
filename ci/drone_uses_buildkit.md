Drone の plugin/docker で buildkit を使う
===

drone の中で docker image を build する plugin/docker でちょっと前に 18.09 が使えるようになってる
https://github.com/drone-plugins/drone-docker/pull/227


雰囲気で .drone.yml を編集してあげると(environment が必要かはわからない)

```diff
-   image: plugins/docker
+   image: plugins/docker:18.09
    dockerfile: ./docker/Dockerfile
+   target: production
    auto_tag: true
+   environment:
+     - DOCKER_BUILDKIT=1
```

使える
```
#1 [internal] load build definition from Dockerfile
#1       digest: sha256:174a08959d51507beac0d4f95d94024c127621ba7133f8eb737642389d05a66f
#1         name: "[internal] load build definition from Dockerfile"
#1      started: 2019-04-08 08:04:01.317228142 +0000 UTC
#1 transferring dockerfile: 2.16kB 0.2s done
#1    completed: 2019-04-08 08:04:01.72257637 +0000 UTC
#1     duration: 405.348228ms
```
