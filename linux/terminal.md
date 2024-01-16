terminal emulator 選び
=====


# terminator

https://github.com/albfan/terminator

![img](https://raw.githubusercontent.com/EliverLara/terminator-themes/af6ec7a1a72963d301dfd0e71b338d8cb1e2e473/images/Broadcast.png)

めちゃめちゃに使いやすい。copy on select が focus 外さないでも使える


```conf
  [[default]]
    background_color = "#2b2b2b"
    background_darkness = 0.92
    background_type = transparent
    copy_on_selection = True
    cursor_color = "#ffffff"
    font = Cica 10
    foreground_color = "#e6e1dc"
    icon_bell = False
    palette = "#000000:#da4939:#519f50:#ffd24a:#6d9cbe:#d0d0ff:#6e9cbe:#ffffff:#555753:#ff7b6b:#83d182:#ffff7c:#9fcef0:#ffffff:#a0cef0:#ffffff"
    scrollbar_position = hidden
    show_titlebar = False
    use_system_font = False
```

# alacritty

https://github.com/jwilm/alacritty

日本語表示に難あり。2 バイト文字以外なら使いやすい。
GPU Accelareted。

フォントサイズ Up/Down のキーバインドが、config ファイルを共有する場合 Mac/Linux でうまくいかない（Mac は Command だが、 Linux は Ctrl)
</br>
ssh 経由だと表示が崩れる。ダブルクリックでの copy on select が不発するときが多い。

# kitty

https://sw.kovidgoyal.net/kitty/

alacritty 同様に GPU Accelareted。あまりつかってない。日本語環境なら alacritty よりもよさそう

