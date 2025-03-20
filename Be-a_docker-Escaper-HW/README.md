
## Writeup
预期思路是， 当选手登陆到容器中后， 会发现这是一个拥有 `sys_admin`权限的

```
$ capsh --decode=00000000a82425fb
0x00000000a82425fb=cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_sys_admin,cap_mknod,cap_audit_write,cap_setfcap
```



通常 `sys_admin` 权限我们会通过 mount `cgroup` 之类的方法来逃逸容器，但是在该题目环境下，会发现并没有权限进行 mount ， 因此我们需要去寻找其他的思路。



通过搜索引擎搜索关键词 `container escaper 、 sys_admin`， 我们可以查找到一些相关的资料 ^[2] ^[3]

因此，我们发现我们可以通过 ebpf 劫持 cron 来逃逸容器。 我们搜索 `ebpf container` escaper 可以获取一些现有的利用工具，或者自己编写一个利用工具。例如下面的链接进行直接使用或者相关魔改都能获取 flag

参考的exploit ^[4] ^[5]  

## Reference Link
[1]: ./deploy	"题目部署脚本"  
[2]: https://www.usenix.org/system/files/sec23_slides_he.pdf  
[3]: https://hackmd.io/@ebpf/ry428EZGo  
[4]: https://github.com/bfengj/eBPFeXPLOIT/blob/main/README.zh-cn.md
[5]: https://github.com/TomAPU/bpfcronescape

