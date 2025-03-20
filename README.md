# Be-a-Docker-Escaper
The container escape challenge of Be A RWCTFer competition (https://be-a-rwctfer.realworldctf.com/)

## 2022 Be-a-Docker-Escaper
使用-v 将docker daemon socket映射到了容器中, 因此我们看可以通过 socket 控制容器外的docker, 直接使用-v映射flag或者启动特权容器逃逸即可

## 2023 Be-a-Docker-Escaper-2
通过 binfmt 来逃逸容器

## 2023 Be-a-Docker-Escaper-3
通过内核漏洞来逃逸容器

## 2024 Be-a-Docker-Escaper-4
容器共享了 pid namespace 情景下的容器逃逸

## 2024 Be-a-Docker-Escaper-HW
拥有 `sys_admin` 权限但是没有 `mount` 权限的容器逃逸