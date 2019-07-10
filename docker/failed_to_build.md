カーネルを上げたら docker build で失敗する
===

https://github.com/moby/moby/issues/39475

> After upgrading to linux 4.19.56 and docker 18.09.7, I'm seeing intermittent container creation failures due to problems with overlayfs. This appears to be code that was recently added to Linux 5.2, and that has been subsequently backported to releases on the stable train. The patch for my release is here:


```
➜ uname -srv  
Linux 5.1.15-1-MANJARO #1 SMP PREEMPT Wed Jun 26 23:48:19 UTC 2019

➜ docker version      
Client:
 Version:           18.09.7-ce
 API version:       1.39
 Go version:        go1.12.6
 Git commit:        2d0083d657
 Built:             Tue Jul  2 01:00:04 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server:
 Engine:
  Version:          18.09.7-ce
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.12.6
  Git commit:       2d0083d657
  Built:            Tue Jul  2 00:59:35 2019
  OS/Arch:          linux/amd64
  Experimental:     true
```
