arvdude で error が出て キーボードが文鎮になる (2018/09/12)
===

https://github.com/qmk/qmk_firmware/issues/3657


```
 ~> make iris/rev2:nekottyo:avrdude
(snip)
avrdude: ERROR: address 0x800201 out of range at line 1079 of ./.build/iris_rev2_nekottyo.hex
```

というエラーでキーボードに firmware が焼けず使えなくなる。

# 解決策

MacOS かつ brew を使っている時

avr-gcc が 8.2.0 のとき

```
 ~> avr-gcc --version
avr-gcc (GCC) 8.2.0
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

local に 8.2 以下が残っているか確認する

```
 ~> brew info avr-gcc
osx-cross/avr/avr-gcc: stable 8.2.0, HEAD
GNU compiler collection for AVR 8-bit and 32-bit Microcontrollers
https://www.gnu.org/software/gcc/gcc.html
/usr/local/Cellar/avr-gcc/7.3.0 (1,682 files, 174.9MB)
  Built from source on 2018-05-21 at 11:17:16
/usr/local/Cellar/avr-gcc/8.1.0 (1,720 files, 200.1MB)
  Built from source on 2018-07-03 at 17:22:56
/usr/local/Cellar/avr-gcc/8.2.0 (1,720 files, 200.4MB) *
  Built from source on 2018-08-20 at 18:58:13
From: https://github.com/osx-cross/homebrew-avr/blob/master/avr-gcc.rb
```

8.1.0 を使う

```
 ~> brew switch avr-gcc 8.2.0
```

すると firmware を焼けるようになる
