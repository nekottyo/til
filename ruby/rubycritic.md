RubyCritic
====

reek, flay, flog を合わせてかっこいい UI を被せたもの。codeclimate を手元実行できるイメージ

https://github.com/whitesmith/rubycritic


# 使い方

Gemfile:
```ruby
gem "rubycritic", require: false
```

```
$ bundle
```

rake の task を作る

Rakefile:
```ruby
RubyCritic::RakeTask.new do |task|
  task.paths   = FileList['lib/**/*.rb']
  # default では browser を直接開くため、CI で実行したい場合はコメントインする
  #task.options = '--no-browser'
  task.verbose = true
end
```

rubycritic を叩く

```bash
$ bundle exec rake rubycritic
```

default では `${PROJECT_ROOT}/tmp/rubycritic/` に html 一式が吐かれる
