['hoge', 'fuga'].map(&:upcase) #=> ['HOGE', 'HUGA'] にどうしてなるのか
=====

from http://gihyo.jp/book/2017/978-4-7741-9397-7


proc を渡す時は & をつける


& は to_proc を呼び出して 戻り値の proc を引数として与える動きをする


```ruby
>> upcase_proc = Proc.new { | word| word.upcase } #=> #<Proc:0x0000563beecf9f60@(irb):5>
>> ['hoge', 'fuga'].map(&upcase_proc) #=> ["HOGE", "FUGA"]
```

symbol は proc 変換できる

```ruby
>> upcase_proc = :upcase.to_proc #=> #<Proc:0x0000563bef107c10(&:upcase)>
>> upcase_proc.class #=> Proc
```

symbol から作った proc は 第1引数はレシーバ, 第2引数以降は symbol のメソッドの 第1, 第2... 引数となる

```ruby
>> upcase_proc = :upcase.to_proc #=> #<Proc:0x0000563bef107c10(&:upcase)>
>> upcase_proc.call('hoge') #=> "HOGE"
```

`['hoge', 'fuga'].map(&:upcase)` は

- :upcase という symbol を to_proc し proc オブジェクトにする
- map の引数として proc オブジェクトがブロックで渡される
- map によりproc の第1引数として各要素を受け取る
- 第1引数が レシーバとなり upcase が呼び出され戻り値となる
- map は proc の戻り値を新しい配列に入れる

ということをしている


