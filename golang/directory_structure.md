Golang directory structure
---


- GopherCon 2018 - How Do You Structure Your Go Apps?
    - https://github.com/sourcegraph/about/blob/master/blogposts/gophercon-2018-how-do-you-structure-your-go-apps.md
    - https://github.com/katzien/talks/blob/master/how-do-you-structure-your-go-apps/londongophers-2018-09-19/slides.pdf
        - Flat structure
            - 全部 root に置く
            - fiel 増えると死ぬ
        - Layerd
            - いわるゆ MVC 的な使い方
        - Modular
            - context 毎にまとめる。DDD
            - ![img](https://user-images.githubusercontent.com/4897310/44753609-e6b54800-aadb-11e8-8d3e-73698cc0da4a.png)
        - Actor model
        - Hexagonal Architecture
            - ![img](https://user-images.githubusercontent.com/4897310/44753508-90480980-aadb-11e8-8362-7f01b5b1fb7f.png)
            - Hexmodel は内側にのみ依存する


- Go Project Layout
    - https://medium.com/golang-learn/go-project-layout-e5213cdcfaa2
    - tow most common patterns
    - このパターンは or ではなく and もありうる
        - `cmd` layout pattern
            - 1つ以上の binary が必要なときによい
                - `your_project/cmd/your_app`
            - main を持っているので、それぞれのパッケージが `go get` 可能
            - `cmd/your_app` 以下で上の structure を作るパターン
            - Kubernetes, Moby, Prometheus, Influxdb
        - `pkg` layout pattern
            - `pkg` directpory には public libraries を置く
                - といいつつ、internal なライブラリを置くパターンもある
                - public libraries は `pkg` に置いて、internal はそれ以外もある
            - 内部/外部から利用する
            - Kubernetes, Moby, Grafana, InfluxDB, Etcd
        - `internal` directory
            - `internal` というディレクトリを用意して private package を置く
                - internal shared libraries を `your_project/internal/pkg/your_private_lib`
                - 他に import されたくないコードを `your_project/internal/app/your_app` に置く
                - `cmd` をそれらのエントリーポイントとして使うことで、`main` を軽い実装に出来る
            - 比較的新しい
            - Dep, Docker, Nsq, Go Ethereal, Counter


