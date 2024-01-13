---
title: WSL中配置golang环境
date: 2023-09-03 16:07:22
tags: 
  - Linux
  - golang
---

## 配置WSL

> 参考文章：[WSL安装](https://deepinout.com/wsl-tutorials/wsl-install-and-quick-start.html)

### 切换root用户

```shell
sudo passwd root
su
```

### 限制Vmmem内存

1. 按下`Windows + R`键，输入`%UserProfile%`并运行进入用户文件夹

2. 新建文件`.wslconfig`，然后记事本编辑

3. 填入以下内容并保存, memory为系统内存上限，这里我限制最大2GB，可根据自身电脑配置设置

```shell
[wsl2]
memory=4GB
swap=8GB
localhostForwarding=true
```

4. 输入`wsl --shutdown`来关闭当前的子系统，重启wsl

### 使用docker

```shell
 curl -fsSL https://test.docker.com -o test-docker.sh
 sudo sh test-docker.sh
 
 # 验证docker是否安装成功
 systemctl status docker
```

```shell
# 启动docker
systemctl start docker

# 查看当前的容器

docker ps -a

# 启动容器
docker  start 容器名或者容器id

# 停止容器
docker  stop 容器名或容器id


# 强制关闭容器
docker container kill 容器名或容器id
# 或可简写为
docker kill 容器名或容器id
```

```shell
docker ps

docker exec -it d498d9f00612 /bin/bash

select  host, user, authentication_string, plugin from mysql.user;

use mysql

update user set host = '%' where user = 'root';
alter user 'root'@'%' identified with mysql_native_password by 'shixiaocaia';

FLUSH PRIVILEGES;
```



### 配置Git

```shell
sudo apt update
sudo apt install git

git --version
git version 1.8.1.2
```

```shell
# 配置用户信息
git config --global user.name "shixiaocaia"​
git config --global user.email shixiaocaia@gmail.com​
git config --list

# 配置SSH
ssh-keygen -t rsa -C "这里换上你的邮箱"
# 不需要密码，直接三次回车
# 生成id_rsa和id_rsa.pub
# 添加公钥pub文件内容，到Settings -- SSH and GPG keys​
cat ~/.ssh/id_rsa.pub
# 测试配置成功
ssh -T git@github.com
```

### 启动目录

- 打开中终端设置，搜索wsl

```shell
{
    "guid": "{72130475-4d4b-5774-bc1a-2a09ff7f0056}",
    "hidden": false,
    "name": "Ubuntu2204",
    "source": "Windows.Terminal.Wsl",
    "startingDirectory": "//wsl$/Ubuntu2204/home/xiaocai"
}
```

- 如果是其他终端工具，设置工作目录`//wsl$/Ubuntu2204/home/xiaocai`

## 自动安装

```shell
sudo apt install golang-go
apt remove golang-go
```

用这样的办法自动安装的golang并非最新版

## 手动安装

1. 查看[google发布的golang版本](https://golang.google.cn/dl/)，选择合适的版本安装。

```shell
wget https://studygolang.com/dl/golang/go1.20.5.linux-amd64.tar.gz
```

2. 解压到`/usr/local`

```shell
tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
```

3. 设置环境变量

```shell
vim /etc/profile
```

```shell
export GOPATH=/home/xiaocai/go
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

```shell
source /etc/profile
```

4. 其他设置

```shell
# 配置代理
go env  -w GOPROXY=https://goproxy.cn,https://goproxy.io,direct

# Go Moudle
go env -w GO111MODULE=on
```

5. 查看环境是否安装成功

```shell
go env
go version
```

## golang配置

![20230907102715106](../images/go1.png)

- 添加语言运行时

- 使用`whereis go`，得到`go: /usr/local/go /usr/local/go/bin/go /mnt/d/Go/bin/go.exe`

  - 第一个参数是go sdk文件夹

  - 第二个参数`/usr/local/go/bin/go`对应Go可执行文件参数

  - 第三个是Windows10里面的go环境映射到了wsl2

![20230907102932244](../images/go2.png)

- 运行/调试配置更改

## 常见问题

### 127.0.0.1

[官方文档说明](https://learn.microsoft.com/zh-cn/windows/wsl/networking)

- wsl下的`127.0.0.1`并不能正确访问，应该使用`localhost`进行访问，得到一种解释：`127.0.0.1`要走从网络层全链路走一圈的，`localhost`则不必。
- wsl访问windows下的服务，需要通过IP地址方式，`ip route | grep default | awk '{print $3}'`获取本机IP地址。

### wsl备份以及恢复

```shell
wsl -l -v # 确认wsl版本

# 提前创建文件夹
wsl --export Ubuntu2204 D:\wsl\ubuntu.tar # 导出Ubuntu2204 到d盘

wsl --unregister Ubuntu2204 # 注销旧版本

wsl -l -v # 确认注销

wsl --import Ubuntu2204 D:\wsl D:\wsl\ubuntu.tar # 导入备份文件

wsl -s Ubuntu2204 # 设置为默认
```

还原后的子系统进入后，默认成了 root 用户，解决方法如下，在终端启动时，配置`wsl.exe -d Ubuntu2204 -u xiaocai`。

### docker-desktop

- 默认情况下，Docker Desktop for Window会创建如下两个发行版（distro）：
  - docker-desktop：用于存放程序
  - docker-desktop-data：用于存放镜像
- docker-desktop-data部分应该按照5.2一样正常迁移

```shell
wsl --export docker-desktop-data D:\wsl\docker-data.tar

wsl --unregister docker-desktop-data

wsl --import docker-desktop-data D:\wsl\docker D:\wsl\docker-data.tar

wsl -l -v
```

### goland error obtaining VCS status: exit status 128

设置，`go env -w GOFLAGS="-buildvcs=false"`

### fatal: detected dubious ownership in repository at

- 启动用户更换成了root，导致goland打开项目时也是root，和创建文件的xiaocai不一致
- 修改/etc/wsl.conf，设置默认启动用户

```shell
[boot]
systemd=true

[user]
default = xiaocai
```

