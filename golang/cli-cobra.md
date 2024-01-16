# [cobra](https://github.com/spf13/cobra)
Go の cli フレームワーク。Moby(Docker)や Kubernetes の cli tool としても使われている。
## init
`github.com/nekottyo/go-cli` を作る
```bash
$ cobra init github.com/nekottyo/go-cli -a "nekottyo" -l MIT
```
`$GOPATH/src/github.com/nekottyo/go-cli` にディレクトリが作られる
```bash
$ cd $GOROOT
$ tree
.
├── cmd
│   └── root.go
├── LICENSE
└── main.go
```
すでに cli として実行可能な状態
```bash
$ go run main.go
A longer description that spans multiple lines and likely contains
examples and usage of using your application. For example:
Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.
```
## add command
add commnad を足してみる
```bash
$ cobra add
cobra add add
add created at $GOPATH/src/github.com/nekottyo/go-cli/cmd/add.go
```
`cmd/add.go` が作成される  
足し算をする
```add.go
package cmd
import (
    "fmt"
    "strconv"
    "github.com/spf13/cobra"
)
// addCmd represents the add command
var addCmd = &cobra.Command{
    Use:   "add",
    Short: "Add tow values.",
    Long: `Add tow values`,
    Run: func(cmd *cobra.Command, args []string) {
        a, _ := strconv.Atoi(args[0])
        b, _ := strconv.Atoi(args[1])
        fmt.Println(a + b)
    },
}
func init() {
    rootCmd.AddCommand(addCmd)
}
```
```
$ go run main.go add 2 3
5
```

## flag
flag は global と local があり、subcommnad に引き継ぐ設定もある  
https://github.com/spf13/cobra#working-with-flags
