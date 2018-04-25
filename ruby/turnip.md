Turnip
===

# はじめに
turnip を調べると食べ物の名前がいっぱい出てくるので整理

- Cucumber(きゅうり)
  - BDDテストフレームワーク
  - https://github.com/cucumber/cucumber
- Gherkin(きゅうりのピクルス)
  - cucumber 等で使う言語
  - https://github.com/codegram/gherkin-ruby
- Turnip(カブ)
  - Cucumber 派生で、Cucumber のイケてないところを強くしたやつ
  - Graken 使う
  - 日本語で書ける
  - https://github.com/jnicklas/turnip
- Spinach(ほうれん草)
  - Gherkin で動く BDD フレームワーク
  - Cucmber と比較して、Step のメンテナンス性や再利用性を重視している
  - 日本語で書けない
  - GitLab で使ってる
  - https://github.com/codegram/spinach

Ruby の テスト界隈食べ物好きすぎなのでは...

## BDD: Behavior Driven Development

TDD(テスト駆動開発)から派生。振る舞い(仕様)や制約条件を記述する。自然言語に近い形で記述。  
利用者目線でシナリオを書いてシステムに対してテストを行う。


# 使ってみる

前に Rails 入門としてやった [小学生でもわかるRuby on Rails入門](https://github.com/nekottyo/ruby-on-rails-grade-schrool) を turnip でテストする。

test framework は RSpec にしたが、流れ的には minitest っぽい

Gemfile に足す

```
gem 'turnip'
gem 'rspec-rails'
gem 'database_rewinder'
```
install
```
bundle install
bundle exec rails generate rspec:install
```
.rspec に turnip 向け設定を入れる
```
-r turnip/rspec
```


feature を書く
```
cat spec/features/tweet.feature
Feature: ユーザは Tweet を投稿できる

   @tweet
   Scenario: タイトルとコンテキストを入力しツイートできること
       When ユーザが new にアクセスする
       And タイトルとコンテキストを入力してツイートボタンを押す
       Then 内容が表示されていること
```
叩く 
```
ruby-on-rails-grade-school> bundle exec rspec
*

Pending: (Failures listed here are expected and do not affect your suite's status)

 1) ユーザは Tweet を投稿できる タイトルとコンテキストを入力しツイートできること When ユーザが new にアクセスする -> And タイトルとコンテキストを入力してツイートボタンを押す -> Then 内容が表示されていること
    # No such step: 'ユーザが new にアクセスする'
    # ./spec/features/tweet.feature:5


Finished in 0.00167 seconds (files took 0.07154 seconds to load)
1 example, 0 failures, 1 pending
```


# 参考
- http://ukstudio.jp/posts/2011/07/02/bdd/
- https://qiita.com/shunhikita/items/d02cc313496e26bb69e1
