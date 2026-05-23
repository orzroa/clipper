**摘 要** 小米R4A使用Breed刷入固件后因为Breed适配问题可能会造成无限重启不能正常进入固件，本文通过修改固件引导地址解决上述问题。同时，发现从Breed中备份出来的eeprom分区重新刷入后有一定概率无法恢复无线正常功率，需要在刷入Breed前备份eeprom分区。

笔者邮箱：cqqjlxy@gmail.com，如有疑问，欢迎交流。

0 刷入前准备
-------

### 0.1 推荐两个精简版固件

[https://firmware-selector.immortalwrt.org/?version=23.05.2&target=ramips%2Fmt7621&id=xiaomi\_mi-router-4a-gigabit](https://firmware-selector.immortalwrt.org/?version=23.05.2&target=ramips%2Fmt7621&id=xiaomi_mi-router-4a-gigabit)

[https://github.com/lemonno2333/shared-lede/releases/tag/v1](https://github.com/lemonno2333/shared-lede/releases/tag/v1)

（下载sysupgrade版）

### 0.2 OpenWRTInvasion

[https://github.com/acecilia/OpenWRTInvasion/releases](https://github.com/acecilia/OpenWRTInvasion/releases)  
下载最新的发行版并解压  
最新的版本好像不支持Windows了，可以在Linux虚拟机或者Mac上进行。或者可以自行查阅下老版本的使用方法

### 0.3 下载Breed

[https://breed.hackpascal.net/breed-mt7621-pbr-m1.bin](https://breed.hackpascal.net/breed-mt7621-pbr-m1.bin)

### 0.4 安装Python

请自行查阅相关教程

1 使用OpenWRTInvasion破解路由器
------------------------

### 1.1 安装Python依赖

在终端中进入OpenWRTInvasion的目录，建立虚拟环境并激活

```
python -m venv .venv
source .venv/bin/activate` 
```

安装requirements.txt

```
pip install -r requirements.txt` 
```

### 1.2 运行破解脚本

```
python remote_command_execution_vulnerability.py` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/cccb1645-ab31-42cf-8c50-f0b9b72d7212.png?raw=true)
  
输入路由器的ip地址（默认应该为192.168.31.1）和管理员密码，后续步骤默认即可，然后开始自动破解  
![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/6f927b4c-030d-48ff-8b67-8f77fbc1863e.png?raw=true)
  
破解完成后使用Telnet连接到路由器，用户名和密码默认均为root

```
telnet 192.168.31.1` 
```

2 备份引导分区和eeprom文件
-----------------
**注意：一定要备份自带的引导分区，后续如果需要刷回官方固件，需要在Breed中刷回官方的Bootloader然后使用恢复工具进行恢复。** 

```
cat /proc/mtd 
dd if=/dev/mtd1 of=/tmp/Bootloader.bin 
dd if=/dev/mtd4 of=/tmp/Factory.bin` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/2e87eb6f-76c2-4a65-8974-24b5790f5d99.png?raw=true)
  
使用ftp连接路由器，用户名和密码均为root，导出上述备份，备份位于/tmp中，引导分区为Bootloader.bin，eeprom为Factory.bin  
Mac用户可以在访达中按Command+K，然后输入路由器地址连接  
![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/5810c38e-1cc3-4839-ad01-80b8f02f088f.png?raw=true)

3 上传Breed文件至/tmp文件目录下并刷入
------------------------

可以选择ftp上传，或者使用python自带的http.server工具搭建简单的http服务器，然后在路由器终端中使用wget下载，这里笔者由于Mac自带的ftp似乎不能直接上传，所以采用第二种办法。

推荐采用第二种方法，因为后续刷入breed后也要采用同样的方法进行上传。

### 3.1 搭建简单的http服务器

新建一个文件夹，然后在终端中进入这个文件夹。将待上传的文件复制到该文件夹中，建议将Breed、固件、eeprom一起复制过来。

然后开启http服务器

```
python -m http.server` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/487e1ee2-7558-43d5-97f2-6f99e6a31d73.png?raw=true)
  
如果没有指定端口，会自动在8000端口暴露文件夹中的文件。需要查看链接路由器的网卡的ip地址，假如我电脑的地址为192.168.31.100，文件夹中Breed的二进制文件名为breed-mt7621-pbr-m1.bin，则可通过“http://192.168.31.100/breed-mt7621-pbr-m1.bin”下载该文件。

### 3.2 上传Breed至路由器

在路由器终端中：

```
cd /tmp
wget http://192.168.89.115:8000/breed-mt7621-pbr-m1.bin` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/5e0cb5ef-4f68-43c7-9e8b-20ccdd07b9ad.png?raw=true)
  
保存Breed到tmp目录

### 3.3 刷入Breed

```
mtd -r write /tmp/breed-mt7621-pbr-m1.bin Bootloader` 
```

刷入完成后路由器会自动重启。

4 连接Breed，刷入固件
--------------

等待路由器重启完后，路由器的地址应该变为了192.168.1.1，连接路由器的网卡应该变为了192.168.1.2。  
破解路由器和刷入Breed的操作都可以连接路由器wifi进行，但是刷入breed并重启后，需要用网线连接到路由器的LAN口进行后续操作。

```
telnet 192.168.1.1` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/97b20591-6d39-4584-a7df-b0a652f9d5d8.png?raw=true)

### 4.1 上传固件

同样，如果在上述步骤中你已经搭建好了http服务器，可以将固件复制到服务器暴露的文件夹中，然后在breed中进行下载。注意电脑的ip地址发生了变化。

```
wget http://192.168.1.2:8000/immortalwrt-23.05.2-ramips-mt7621-xiaomi_mi-router-4a-gigabit-squashfs-sysupgrade.bin` 
```

![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/2a0e81e8-5365-4758-9d92-f78d3a55bc72.png?raw=true)
  
上传成功后应该如上图所示。此处需要注意的是上传后文件被保存到了0x80001000这个地址，文件大小为0x89047e（文件大小会根据你选择的固件不同而变化，请注意！）

### 4.2 擦除现有空间

为了写入固件，需要先擦出现有的数据。小米R4A千兆版V1固件的引导地址是从0x180000开始的，所以需要从0x180000开始，擦出一块比固件文件大小大的空间（一定要比固件大小大），比如上传后提示我固件的大小为0x89047e，则可以擦出0x900000大小的空间（固件的大小使用16进制表示，0-9，a-f，如果固件大小为0x900000，则可以擦除0xa00000大小的空间）

```
flash erase 0x180000 0x900000` 
```

### 4.3 写入固件

```
flash write 0x180000 0x80001000 0x89047e` 
```

### 4.4 从0x180000处启动系统

```
boot flash 0x180000` 
```

等待路由器重启，如果重启成功，应该显示蓝灯，则固件成功刷入。

如果写入失败，请检查一下固件是否是配，写入过程中内存地址或者大小是否正确，然后拔掉路由器电源，按住reset键插上电源并保持按住几秒，重新进入breed，重新使用telnet连接，然后重新上传-擦除-写入。

如果写入成功，并能通过192.168.1.1（根据刷入固件的不同可能后台会不同）进入openwrt后台，则进行后续操作。

5 修改Breed启动参数解决无限重启问题
---------------------

因为Breed并不能很好地适配该路由器，Breed默认的固件引导地址并不是0x180000，所以会导致不能正确引导进入固件从而出现无限重启的现象。所以需要修改Breed默认的固件引导地址。

### 5.1 开启环境变量功能

拔掉路由器电源，按住reset键插上电源并保持按住几秒，重新进入breed，在浏览器中输入192.168.1.1进入breed后台，点击环境变量设置，在右侧启用环境变量功能，位置选择Breed内部，其它保持默认，然后点击设置。  
![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/bb3d9905-dc30-4ce8-aca8-92cab7834ad3.png?raw=true)

### 5.2 添加环境变量

重新拔掉电源，以同样的方法重新进入Breed，然后浏览器刷新一下，点击环境变量编辑，然后添加一条，字段为autoboot.command，值为boot flash 0x180000，添加后保存，重启路由器。这样路由器每次重新启动时，Breed会从0x180000处开始读取固件。如果路由器启动成功，并成功引导进入固件，则固件刷入成功。  
![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/0da1a463-9e31-405a-8a98-b87b5981e0fe.png?raw=true)

6 恢复eeprom
----------

eeprom是厂家对天线提前设置好的参数，该分区会在写入第三方固件后丢失，导致openwt不能正确驱动天线，从而导致无线信号弱，因此需要手动恢复官方的eeprom分区。

重新进入Breed，使用telnet连接，上传提前备份的eeprom文件。

```
wget http://192.168.1.2:8000/Factory.bin` 
```

```
flash erase 0x50000 0x10000  
flash write 0x50000 0x80001000 0x10000` 
```

写入后重启路由器，5G频段wifi功率应该恢复正常，可以设置到23dBm左右（异常情况下最大值为3dBm）  
![](https://github.com/orzroa/clipper/blob/main/images/5-23-2026,%2023-02-15/129c0c8c-c488-40ae-95a4-c59673466e2a.png?raw=true)
  
如果没有成功恢复，有可能是你备份的eeprom文件出现了问题，可以使用经过我测试能够正常使用的eeprom文件（适用于小米R4A千兆版V1）  
[官方eeprom和上述用到的所有文件](https://pan.baidu.com/s/1PGxyc_ctZytbvIu4i0koxw?pwd=73f5)
