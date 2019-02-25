zplug から zplugin への乗り換え
=====


[zplug](https://github.com/zplug/zplug) から早いと噂の [zplugin](https://github.com/zdharma/zplugin) に乗り換えた


https://github.com/nekottyo/dotfiles/pull/1



before 
```sh
➜ for i in $(seq 1 10); do time zsh -i -c exit; done

zsh -i -c exit  0.71s user 0.31s system 117% cpu 0.865 total
zsh -i -c exit  0.73s user 0.34s system 119% cpu 0.899 total
zsh -i -c exit  0.69s user 0.34s system 120% cpu 0.856 total
zsh -i -c exit  0.72s user 0.32s system 118% cpu 0.878 total
zsh -i -c exit  0.75s user 0.35s system 118% cpu 0.926 total
zsh -i -c exit  0.71s user 0.36s system 120% cpu 0.890 total
zsh -i -c exit  0.73s user 0.35s system 118% cpu 0.910 total
zsh -i -c exit  0.81s user 0.36s system 118% cpu 0.981 total
zsh -i -c exit  0.77s user 0.34s system 118% cpu 0.935 total
zsh -i -c exit  0.74s user 0.37s system 118% cpu 0.939 total
```

after
```sh
➜ for i in $(seq 1 10); do time zsh -i -c exit; done

zsh -i -c exit  0.37s user 0.12s system 126% cpu 0.389 total
zsh -i -c exit  0.37s user 0.12s system 121% cpu 0.400 total
zsh -i -c exit  0.34s user 0.13s system 113% cpu 0.415 total
zsh -i -c exit  0.34s user 0.12s system 118% cpu 0.386 total
zsh -i -c exit  0.35s user 0.11s system 127% cpu 0.361 total
zsh -i -c exit  0.33s user 0.12s system 115% cpu 0.388 total
zsh -i -c exit  0.32s user 0.12s system 124% cpu 0.354 total
zsh -i -c exit  0.34s user 0.12s system 124% cpu 0.369 total
zsh -i -c exit  0.33s user 0.13s system 125% cpu 0.363 total
zsh -i -c exit  0.34s user 0.12s system 112% cpu 0.403 total
```

なかなかいい感じ
