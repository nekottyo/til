# ansible の file module は atomic に置き換えができるか

## TL;DR
ansible の file module での file 操作は atomic な置き換えである :tada:

## 経緯
symlink を `ln -sf` すると `rm` + `ln` になるので神のタイミング（rm から ln するまでの間）で path を参照されると見えなくなる場合がある  
そのため safe な置き換えのためには、隣に new を作成し `mv -T new old` で atomic に置き換える必要がある  
ansible の file module の `state: symlink` は `ln -sf new old` をしているのか new を作って `mv -T` 相当をしているのか不明だったので調査  

## file module
もはやコメントに書いてあるが
[file.py#L410-L411](https://github.com/ansible/ansible/blob/stable-2.4/lib/ansible/modules/files/file.py#L410-L411)

```
            if prev_state != 'absent':
                # try to replace atomically
                b_tmppath = to_bytes(os.path.sep).join(
                    [os.path.dirname(b_path), to_bytes(".%s.%s.tmp" % (os.getpid(), time.time()))]
                )
```
dest path に `.${PID}.${EPOCH_TIME}.tmp` で置いた後

[file.py#L423](https://github.com/ansible/ansible/blob/stable-2.4/lib/ansible/modules/files/file.py#L423)

```
                    os.rename(b_tmppath, b_path)
```

[os.rename](https://docs.python.jp/2.7/library/os.html#os.rename) で置き換えているため atomic に置き換えることができる

mv であっても file system をまたぐと atomic な置き換えは出来ない
