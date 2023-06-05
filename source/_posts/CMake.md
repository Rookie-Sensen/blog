---
title：CMake
date: 2023-06-05 12:00:00
tags: CMake
---

## 啥是CMake

简单来说就是用于自动生成Makefile的工具。

脚本文件名是`cmakelists.txt`，用cmake命令生成Makefile文件。

写cmakelists比写Makefile简单，所以cmake其实就是帮助程序员写Makefile的工具。

## 安装CMake(CentOs环境下)

执行代码：

```bash
yum -y install cmake
```

-y参数是指，在安装和卸载过程中，系统都会询问你是否确定“Is that ok?"，-y参数就是自动帮你确定，不再询问你。