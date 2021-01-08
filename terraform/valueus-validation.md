terraform module の values に validation を入れる
===

# 背景
terraform 0.13 から values の validation ができる。
https://www.terraform.io/docs/configuration/variables.html#custom-validation-rules

module に与えられた入力値を conditon に従ってチェックしてくれる。
例には単一 string や list length で validation をかけていたが、list の中身の validation をしたかった。

# list の validation
issue に書いてあった
https://github.com/hashicorp/terraform/issues/26205#issuecomment-692935473

```hcl
  condition = can([for s in var.private_subnets : regex("^([0-9]{1,3}\\.){3}[0-9]{1,3}(\\/([0-9]|[1-2][0-9]|3[0-2]))?$", s)]
```


# optional な list の validation

```hcl
    condition     = length(var.user-account-arns) == 0 ? true : can([for a in var.user-account-arns : regex("^arn:aws:iam::\\d{12}:root$", a)])
```

length と ternary operaor で lenght が 0 のときは true を返して、あとは `can` で `regex` してあげるとよい。

# その他
飛び道具として、 local で try ~ raise の方法もあるっぽい
https://github.com/hashicorp/terraform/issues/24223#issuecomment-594966990
```hcl
locals {
  my_int    = "asdf"
  my_calc_a = try(local.my_int * 5, raise("Can't perform calc A. Is the int really an int?")
  my_calc_c = try(local.my_calc_a * 5, raise("Can't perform calc C. Is the int really an int?")
}
```
t
