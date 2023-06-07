---
title: TinyWebserver的运行所遇到的问题
date: 2023-06-07 17:39:00
tags: c++, mysql, Tinywebserver
---

## vscode报错“无法打开源文件mysql/mysql.h

### 发生时间点

安装好mysql，以及将Tinywebserver项目导入到linux系统里面去时。此时如果强行执行：

```bash	
sh ./build.sh
```

编译器也会报错，说其找不到mysql.h。

### 解决办法

#### 安装头文件

在此项目的Github上，作者给出了回答：

> 解决方案：缺少libmysqlclient-dev
>
> ubantu: sudo apt-get install libmysqlclient-dev
>
> centos: yum install mysql-devel

但是在我执行yum install mysql-devel时，却报错：

[![error_mysql](https://s1.ax1x.com/2023/06/07/pCF6Yid.png)](https://imgse.com/i/pCF6Yid)

我是根据[此视频](https://www.bilibili.com/video/BV1et4y177E5/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=fb96729252c30c70b2c0ae9cf30fe1a3)安装的mysql，根据[这篇博客](https://blog.csdn.net/u013255206/article/details/73650950?ops_request_misc=&request_id=&biz_id=102&utm_term=无法打开源文件mysql/mysql.h&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-73650950.142^v88^insert_down38v5,239^v2^insert_chatgpt&spm=1018.2226.3001.4187)的说法，是没有安装

mysql-community-devel.x86_64这个文件，因为这个文件就是提供头文件的功能。

找到了问题所在，在bash界面输入：

```bash
rpm -ivh mysql-community-devel-8.0.28-1.el7.x86_64.rpm 
```

结果又报错：

[![rpm报错](https://s1.ax1x.com/2023/06/07/pCF6WQ0.png)](https://imgse.com/i/pCF6WQ0)

不过这个问题就好解决了，记然缺少依赖就安装对应依赖。

如何安装依赖呢？执行：

```bash
yum install openssl-devel -y
```

安装成功后，再执行上述rpm代码，发现已经成功。

但是此时vscode还是报错，编译也不会通过。因为还需要最后一步配置。

#### 更改路径

根据Github上面的提问以及各位大佬的回答，我是按如下方法解决这个问题的。

首先确定我的mysqlclient.so文件再/usr/lib64/mysql目录下，此时添加环境变量：

```bash
export LIBRARY_PATH=/usr/lib/mysql
```

随后，更改makefile：

```makefile
CXX ?= g++

DEBUG ?= 1
ifeq ($(DEBUG), 1)
    CXXFLAGS += -g
else
    CXXFLAGS += -O2

endif

server: main.cpp  ./timer/lst_timer.cpp ./http/http_conn.cpp ./log/log.cpp ./CGImysql/sql_connection_pool.cpp  webserver.cpp config.cpp
	$(CXX) -o server  $^ $(CXXFLAGS) -lpthread -L /usr/lib64/mysql/ -lmysqlclient ##此行变更

clean:
	rm  -r server
```

更改完成就发现可以编译通过了，vscode也不报错了。

*ps:卡了一下午，心累。*
