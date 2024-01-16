lvremove しようとしたら `used by another device` で消せない
===

vm 削除で `virsh undefine` した後に発生


## 原因

```
# dmsetup info -c
Name        Maj Min Stat Open Targ Event  UUID
VG1--hogep2 253   9 L--w    0    1      0 part2-LVM-AAAAAAAAAAAAAAAAAAAAAaaaaaaaaaaaaaaaaa
VG1--hoge   253   7 L--w    2    1      0 LVM-AAAAAAAAAAAAAAAAAAAAAaaaaaaaaaaaaaaaaa
VG1--hogep1 253   8 L--w    0    1      0 part1-LVM-AAAAAAAAAAAAAAAAAAAAAaaaaaaaaaaaaaaaaa
```

hoge が hogep1, p2 から参照されていて Open が 2 なので busy


## 削除方法

part から消して参照をなくす

```
# dmsetup remote /dev/mapper/VG1-hogep2
# dmsetup remote /dev/mapper/VG1-hogep1
# dmsetup remote /dev/mapper/VG1-hoge
```


その後 lvremove
```
# lvremove /dev/VG1/hoge
  Logical volume "hoge" successfully removed
```
