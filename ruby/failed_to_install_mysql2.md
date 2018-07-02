mysql2 gem が install出来ない
===

```
./result.h:24:3: error: unknown type name ‘my_bool’
   my_bool *is_null;
   ^~~~~~~
```

`my_bool` がないと怒られる

OS 側の `libmysqlclient` が `mysql 8.x` のため発生

https://github.com/brianmario/mysql2/issues/838


```
$ yarout install libmysqlclient57
```
