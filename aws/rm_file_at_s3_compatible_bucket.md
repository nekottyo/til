s3 互換 bucket でデータを消す
=====

セットアップ

```bash
$ pip install awscli
$ aws configure
AWS Access Key ID [None]: example id
AWS Secret Access Key [None]: example secret
Default region name [None]:
Default output format [None]:
```

対象を絞り込む

```bash
$ aws --endpoint-url http://bucket.example.com s3 ls --summarize --recursive --human-readable  s3://hogehoge
(snip)
```

消す

```bash

$ aws --endpoint-url http://bucket.example.com s3 ls --summarize --recursive --human-readable  s3://hogehoge | \
    | xargs -n1 -I{} aws --endpoint-url http://bucket.example.com s3 rm s3://hogehoge/{}
```
