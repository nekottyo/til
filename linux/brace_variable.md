変数に brace 展開を含めたときにうまく展開されない問題
=====

# 症状
```
$ tree hoge fuga
hoge
└── aaa.txt
fuga
└── aaa.txt


$ ls {hoge,fuga}/aaa.txt
fuga/aaa.txt  hoge/aaa.txt

$ HOGE={hoge,fuga}/aaa.txt
$ echo $HOGE
{hoge,fuga}/aaa.txt

$ ls $HOGE
ls: cannot access {hoge,fuga}/aaa.txt: No such file or directory
#brace展開されない
```

# 理由

- https://www.gnu.org/software/bash/manual/bash.txt<Paste>

>3.5 Shell Expansions
>====================

>Expansion is performed on the command line after it has been split into
>'token's.  There are seven kinds of expansion performed:
>
>   * brace expansion
>   * tilde expansion
>   * parameter and variable expansion
>   * command substitution
>   * arithmetic expansion
>   * word splitting
>   * filename expansion

`$HOGE` の時点では brace ではないため進んで、 variable expansion で展開されてそのまま `ls` の引数となったため

`eval` や subshell 経由の実行をすると再度上から式評価されるため brace expansion される


# 解決策
```
$ eval ls $HOGE
fuga/aaa.txt  hoge/aaa.txt
#evalならされる

$ bash -c "ls $HOGE"
fuga/aaa.txt  hoge/aaa.txt
#subshellでもされる

$ HOGE=( {hoge,fuga}/aaa.txt )

$ echo $HOGE
hoge/aaa.txt fuga/aaa.txt
```

# 参考
- https://stackoverflow.com/questions/369145/how-to-assign-a-glob-expression-to-a-variable-in-a-bash-script
