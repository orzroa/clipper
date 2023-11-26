[（转载）OpenWrt下把SD卡挂载到 /overlay ，扩大软件空间_openwrt overlay docker挂载点-CSDN博客](https://blog.csdn.net/jacob210/article/details/105443098) 

在wall内外搜索无数文章，唯有这篇文章能够看得懂并奏效，感谢作者。

原文地址：[https://blog.samnya.cn/mount-sd-card-to-overlay-on-openwrt/](https://blog.samnya.cn/mount-sd-card-to-overlay-on-openwrt/)  作者：Sam喵

原料：Newifi D1（Newifi 2）一台

这个机子自带了一个 Micro SD 插槽，刷了 OpenWrt 之后一直没怎么用到。闲着来折腾一下把 Micro SD 卡挂载到 /overlay 分区，增加内部可用的空间。

参照 openwet 官方 wiki 上的 exroot 教程，我们要做以下的步骤：

首先要使用 Micro SD 卡槽，需要安装以下两个内核模块：

```
opkg install kmod-sdhci kmod-sdhci-mt7620
```

接下来在 /dev 下应该可以看到有 mmcblk0 的文件了，那就是我们的 Micro SD。

然后再安装一些文件系统相关的软件包。

```
opkg install block-mount kmod-fs-ext4 e2fsprogs fdisk
```

这个时候输入

```
block info
```

应该可以看到你的 SD 卡信息。

这里我们把 SD 卡格式化成 ext4 格式。

```
mkfs.ext4 /dev/mmcblk0p1
```

 接下来，转移现有的文件到 SD 卡上。不知道 OpenWrt 中 / 目录和 /overlay 目录的意义的可以看后面。

```
mount /dev/mmcblk0p1 /mnt ; tar -C /overlay -cvf - . | tar -C /mnt -xf - ; umount /mnt
```

稍等一会，文件就复制完成了。

接下来要创建 mmcblk0p1 的挂载配置，全自动可以使用以下命令

```
block detect > /etc/config/fstab;
sed -i s/option$'\t'enabled$'\t'\'0\'/option$'\t'enabled$'\t'\'1\'/ /etc/config/fstab;
sed -i s#/mnt/mmcblk0p1#/overlay# /etc/config/fstab;
cat /etc/config/fstab;
```

这样子就可以完成挂载点的设置。

于是现在来实际把 mmcblk0p1 挂载到 /overlay 上

```
mount /dev/mmcblk0p1 /overlay
```

查看一下挂载后的效果

```
root@OpenWrt:/dev# df
Filesystem           1K-blocks      Used Available Use% Mounted on
/dev/root                 2560      2560         0 100% /rom
tmpfs                   126944      1308    125636   1% /tmp
/dev/mtdblock6         3756448     31236   3514680   1% /overlay
overlayfs:/overlay       28224      7976     20248  28% /
tmpfs                      512         0       512   0% /dev
/dev/mmcblk0p1         3756448     31236   3514680   1% /overlay
```

现在可以看到，/overlay 的空间已经增加了。

这时候就可以重启你的路由器了，看看是否成功自动挂载。

[![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/edd66322-6981-4cae-b84e-f7505176fa67.png)
](https://blog.samnya.cn/wp-content/uploads/2018/11/QQ%E6%88%AA%E5%9B%BE20181117012658.png)

看，这时候想安装什么软件都可以了。

#### 附录

/overlay 是什么意思呢？

OpenWRT 一般使用的文件系统是 SquashFS ，这个文件系统的特点就是：只读。

那，一个只读的文件系统，是怎么做到保存设置和安装软件的呢？

这里就是使用一个 /overlay 的分区，overlay顾名思义就是覆盖在上面一层的意思。

虽然原来的文件不能修改，但我们把修改的部分放在 overlay 分区上，然后映射到原来的位置，读取的时候就可以读到我们修改过的文件了。

但为什么要用这么复杂的方法呢？ OpenWRT 当然也可以使用 EXT4 文件系统，但使用 SquashFS + overlay 的方式有一定的优点。

首先 SquashFS 是经过压缩的，在路由器这种小型 ROM 的设备可以放下更多的东西。

然后 OpenWRT 的恢复出厂设置也要依赖于这个方式。在你捅 Reset 重置的时候，它只需要把 overlay 分区清空就可以了，一切都回到了刚刷进去的样子。

如果是 EXT4 文件系统，就只能够备份每个修改的文件，在恢复出厂设置的时候复制回来，十分复杂。

当然，SquashFS + overlay 也有它的缺点，修改文件的时候会占用更多的空间。

首先你不能够删除文件，因为删除文件实际上是在 overlay 分区中写入一个删除的标识，反而占用更多的空间。

另外在修改文件的时候相当于增加了一份文件的副本，占用了双份的空间。
