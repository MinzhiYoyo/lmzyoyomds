---
title: Ubuntu配置分布式饥荒服务器
id: 54
date: 2024-12-03 17:09:53
auther: yutanbird
cover: 
excerpt: 科雷推荐使用Ubuntu，当然Centos也是可以的
permalink: /archives/ubuntu%E9%85%8D%E7%BD%AE%E5%88%86%E5%B8%83%E5%BC%8F%E9%A5%A5%E8%8D%92%E6%9C%8D%E5%8A%A1%E5%99%A8
categories:
 - comb
tags: 
 - 饥荒
---



# 安装`SteamCMD`

## 增加用户`Steam`

&emsp;`ps`：我喜欢使用单独的用户进行管理，因此我新建了一个用户

``` shell
# 假设你现在是root用户
adduser steam # 增加用户
adduser steam sudo # 加入管理员组
# 自行设置密码

su steam
cd ~
mkdir -p ~/steam/
cd ~/steam
```

## 安装`steamcmd`

```shell
sudo apt-get install libstdc++6:i386 libgcc1:i386 libcurl4-gnutls-dev:i386
# 报错解决方法：
sudo dpkg --add-architecture i386
sudo apt-get update

# 开始下载Steamcmd
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz # 下载
tar -xvzf steamcmd_linux.tar.gz # 解压
./steamcmd.sh # 运行脚本会自动安装，注意自己网络的问题

Steam>  # 意味着安装完成
```

# 安装`DST Server`

```shell
Steam>login anonymous # 匿名登录就行，不需要登陆自己的steam
# 登录成功后
Steam>app_update 343050 validate # 安装Dst server

Ctrl+C 退出

# 更改文件夹名字，因为Dont Starve ...的文件夹名字含有空格以及引号
cd ~/Steam/steamapp/common/
mv ./Don't Starve together ./DSTS # 更改成DSTS
```

# 配置文件

## 配置访问许可文件

- token：在饥荒联机版**主页**中，点击左下角**账号**，弹出的窗口加载完毕，点击上方导航栏的**游戏**，点击进入**《饥荒·联机版》游戏服务器**，滑到最**群名**，点击**添加新服务器**，并且记住token。

- 用户ID：再点击刚刚导航栏中的用户信息，复制**Klei用户ID**。

## 生成饥荒服务器配置

在自己笔记本上新建一个世界，然后生成，但不需要进入。然后回到饥荒主页面，点击左下角的**数据**，找到你生成的世界`Cluster_`最大的那个。我这里认为是`Cluster_1`

单独将这个`Cluster_1`文件夹整个复制出来，在`Cluster_1`新建四个文件：

```
- Cluster_1
	|-- Caves
	|	|-- backup
	|	|-- save
	|	|-- leveldataoverride.lua
	|	|-- modoverrides.lua
	|	|-- server.ini 
	|
	|-- Master
	|	|-- backup
	|	|-- save
	|	|-- leveldataoverride.lua
	|	|-- modoverrides.lua
	|	|-- server.ini
	|
	|-- whitelist.txt：内容为空
	|-- blocklist.txt: 为空
	|-- adminlist.txt：为上面的用户ID
	|-- cluster_token.txt: 内容为：上面的token
	|-- cluster.ini
```

# 服务器配置

## Master服务器

将上面那个Cluster拷贝到服务器上的`~/.klei/DontStarveTogether/`

删除Caves文件夹

### 修改文件`Cluster_1/cluster.ini`

```shell
[GAMEPLAY]
game_mode = survival
max_players = 6
pvp = false
pause_when_empty = true


[NETWORK]
lan_only_cluster = false
cluster_password = 房间的密码
cluster_description = 房间的描述
cluster_name = 房间的名字
offline_cluster = false
cluster_language = zh
cluster_cloud_id = id


[MISC]
console_enabled = true


[SHARD]
shard_enabled = true
bind_ip = 0.0.0.0 # 这里修改
master_ip = 127.0.0.1
master_port = 10995
cluster_key = 密码token # 这里修改
```

### 修改`Cluster_1/Master/server.ini`

``` shell
# 并没有修改
[NETWORK]
server_port = 10999

[SHARD]
is_master = true
id=1 # id设置为1

[ACCOUNT]
encode_user_path = true
```

## Caves服务器配置

将上面那个Cluster拷贝到服务器上的`~/.klei/DontStarveTogether/`

删除Caves文件夹

### 修改文件`Cluster_1/cluster.ini`

```shell
[GAMEPLAY]
game_mode = survival
max_players = 6
pvp = false
pause_when_empty = true


[NETWORK]
lan_only_cluster = false
cluster_password = 房间的密码
cluster_description = 房间的描述
cluster_name = 房间的名字
offline_cluster = false
cluster_language = zh
cluster_cloud_id = id


[MISC]
console_enabled = true


[SHARD]
shard_enabled = true
bind_ip = 127.0.0.1
master_ip = xx.xx.xx.xx # 你的Master主机的IP
master_port = 10995
cluster_key = # 跟master的一样
```

### 修改`Cluster_1/Master/server.ini`

``` shell
# 并没有修改
[NETWORK]
server_port = 10998


[SHARD]
is_master = false
name = Caves
id = 2130207453


[ACCOUNT]
encode_user_path = true


[STEAM]
master_server_port = 27017
authentication_port = 8767
```

## 两个服务器共同修改

### 打开防火墙

***注意：***所有用到的端口，都需要打开，有些是`udp`， 有些是`tcp`，建议所有端口的 `tcp udp`都打开

### 配置mods

找到你`mods`文件夹，默认应该在`~/Steam/steamapps/common/DSTS/mods`，除此之外

修改文件：`~/Steam/steamapps/common/DSTS/mods/dedicated_server_mods_setup.lua`

```lua
--There are two functions that will install mods, ServerModSetup and ServerModCollectionSetup. Put the calls to the functions in this file and they will be executed on boot.

--ServerModSetup takes a string of a specific mod's Workshop id. It will download and install the mod to your mod directory on boot.
	--The Workshop id can be found at the end of the url to the mod's Workshop page.
	--Example: http://steamcommunity.com/sharedfiles/filedetails/?id=350811795
	--ServerModSetup("350811795")

--ServerModCollectionSetup takes a string of a specific mod's Workshop id. It will download all the mods in the collection and install them to the mod directory on boot.
	--The Workshop id can be found at the end of the url to the collection's Workshop page.
	--Example: http://steamcommunity.com/sharedfiles/filedetails/?id=379114180
	--ServerModCollectionSetup("379114180")

-- 上面是默认的
-- 下面是我修改的
-- 这些模组数字怎么来？
-- Cluster_1/Master/modoverrides.lua里面所有的id，一个个复制出来的
-- 理论上 Master与Caves的模组是相同的，除非自行设置过。
ServerModSetup("356930882")
ServerModSetup("362175979")
ServerModSetup("2078243581")
ServerModSetup("1207269058")
ServerModSetup("378160973")
ServerModSetup("666155465")
ServerModSetup("1185229307")

```

### 缺失的`mods`

&emsp;在你的个人电脑的路径：`./steamapps/workshop/content/322330`下，是你的饥荒联机版的mod，复制到服务器上。如果服务器上没有`workshop`的文件夹，那就直接办`workshop`文件夹拷贝就行

&emsp;在你的个人电脑的路径：`./steamapps/common/Don't Starve Together/mods`下所有的文件夹拷贝到服务器上对应位置

# 启动

## 启动命令

```shell
# 在Master服务器上
cd ~/Steam/steamapps/common/DSTS/bin
./dontstarve_dedicated_server_nullrenderer console_enable -shard Master # 启动Master

# 在Caves服务器上
cd ~/Steam/steamapps/common/DSTS/bin
./dontstarve_dedicated_server_nullrenderer console_enable -shard Caves # 启动Caves
```

## 注意

```shell
# 不能直接
/home/steam/Steam/steamapps/common/DSTS/bin/dontstarve_dedicated_server_nullrenderer console_enable -shard Master
/home/steam/Steam/steamapps/common/DSTS/bin/dontstarve_dedicated_server_nullrenderer console_enable -shard Caves

# 绝对路径会报错，所以没有设置守护进程，也没法设置服务，也许有其他方法，欢迎与我联系！！！
```

