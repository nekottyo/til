gorilla.vim
=====

https://gorillavim.connpass.com/event/121394/

https://twitter.com/search?f=tweets&q=%23gorillavim&src=typd&lang=ja

2019/03/11 @Quipper Ltd


# 速度.vim @chaspy	

- Motion
- 変更
    - Modes
        - Normal, Insert, Visual
    - Operator
        - d,y : operator は2回叩くと行志向になる
    - 変更と繰り返し
        - .
        - u
        - C-R

# ターミナルアプリケーションとしてのVimの使い方 @LightTiger2505

- 他のコマンドから連携が行える
    - `crontab -e` とか `git commit` 
    - sql で `\e` やると vim 立ち上がる
    - `git rebase` とか間違って起動したとき `:cq` で error code を返してくれる
    - `$EDITOR` で EDITOR を読んでくれるコマンドは使ってくれる
    - tig の `difftool` としても使える

# 社内Vim活	@mopp

https://docs.google.com/presentation/d/1uNouaZOLsYDwtaFBGhWdX4u1V-9uWWWqX7U2gl_Ou3k/edit#slide=id.p

- 社内 vim slack があるけど活発じゃない...
    - 実践Vim 読書会
        - 1H 会議室
            - 目次を眺めて、読む場所決めて、時間まで読みながら雑談
    - `'`,  で戻れる
    - `C-E` で保管中断
    - スライドにいっぱいおすすめある

# Vimで任意のコード書きます	@ujihisa

- `Vim script`
- Vim の本気を見た

# vscode-vimについて @mtsmfm

https://speakerdeck.com/mtsmfm/vscodevim-is-also-vim

- vscode-vim めっちゃ vim
    - デフォで vim-surround 入ってるし、マクロも使える
- vscode の terminal でも vim 開ける

# mastering vim という本について @okuramasafumi

- ユースケース別(機能別ではない)
- ぷらぎん豊富
- `:arg` であとから引数を足せる

# テキストプロパティ触ってみた @thinca

- Vim 8.1 で追加される機能
