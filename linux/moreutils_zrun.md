# zrun

色々便利なコマンドが詰まっている [more utils](https://joeyh.name/code/moreutils/) の zrun

> zrun: automatically uncompress arguments to command

logrotate で圧縮しているファイルと、当日分の圧縮していないファイルを同時に扱いたいみたいな時に非常に便利

## 使い方

```
$ zrun command [compressed or uncompressed file]
```

`command` には `cat` もいけるし `grep regexp` みたいに引数込みも取れる  
拡張子は `gz bz2 Z xz lzma lzo` に対応。ファイル名で拡張子判断をする

### ハマったところ

- 拡張子判断のため split で生成された `.gz` や `.xz` を食うと解凍出来なくて error になる
  - `split -d` を使う
  > -d     use numeric suffixes starting at 0, not alphabetic

- default で tmpdir は `File::Spec->tmpdir` を使うため `/tmp` を埋める
  - /var/tmp を指定する
