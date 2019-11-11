[GoogleContainerTools/container-structure-test](https://github.com/GoogleContainerTools/container-structure-test)
---

Docker image に対してテストが書ける。

[nekottyo/docker-kustomize-kubeval/config.yaml](https://github.com/nekottyo/docker-kustomize-kubeval/blob/b31709e35dc5b3805f7a74108bf6d7af2313ddfa/config.yaml)


ex) 

```yaml
schemaVersion: 2.0.0

commandTests:
  - name: "kubectl test"
    command: "kubectl"
    args: ["version", "--client"]
    expectedOutput: ["Client Version:.*"]

  - name: "kustomize test"
    command: "kustomize"
    args: ["version"]
    expectedOutput: ["Version:.*"]

  - name: "kubeval test"
    command: "kubeval"
    args: ["--version"]
    expectedOutput: ["Version:.*"]
```

- `commandTests`: コマンドを実行して期待する出力, エラー出力で判断
- `fileExistenceTests` 指定したファイルパスに指定した uid, gid などで配置されているか
- `fileContentTests` 指定したファイルの中身のテスト
- `metadataTest`: Docker image のテスト
  - label がついているか, expose port がただし以下, CMD, WORKDIR がただしいか
- `globalEnvVars`: 指定した環境変数があるか



```
$ container-structure-test test -c config.yaml -i <image name>
```

https://circleci.com/gh/nekottyo/docker-kustomize-kubeval/8
