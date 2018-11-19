shellscript の中で stderr だけ使いたい
=====

さらに、stderr の内容はそのまま stdout に吐きつつ journald にも記録させたいとき


```bash
#!/bin/bash

exec 3>&1 4>&2 1>/dev/null 2> >(tee >(systemd-cat -t "${0}") >&2)

# some scripts

exec 1>&3 2>&4
```


`exec 3>&1 4>&2 1>/dev/null 2> >(tee >(systemd-cat -t "${0}") >&2)`

- `3>&1` : stdout を fd 3 に複製
- `4>&2` : stderr を fd 4 に複製
- `1>/dev/null` : /dev/null を stdout にする
- `2> >(tee >(systemd-cat -t "${0}")>&2)`
    - `2> >(commands)` : stderr を commands でプロセス置換する
    - `tee >(systemd-cat -t "${0}" >&2)` : tee の出力を systemd-cat でプロセス置換する。 stderr を stdout に複製する

`exec 1>&3 2>&4`

- `1>&3` : fd 3 を stdout に複製
- `2>&4` : fd 4 を stderr に複製

