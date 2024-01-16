# [Rails セキュリティガイド](https://railsguides.jp/security.html)

## セッション

- セッションには巨大なオブジェクトを格納せず、その id だけを格納する
- セッションに重要な情報を格納しない
- `ActionDispatch::Session::CookieStore` はセッションハッシュをクライアント cookie に保存し、その cookie からハッシュを読み出す
- https://qiita.com/kawasaki/items/dcaf5716c3fd5e2fe69f
    ```ruby
    production:
        secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
    ```
- セッション固定攻撃対策に、ログイン成功したらセッション作り直す
    - ユーザ管理に [Devise](https://github.com/plataformatec/devise) 使う
- サーバ側でセッション切れを管理しといたほうが安全
- セッションテーブルに作成日も入れといて期限を過ぎたセッションは消す
    ```ruby
    delete_all "updated_at < '#{time.ago.to_s(:db)}' OR
        created_at < '#{2.days.ago.to_s(:db)}'"
    ```

## CSRF 対策
- `protect_from_forgery with: :exception` (default で入る）でトークン入れる

## リダイレクト, ファイル
- リダイレクトはパラメータから取らない。ユーザから与えられないようにする
- Ruby や DB などは低い権限で実行する
- ファイル名はホワイトリスト or ブラックリストにする
    ```
    def sanitize_filename(filename)
      filename.strip.tap do |name|
        # メモ: File.basenameは、Unix上でのWindowsパスに対しては正常に動作しません
        # フルパスではなくファイル名のみを取得
        name.sub! /\A.*(\\|\/)/, ''
        # 最終的に非英数文字をアンダースコアまたは
        # ピリオドとアンダースコアに置き換え
        name.gsub! /[^\w\.\-]/, '_'
      end
    end
    ```
- アップロードと DB への保存は非同期にする
- ダウンロードもファイル名が期待されている path に置かれているかチェックする
    - あるいはファイルを id 管理にする [attachment_ru](https://github.com/technoweenie/attachment_fu)

## イントラネットとAdminのセキュリティ

- イントラ用画面の方が強い権限を持っているため表画面よりもリスクが高いことを認識する
    - 権限を複数管理者で分散する
    - 管理画面様に特別なログイン情報を設置する
    - 一般ユーザの User model に管理者フラグは危ない
    - IP 縛る
    - サブドメインで分ける

## ユーザ管理
- 自前で管理せず Devise や Authlogic などを使う
- ログイン画面では失敗理由を表示しない
- パスワード忘れでも失敗理由を表示しない
- 複数回失敗したら CAPCHA 義務付ける
- パスワード変更は（おそらくログイン済みの時）古いパスワードを入力させる
- メールアドレス変更もパスワードを入力させる
- つまり重要な変更を加える前にパスワードを変更させる！
- パスワードはログに出さない
    - filter_parameters を設定する
    ```
    config.filter_parameters << :password
    ```
- 正規表現は `^` `$` は行単位で一致するので `\A` `\z` を使う
- ユーザの入力値はアクセス権なども含め確認する
- ホワイトリストとブラックリストは、まずはホワイトリストの適用を考える
    - ブラックリストに引っかかったらこねくり回さずに拒否する
- SQL で外部文字列はサニタイズする、プレースホルダーを使う
- ユーザ入力はホワイトリストで弾く
    ```
    tags = %w(a acronym b strong i em li ul ol h1 h2 h3 h4 h5 h6 blockquote br cite sub sup ins p)
    s = sanitize(user_input, tags: tags, attributes: %w(href title))
    ```
- 言わずもがなコマンドラインの引数にするときも気をつける

