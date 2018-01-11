# `md5sum -c file`

`md5sum -c file` で md5sum 出力形式の file を食わせると、md5sum が一致するかを検証してくれる

大量のファイルを一度に検証したいときに便利

```
$ cat test1
hogee
$ md5sum test1
1742e0f1c900f8a63cd55c10349a2fbd  test1
$ md5sum -c <(md5sum test1)
test1: OK

$ echo fugaa > test1
$ md5sum -c test1.md5
test1: FAILED
md5sum: WARNING: 1 computed checksum did NOT match
```


