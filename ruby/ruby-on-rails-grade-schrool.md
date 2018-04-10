小学生でもわかるRuby on Rails入門
=====

https://openbook4.me/projects/92

- Rails では HTML 部分を `ビュー (View)` と呼ぶ
- ビューは HTML 表示するだけ
- 値を設定してビューに渡すのを `コントローラー (Controller)` と呼ぶ

# init

```
rails new mitakalab-twitter
```

めっちゃできる

```
mitakalab-twitter> tree -L 1
.
├── app
├── bin
├── config
├── config.ru
├── db
├── Gemfile
├── Gemfile.lock
├── lib
├── log
├── package.jsonia
├── public
├── Rakefile
├── README.md
├── test
├── tmp
└── vendor
```

```
mitakalab-twitter> tree -L 1 app
app
├── assets
├── channels
├── controllers // コントローラ
├── helpers
├── jobs
├── mailers
├── models
└── views       // ビュー
```

## ビューとコントローラー作る

`rails g` の g は generate

http://tech-dig.hatenablog.com/entry/2016/09/19/083623

```
mitakalab-twitter> rails g controller users index show
Running via Spring preloader in process 13281
     create  app/controllers/users_controller.rb
      route  get 'users/show'
      route  get 'users/index'
     invoke  erb
     create    app/views/users
     create    app/views/users/index.html.erb
     create    app/views/users/show.html.erb
     invoke  test_unit
     create    test/controllers/users_controller_test.rb
     invoke  helper
     create    app/helpers/users_helper.rb
     invoke    test_unit
     invoke  assets
     invoke    coffee
     create      app/assets/javascripts/users.coffee
     invoke    scss
     create      app/assets/stylesheets/users.scss
```

>注 indexやshowは自分で自由に名前をつけることが可能ですが、あとで説明するようにindex、showというようにつけるのがよしとされています。 また、コントローラー名はusersというように複数形にするのがよしとされています。 こういう暗黙の約束事のようなものをRailsの規約と言います。 Railsはその名の通りレールに沿って作ればカンタンに作れる、という意味なので、出来る限りRailsが敷いたレールの上を走りましょう。



`rails s` で上げる

server の s

```
http://127.0.0.1:3000/users/index
http://127.0.0.1:3000/users/show
```

にアクセスすると `index` と `show` が見れる。レールに乗ってしまった。


# ビューとコントローラー

app/views/users/show.html.erb

```erb
<h1><%= @user[:name] %></h1>
<p><%= @user[:username] %></p>
<ul>
    <li>Location : <%= @user[:location] %></li>
    <li>About    : <%= @user[:about] %></li>
</ul>
```

`@user` 作ってないので

![error](https://raw.github.com/mitakalab/wiki/master/screenshots/rails/rails-04.png)

## controller 作る

```ruby
class UsersController < ApplicationController
  def index
  end

  def show
    @user = Hash.new
    @user[:name] = 'Nekottyo'
    @user[:username] = 'nekottyo'
    @user[:location] = 'Tokyo, Japan'
    @user[:about] = 'Nice to meet you'
  end
end
```

twitter っぽくしたいとき `http://hoge.com/users/show/nekottyo`  みたいに名前 が URI になるのはどうすれば?

# ルーティング

`http://hoge.com/users/show/nekottyo` がしたい！ -> ルーティング

- Rails での アクセスした URL を元に振り分けるもの
>ユーザーがURLにアクセス ⇒ ルーティングが仕分け ⇒ コントローラーが値を入れる ⇒ ビューがコントローラーから渡された値を表示


router を変えてあげる

config/routes.rb

```diff
Rails.application.routes.draw do
  get 'users/index'

- get 'users/show'
+ get 'users/show/:username' => "users#show"  
end
```

http://127.0.0.1:3000/user/show/nekottyo

で名前ベースで 表示できるようになる


# DB

- DBの構造 -> スキーマ
- マイグレーションファイル -> スキーマ定義

> 注 migrationという名の通り、移住をしやすくするためのようなイメージです。 移住するときに家を立てなければなりませんが、家の設計図さえあれば、いくらでも同じ家を立てることができます。 その家の設計図がmigration fileです。しかし、このmigration fileだけでは、住めません。 家をたてるために、その設計図から家を実際に建てなければなりません。それがmigrateです。

## DB create

`db:create`

```
ruby-on-rails-grade-school> rails db:create
Created database 'db/development.sqlite3'
Created database 'db/test.sqlite3'
```

model generate

```
ruby-on-rails-grade-school> rails g model user name:string username:string location:string about:text

Running via Spring preloader in process 30236
     invoke  active_record
     create    db/migrate/20180409110638_create_users.rb
     create    app/models/user.rb
     invoke    test_unit
     create      test/models/user_test.rb
     create      test/fixtures/users.yml
```
>注 モデルはusersではなく、userという単数形であることに注意しましょう。 これもRailsの敷いたレールの一つです。


マイグレーションファイルができる
db/migrate/20180409110638_create_users.rb

```ruby
class CreateUsers < ActiveRecord::Migration[5.1]
 def change
   create_table :users do |t|
     t.string :name
     t.string :username
     t.string :location
     t.text :about

     t.timestamps
   end
 end
end
```

`rails db:migrate` で db 作る
```
ruby-on-rails-grade-school> rake db:migrate
== 20180409110638 CreateUsers: migrating ======================================
-- create_table(:users)
  -> 0.0007s
== 20180409110638 CreateUsers: migrated (0.0008s) =============================
```

## seed で種作る

`db/sheeds.rb`
```ruby
ruby-on-rails-grade-school> cat db/seeds.rb
@user = User.new
@user.name = 'Nekottyo'
@user.username = 'nekottyo'
@user.location = 'Tokyo, Japan'
@user.about = 'Hello, I am nekottyo. I am from database!'
@user.save

@user = User.new
@user.name = 'Shohei Aoki'
@user.username = 'moyahima'
@user.location = 'Tottori, Japan'
@user.about = 'Nice to meet you. I am from database!'
@user.save
```

## DB から引っ張る

`app/controllers/users_controller.rb`
```ruby
class UsersController < ApplicationController
  def show
    @user = User.find_by(:username => params[:username])
  end
end
```

# twitter つくる

## controller, model 作成

```
rails generate controller tweets index show new
rails g model tweet title:string context:text
```

## form

今の new

![new](https://raw.github.com/mitakalab/wiki/master/screenshots/rails/rails-10.png)

view の form を入れる(フォームヘルパー)

`app/views/tweets/new.html.erb`

```erb
<%= form_for Tweet.new do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
  <%= f.label :context %>
  <%= f.text_area :context %>
  <%= f.submit %>
<% end %>
```
```
undefined method `tweets_path' for #<#<Class:0x00007f1e7c6ea9c8>:0x00007f1e7c3db1b0>
```

form の投げ先がわからん ので route に足す

```diff
Rails.application.routes.draw do
  get 'tweets/index'

  get 'tweets/show'

  get 'tweets/new'

+  post 'tweets' => 'tweets#create'

  get 'users/index'

  get 'users/show/:username' => "users#show"
end
```

すると form が表示されて入力すると

![](https://raw.github.com/mitakalab/wiki/master/screenshots/rails/rails-13.png)

`#create` がないから error になる

![](https://raw.github.com/mitakalab/wiki/master/screenshots/rails/rails-14.png)

データは `params` の hash で飛んでくるので `params` で受け取る

`app/controllers/tweets_controller.rb`

```ruby
def create
  @tweet = Tweet.new
  @tweet.title = params[:tweet][:title]
  @tweet.context = params[:tweet][:context]
  @tweet.save
end
```

create の view がないのでエラーになる

最後に index に redirect してあげる

`app/view/tweets_controller.rb`

```ruby
def create
  @tweet = Tweet.new
  @tweet.title = params[:tweet][:title]
  @tweet.context = params[:tweet][:context]
  @tweet.save
  redirect_to '/tweets/index'
end
```

`tweets/new` で post すると `tweets/index` に redirect される

# tweet 一覧表示

`app/controllers/tweets_controller.rb`
```ruby
def index
  @tweets = Tweet.all
end
```

`Tweet.all` で model 全体取得

`app/views/tweets/index.html.erb`

```erb
<% @tweets.each do | tweet | %>
    <h1><%= tweet.title %></h1>
    <p><%= tweet.context %></p>
<% end %>
```

`@tweets.each` で要素を loop して表示する

![tweet](https://raw.githubusercontent.com/mitakalab/wiki/master/screenshots/rails/rails-16.png)

## 補足

ルーティングは一般的に

```ruby
resources :tweets
```

と書き、 いい感じにルーティングを生成してくれる

```ruby
get 'tweets' => 'tweets#index'
get 'tweets/:id' => 'tweets#show'
get 'tweets/new' => 'tweets#new'
get 'tweets/:id/edit' => 'tweets#edit'
post 'tweets' => 'tweets#create'
put 'tweets' => 'tweets#update'
delete 'tweets' => 'tweets#destroy'
```
