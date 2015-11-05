---
title: "Babun 安装配置"
date: 2015-10-25 20:15:15
---
众所周知，Windows是一个不太适合开发人员使用的操作系统，因为缺乏一个好用的命令行工具。而且不得不承认，诸如 Python 之类常用的开发环境在Windows下用着实在是糟心。Windows下最出名的类Linux环境当然非[Cygwin](https://www.cygwin.com/)莫属了，号称 *Get that Linux felling on Windows*，然而 Cygwin 的使用体验无比糟糕，安装软件的步骤也极其繁琐。而 [Babun](http://babun.github.io/) 无疑是一个更好的选择，它基于Cygwin，却又更胜于Cygwin。

Babun 集成了 ```cygwin``` 与 ```oh-my-zsh```，自带包管理工具 ```pact```（基于```apt-cyg```），并预装了一系列软件。

### 安装 ###
解压缩到任意文件夹后，运行 ```install.bat```（需管理员权限）

### 配置 ###

#### 验证
```babun check```, 判断环境是否正确
```babun update```, 检查是否有新的更新包

#### 默认根目录
```%userprofile%\.babun\cygwin```

#### 包管理
Babun 自带叫做 ```pact``` 的包管理工具（类似 apt-get 或 yum）， 可使用```pact install```，```pact remove```，```pact update```等管理软件包。

#### 常用开发配置
Babun自带了Python，但没有安装pip， 手动安装：
```wget https://bootstrap.pypa.io/get-pip.py -O - | python```

Python开发的一些依赖库：
```
pact install python-setuptools
pact install libxml2-devel libxslt-devel libyaml-devel
curl -skS https://bootstrap.pypa.io/get-pip.py | python
pip install virtualenv
curl -skS https://raw.githubusercontent.com/mitsuhiko/pipsi/master/get-pipsi.py | python
```

常用软件安装：
```
pact install tmux        #安装tmux
pact install screen      #安装screen 有了这两个不用conEmu也可以了  
pact install zip         #安装zip
pact install lftp        #lftp命令
pact install p7zip       # p7zip命令
pact install connect-proxy # 基于openssh的socks https代理
pact install util-linux    #安装linux基础命令行工具 more/col/whereis等等命令
pact install bind-utils    #安装dig命令
pact install inetutils     #安装Telnet等常用网络命令
pact install python        #python环境
pact install python-crypto #python 环境
```
