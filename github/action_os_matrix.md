そのスクリプト、MacOS でも Linux でも動くのか
===

## 背景
ツール (ちょっとした ShellScript) を作ってる時に利用者の実行環境を縛れないケースのあるあるだと思うが、特に Mac はデフォルト BSD コマンドなので自分環境では動くけど他人にやらせたら実行できないみたいなのがある。
なのでそれを事前に検出して潰したい。

Golang で作って goreleaser とかで multi arch build すりゃいいじゃん？というのはそれはそうで、HomeBrew/LinuxBrew 用に Fomula 合わせて更新していくみたいなのが筋がいいとは思っている 。
用意が面倒。

あとは試してないけど Docker image に包んで multi-arch で配るのも考えられる。

いずれにせよ環境差分を考えるのは面倒である。

## やりかた
便利なことに GitHub Actions で runs-on に ubuntu, macos, windows が選べる。

GitHub workflow で matrix を使って、同じ step を複数の os で実行し、pass するかを見る。

ちなみに指定できるのはここで作られている。
https://github.com/actions/runner-images


例えばこんな感じで、
```yaml
name: os matrix

on:
  pull_request:

jobs:
  sample:
    strategy:
      matrix:
        os:
          - ubuntu-latest
          - macos-latest
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v3
...
```

みたいな雰囲気でいい感じに書ける。
便利。
