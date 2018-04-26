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
  - 日本語DSLで書ける
  - https://github.com/jnicklas/turnip
- Spinach(ほうれん草)
  - Gherkin で動く BDD フレームワーク
  - Cucmber と比較して、Step のメンテナンス性や再利用性を重視している
  - 日本語DSLで書けない
  - GitLab で使ってる
  - https://github.com/codegram/spinach
- Capibara
  - UI テストのためのフレームワーク
  - form に値を入れたりボタン押したりできる
  - Driver が必要(headlesschrome とか)
  - https://github.com/teamcapybara/capybara


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
gem 'selenium-webdriver'
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

ここではユーザがやることを自然言語的に記述する

```ruby
Feature: ユーザは Tweet を投稿できる

    @tweet
    Scenario: タイトルとコンテキストを入力しツイートできること
        When ユーザが new にアクセスする
        And タイトルとコンテキストを入力してツイートボタンを押す
        Then タイトルが表示されていること
        And コンテキストが表示されていること

```

`rspec` を叩いてみる

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

step がないので pending になる。

step を書く。  
`visit` や `fill_in` などは capybara で使えるもの。

```ruby
steps_for :tweet do
 step 'ユーザが new にアクセスする' do
   visit '/tweets/new'
 end

 step 'タイトルとコンテキストを入力してツイートボタンを押す' do
   fill_in 'tweet[title]', with: 'turnip test'
   fill_in 'tweet[context]', with: 'my first turnip!'
   click_button 'Create Tweet'
 end

 step 'タイトルが表示されていること' do
   expect(page).to have_content 'turnip test'
 end

 step 'コンテキストが表示されていること' do
   expect(page).to have_content 'my first turnip!'
 end
end
```

Capybara の setup

spec/support/capybara.rb
```ruby
require 'capybara/rspec'
require 'selenium-webdriver'

Capybara.register_driver :selenium do |app|
  Capybara::Selenium::Driver.new(app,
                                 browser: :chrome,
                                 desired_capabilities: Selenium::WebDriver::Remote::Capabilities.chrome(
                                   chrome_options: {
                                     args: %w[window-size=1680,1050]
                                   }
                                 ))
end

Capybara.javascript_driver = :selenium
```


実行する

```
ruby-on-rails-grade-school> bundle exec rspec

ユーザは Tweet を投稿できる
 タイトルとコンテキストを入力しツイートできること
   When ユーザが new にアクセスする -> And タイトルとコンテキストを入力してツイートボタンを押す -> Then タイトルが表示されていること -> And コンテキストが表示されていること

Finished in 0.19353 seconds (files took 1.09 seconds to load)
1 example, 0 failures
```

:tada: :tada: :tada:



# 参考
- http://ukstudio.jp/posts/2011/07/02/bdd/
- https://qiita.com/shunhikita/items/d02cc313496e26bb69e1
- https://github.com/willnet/capybara-readme-ja
