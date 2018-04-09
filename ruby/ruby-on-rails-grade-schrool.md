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
