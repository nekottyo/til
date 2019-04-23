BuildKit `--mount-type=cache` の Tips
=====

# COPY `--from` 問題
Multi Stage Build で`target=/hoge` の path の先を COPY  で引っ張ろうとすると虚無なので注意が必要

つまり 成果物は cache できない



# cache は共有問題
`--mount=type=cache` はデフォルトで shareing=shared なので後に走った apt-get が /var/lib/apt/lists/lock を取れなくて詰む

こんな dockerfile にすると

```dockerfile
# syntax = docker/dockerfile:experimental


FROM debian as a
RUN \
  --mount=type=cache,target=/var/cache/apt \
  --mount=type=cache,target=/var/lib/apt\
    apt-get update \
    && apt-get install -y  --no-install-recommends \
        build-essential 
 
FROM debian as b
RUN \
  --mount=type=cache,target=/var/cache/apt \
  --mount=type=cache,target=/var/lib/apt\
    apt-get update \
    && apt-get install -y  --no-install-recommends \
        build-essential 
```
lock が取れない

``` 
#9 2.985 Reading package lists...
#9 3.454 E: Could not get lock /var/lib/apt/lists/lock - open (11: Resource temporarily unavailable)
#9 3.454 E: Unable to lock directory /var/lib/apt/lists/
```

なので shareing=private にする

https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/experimental.md#run—mounttypecache

```dockerfile
RUN \
  --mount=type=cache,target=/var/cache/apt,sharing=private \
  --mount=type=cache,target=/var/lib/apt,sharing=private \
 ```
