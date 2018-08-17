`presence` method が神
=====

`presence` は `present?` が `true` なら self を返し、そうでない時は `nil` を返す。


その変数があったら使うしなかったら定数を代入したいみたいな時に便利。

```ruby
fuga = hoge.present? ? hoge || 'ほげほげ'
```

これを `presence` を使うと

```ruby
fuga = hoge.presence || 'ほげほげ'
```

 とできる。
