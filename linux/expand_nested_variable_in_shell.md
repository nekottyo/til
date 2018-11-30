shell でネストされた変数を展開したい
=======

# 問題

例えばRuby の bundle install 時に、 development なら `bundle install --without development` で、
production なら `bundle install --without development:test` みたいなことをやりたい。

```bash
$ config_staging=hoge
$ config_production=fuga
$ target=staging
```

のとき、 `config_${target}` の中身の `hoge` を呼び出したい

# 解決

eval を使う

```bash
$ echo "$(eval echo \${config_${target}})"
hoge
# or
$ config=\$config_${target}
$ echo $config
$config_staging

$ echo "$(eval echo $config)"
hoge
```
