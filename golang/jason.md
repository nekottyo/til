# [jason](https://github.com/antonholmquist/jason)

> Easy-to-use JSON Library for Go
軽量json パーサ

## jason parse


`GetObject()` で path を辿れる。型が違うとエラー

```go
package main

import (
    "fmt"
    "strings"

    "github.com/antonholmquist/jason"
)

func main() {
    rawData := `{
  "groups": {
    "test": {
      "members": [
        "hoge01",
        "hoge02",
        "hoge03"
      ],
      "room": [
        "room1"
      ],
      "msg": "test message"
    }
  }
}`

    v, _ := jason.NewObjectFromBytes([]byte(rawData))

    test, _ := v.GetObject("groups", "test")
    members, _ := test.GetStringArray("members")
    msg, _ := test.GetString("msg")
    fmt.Printf("members: [%s], msg: %s", strings.Join(members, ","), msg)
}
```

result

```
> go run main.go
members: [hoge01,hoge02,hoge03], msg: test message
```
