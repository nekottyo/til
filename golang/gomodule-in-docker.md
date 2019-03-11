Go Modules に linter は含めたくないけど development 用 image には含めたい
=====

依存関係を Go Modules で管理している時、linter を愚直に install しようとすると

```dockerfile
FROM golang as gomod
COPY go.mod ./
COPY go.sum ./
RUN go mod download

RUN go get 
    github.com/rakyll/gotest \
    golang.org/x/lint/golint
    ...
```
のようになってしまい、 `gotest` などが `go.mod` 管理下に置かれてしまう

そこで、multi-stage-build を使い、GOPATH 内で linter 系を入れるようにすると、Go Modules 配下ではないがコンテナに含めることが出来る。


```dockerfile
# set go version
# ------------------------------------------------
ARG GO_VERSION=1.12


# alias image
# ------------------------------------------------
FROM golang:${GO_VERSION}-alpine as golang
ENV GO111MODULE=auto
WORKDIR /src


# base image for build dependency
# ------------------------------------------------
FROM golang as builder
RUN apk add --no-cache \
    ca-certificates \
    curl \
    g++ \
    gcc \
    git \
    libc-dev \
    make


# install dependency
# ------------------------------------------------
FROM builder as gomod
COPY go.mod ./
COPY go.sum ./
RUN go mod download


# install external packages
# ------------------------------------------------
FROM builder as external
WORKDIR /go
RUN go get \
    github.com/rakyll/gotest \
    github.com/go-delve/delve/cmd/dlv \
    github.com/cortesi/modd/cmd/modd \
    honnef.co/go/tools/cmd/staticcheck \
    golang.org/x/lint/golint \
    github.com/mgechev/revive \
    mvdan.cc/unparam \
    github.com/axw/gocov/gocov \
    github.com/matm/gocov-html


# runtime image
# ------------------------------------------------
FROM builder as runtime
COPY --from=gomod /go /go
COPY . .


# runtime with external
# ------------------------------------------------
FROM runtime as dev-runtime
COPY --from=external /go /go


# develop image
# ------------------------------------------------
FROM dev-runtime as development
ENTRYPOINT ["docker/docker-entrypoint.sh"]
```
