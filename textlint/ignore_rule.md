textlint-prh が textlint-fliter-rule-allowlist で無視できないとき
===

## 背景
[textlint-prh](https://github.com/textlint-rule/textlint-rule-prh) を使っていて、[WEB+DB_PRESS.yml](https://github.com/prh/rules/blob/711e00793d9d69eeda04d68a796b6d9afb6d5748/media/WEB%2BDB_PRESS.yml) 起因で fix される rule のうち、直してほしくないやつを無視したい。

例えば
`master merge` という単語がある場合、
https://github.com/prh/rules/blob/master/media/WEB+DB_PRESS.yml#L2236-L2239
```
  - expected: $1マスタ$2
    pattern:  /(?:([^ム])\bMaster)|(?:Master\b([^.]))/
  - expected: $1マスタ$2
    pattern:  /(?:([^ム])\bmaster)|(?:master\b([^.]))/
```
以下のルールによって、`マスタ merge` となってしまう。

大体は [textlint-filter-rule-allowlist](https://github.com/textlint/textlint-filter-rule-allowlist) で回避可能なのだが、 rule 評価のタイミング（ちゃんと調べてないから分からん）起因なのか、
`.textlintrc` の `filters.allowlist.allow: ["/master/"]` のように書いても rule を無視できない事がある。

## prh.yaml による ignore
https://dev.classmethod.jp/articles/eetann-ignore-textlint-rule-prh/

ここに全てが書いているが、`.textlintrc` に書くのではなく、 yaml で import path を書いて rulePaths に読み込ませる方式にすると、 `ignoreRules` で expected の ignore が書ける。

```yaml
version: 1
imports:
  - path: ./node_modules/prh/prh-rules/media/techbooster.yml
  - path: ./node_modules/prh/prh-rules/media/WEB+DB_PRESS.yml
    ignoreRules:
      - expected: $1マスタ$2
```
