data.aws_iam_policy_document を merge して 1つの policy に食わせたい
===

## 背景
```hcl
data aws_iam_policy_document policy_1 {}
data aws_iam_policy_document policy_2 {}
data aws_iam_policy_document policy_3 {}
```
で
```hcl
resource aws_iam_policy_document a {
  role = hoge
  policy = [
    data.aws_iam_policy_document.policy_1.json,
    data.aws_iam_policy_document.policy_2.json
  ]
}
```
みたいなことをやりたい。
ユースケースとしては、1 の policy json を再利用して複数の policy に紐付けたい時にほしい。


しかし上記の書き方は policy は string (json)なのでうまくいかない。
また、`data.aws_iam_policy_document` には `source_json` や `override_json` もあるが各 policy に食わせるように新たに作り直さなきゃいけないのでめんどい。

## 失敗
```hcl
locals {
  policies = [
    "policy_1",
    "policy_2"
  ]
}

resource aws_iam_policy_document a {
  for_each = toset(local.policies)
  policy = data.aws_iam_policy_document.${each.key}.json
```
変数を each で render するやり方はうまくいかない
`data.aws_iam_policy_document[each.key].json` も無理だし
`lookup(data.aws_iam_policy_document, each.key).json` も無理

## 答え
https://stackoverflow.com/questions/62658247/is-there-a-way-to-append-iam-bucket-policy-statement-to-existing-statements-usin?answertab=votes#tab-top

map を作って concat でくっつけて `jsonencode` で食わせる

```hcl
locals {
    policy_1 = jsondecode(data.aws_iam_policy_document.policy_1.json)
    policy_2 = jsondecode(data.aws_iam_policy_document.policy_2.json)
    merged_policy = {
      Version: local.policy_1.Version,
      Statement: concat(
        local.policy_1.Statement,
        local.policy_2.Statement
    ),
  }
}

resource aws_iam_policy_docuemnt a {
  role = "hoge"
  policy = jsonencode(local.merged_policy)
```
