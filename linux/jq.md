jq をそこそこつかえるようにしたい　
---

# 検索して否定したい

```bash
❯ echo '[{"foo": "hoge"},{"foo": "hogehoge"}]' | jq '.[] | select(.foo | contains("hogehoge") | not)'
{
  "foo": "hoge"
}
```

# nest された key があったりなかったりするとき

ないキーを参照しようとすると死ぬ

```bash
❯ echo '[{"key1": "val", "key2": [{"foo": "val"}]},{"key1": "val"}]' | jq '.[].key2[].foo'
"val"
jq: error (at <stdin>:1): Cannot iterate over null (null)
```

`[]?` で ruby で言うところの `&.` ができる
```bash
❯ echo '[{"key1": "val", "key2": [{"foo": "val"}]},{"key1": "val"}]' | jq '.[].key2[]?.foo'
"val"

```

# if したい

```bash
❯ echo '[{"key1": "val", "key2": [{"foo": "val"}]},{"key1": "val", "key2": [{"foo": "var"}]}]' | jq '.[] | if .key2[]?.foo == "var" then . else empty end'
{
  "key1": "val",
  "key2": [
    {
      "foo": "var"
    }
  ]
}

```
