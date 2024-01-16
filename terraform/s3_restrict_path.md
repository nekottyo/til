terraform で s3 で特定の path だけアクセスを許可する policy の書き方
===

path を限定させて、 `s3:GetObject` と `s3:List*` を特定 path に許可させる
同時に、`s3:List*` は bucket に対して与え、 condition で prefix check をする。
list object を dynamic block で `for_each` で回すのと、 condition の values も list で展開するといい感じに書ける。

```terraform
resource aws_s3_bucket hoge {
  bucket = "hoge"
  acl = "private"
}

locals {
  s3_hoge_paths = [
    "a",
    "b",
    "c"
  ]
}

resource aws_iam_policy s3-hoge {
  name = "s3-hoge"
  path = "/"
  policy = data.aws_iam_policy_document.s3-hoge.json
}

data aws_iam_policy_document s3-hoge {
  dynamic statement {
    for_each = local.allow_s3_hoge_paths

    content {
      effect = "Allow"
      actions = [
        "s3:GetObject",
        "s3:List*",
      ]
      resources = [
        "arn:aws:s3:::hoge/${statement.value}/*"
      ]
    }
  }

  statement {
    effect = "Allow"
    actions = [
      "s3:List*",
    ]
    resources = [
      "arn:aws:s3:::hoge/"
    ]

    condition {
      test     = "StringLike"
      variable = "s3:prefix"
      values   = local.allow_s3_hoge_paths
    }
  }
}
```

できる polocy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "",
            "Effect": "Allow",
            "Action": [
                "s3:List*",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::hoge/a/*"
        },
        {
            "Sid": "",
            "Effect": "Allow",
            "Action": [
                "s3:List*",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::hoge/b/*"
        },
        {
            "Sid": "",
            "Effect": "Allow",
            "Action": [
                "s3:List*",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::hoge/c/*"
        },
        },
        {
            "Sid": "",
            "Effect": "Allow",
            "Action": "s3:List*",
            "Resource": "arn:aws:s3:::hoge/",
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "a",
                        "b",
                        "c"
                    ]
                }
            }
        }
    ]
}
```

## ref
- https://dev.classmethod.jp/articles/access-control-on-specific-path-on-s3bucket/
