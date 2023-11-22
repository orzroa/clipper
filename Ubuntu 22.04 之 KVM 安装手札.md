[系统运维|Ubuntu 22.04 之 KVM 安装手札](https://linux.cn/article-14661-1.html) 

作者： [James Kiarie](https://www.linuxtechi.com/how-to-install-kvm-on-ubuntu-22-04/) 译者： [LCTT](https://linux.cn/lctt/) [SamMa](https://linux.cn/lctt/turbokernel)

| 2022-06-01 17:16   评论: [](https://linux.cn/portal.php?mod=comment&id=14661&idtype=aid "查看全部评论")    

**KVM** 是 基于内核的虚拟机Kernel-based Virtual Machine 的首字母缩写，这是一项集成在内核中的开源虚拟化技术。它是一种类型一（裸机）的管理程序hypervisor，可以使内核能够作为一个裸机管理程序bare-metal hypervisor。

在 KVM 之上可以运行 Windows 和 Liunx 虚拟机。每个虚拟机都独立于其它虚拟机和底层操作系统（宿主机系统），并拥有自己的 CPU、内存、网络接口、存储设备等计算资源。

本文将介绍在 Ubuntu 22.04 LTS（Jammy Jellyfish）中如何安装 KVM 。在文末，我们也将演示如何在安装 KVM 完成之后创建一台虚拟机。

# 1、更新 Ubuntu 22.04

在一切开始前，打开终端并通过如下命令更新本地的软件包索引：

```
$ sudo apt update
```

# 2、检查虚拟化是否开启

在进一步行动之前，首先需要检查你的 CPU 是否支持 KVM 虚拟化，确保你系统中有 VT-x（ vmx）英特尔处理器或 AMD-V（svm）处理器。

你可以通过运行如下命令，如果输出值大于 0，那么虚拟化被启用。否则，虚拟化被禁用，你需要启用它：

```
$ egrep -c '(vmx|svm)'  /proc/cpuinfo
```

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/4a58f9ce-f610-44ba-9cda-2f0c91774d5a.png)
_SVM-VMX-Flags-Cpuinfo-linux_

根据上方命令输出，你可以推断出虚拟化功能已经启用，因为输出结果大于 0。如果虚拟化功能没有启用，请确保在系统的 BIOS 设置中启用虚拟化功能。

另外，你可以通过如下命令判断 KVM 虚拟化是否已经在运行：

```
$ kvm-ok
```

运行该命令之前，请确保你已经安装了 `cpu-checker` 软件包，否则将提示未找到该命令的报错。

直接就在下面，你会得到如何解决这个问题的指示，那就是安装 `cpu-checker` 包。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/2b0e5e03-b143-4c8a-8e06-5e4ef652ec0f.png)
_KVM-OK-Command-Not-Found-Ubuntu_

随后，通过如下命令安装 `cpu-checker` 软件包：

```
$ sudo apt install -y cpu-checker
```

接着再运行 `kvm-ok` 命令，如果 KVM 已经启动，你将看到如下输出：

```
$ kvm-ok
```

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/78dda8af-331a-4477-bdb6-4bd5a8d1fa9d.png)
_KVM-OK-Command-Output_

# 3、在 Ubuntu 22.04 上安装 KVM

随后，通过如下命令在 Ubuntu 22.04 中安装 KVM 以及其他相关虚拟化软件包：

```
$ sudo apt install -y qemu-kvm virt-manager libvirt-daemon-system virtinst libvirt-clients bridge-utils
```

以下为你解释刚刚安装了哪些软件包：

*   `qemu-kvm` – 一个提供硬件仿真的开源仿真器和虚拟化包
*   `virt-manager` – 一款通过 libvirt 守护进程，基于 QT 的图形界面的虚拟机管理工具
*   `libvirt-daemon-system` – 为运行 libvirt 进程提供必要配置文件的工具
*   `virtinst` – 一套为置备和修改虚拟机提供的命令行工具
*   `libvirt-clients` – 一组客户端的库和API，用于从命令行管理和控制虚拟机和管理程序
*   `bridge-utils` – 一套用于创建和管理桥接设备的工具

# 4、启用虚拟化守护进程（libvirtd）

在所有软件包安装完毕之后，通过如下命令启用并启动 libvirt 守护进程：

```
$ sudo  systemctl enable --now libvirtd
$ sudo  systemctl start libvirtd
```

你可以通过如下命令验证该虚拟化守护进程是否已经运行：

```
$ sudo  systemctl status libvirtd
```

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/87433102-74e9-4561-a93c-3523d4fa3c1e.png)
_Libvirtd-Status-Ubuntu-Linux_

另外，请将当前登录用户加入 `kvm` 和 `libvirt` 用户组，以便能够创建和管理虚拟机。

```
$ sudo  usermod  -aG kvm $USER
$ sudo  usermod  -aG libvirt $USER
```

`$USER` 环境变量引用的即为当前登录的用户名。你需要重新登录才能使得配置生效。

# 5、创建网桥（br0）

如果你打算从本机（Ubuntu 22.04）之外访问 KVM 虚拟机，你必须将虚拟机的网卡映射至网桥。`virbr0` 网桥是 KVM 安装完成后自动创建的，仅做测试用途。

你可以通过如下内容在 `/etc/netplan` 目录下创建文件 `01-netcfg.yaml` 来新建网桥：

```
$ sudo vi /etc/netplan/01-netcfg.yaml
network:
  ethernets:
    enp0s3:
      dhcp4: false
      dhcp6: false
  # add configuration for bridge interface
  bridges:
    br0:
      interfaces: [enp0s3]
      dhcp4: false
      addresses: [192.168.1.162/24]
      macaddress: 08:00:27:4b:1d:45
      routes:
        - to: default
          via: 192.168.1.1
          metric: 100
      nameservers:
        addresses: [4.2.2.2]
      parameters:
        stp: false
      dhcp6: false
  version: 2
```

保存并退出文件。

注：上述文件的配置是我环境中的，请根据你实际环境替换 IP 地址、网口名称以及 MAC 地址。

你可以通过运行 `netplan apply` 命令应用上述变更。

```
$ sudo netplan apply
```

你可以通过如下 `ip` 命令，验证网桥 `br0`：

```
$ ip add show
```

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/c33a176f-39e5-4169-8bf9-93ffb432a751.png)
_Network-Bridge-br0-ubuntu-linux_

# 6、启动 KVM 虚拟机管理器

当 KVM 安装完成后，你可以使用图形管理工具 `virt-manager` 创建虚拟机。你可以在 GNOME 搜索工具中搜索 `Virtual Machine Manager` 以启动。

点击搜索出来的图标即可：

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/d6319ddd-48f7-4e17-8368-a02b93e5326d.png)
_Access-Virtual-Machine-Manager-Ubuntu-Linux_

虚拟机管理器界面如下所示：

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/e13f9aaa-b5eb-4ddc-aeaf-d20940ccac25.png)
_Virtual-Machine-Manager-Interface-Ubuntu-Linux_

你可以点击 “文件File” 并选择 “新建虚拟机New Virtual Machine”。你也可以点击下图所示的图标：

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/ab208526-3d87-46fb-a8f9-825399f3e93b.png)
_New-Virtual-Machine-Icon-Virt-Manager_

在弹出的虚拟机安装向导将看到如下四个选项：

*   本地安装介质（ISO 镜像或 CDROM）
*   网络安装（HTTP、HTTPS 和 FTP）
*   导入现有磁盘镜像
*   手动安装

本文使用已下载的 ISO 镜像，你可以选择自己的 ISO 镜像，选择第一个选项，并点击 “向前Forward”。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/95016d88-81be-4a3f-950d-35f23baaf3b9.png)
_Local-Install-Media-ISO-Virt-Manager_

下一步中，点击 “浏览Browse” 选择 ISO 镜像位置。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/8febf3f0-404e-4e9c-a922-70335912c869.png)
_Browse-ISO-File-Virt-Manager-Ubuntu-Linux_

在下一个窗口中点击 “浏览本地Browse local” 选取本机中 ISO 镜像。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/9a0d0753-5d86-431f-bcd4-e7fdf124df52.png)
_Browse-Local-ISO-Virt-Manager_

如下所示，我们选择了 Debian 11 ISO 镜像，随后点击 “打开Open”。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/e12c6391-e9a7-4a37-ae7b-22e061b61d08.png)
_Choose-ISO-File-Virt-Manager_

当 ISO 镜像选择后，点击 “向前Forward” 进入下一步。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/f9a3321f-d4cb-4474-96a9-51cd7aabdb99.png)
_Forward-after-browsing-iso-file-virt-manager_

接着定义虚拟机所用内存大小以及 CPU 核心数，并点击 “向前Forward” 。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/9e2951d1-dae2-47c9-8e96-0f6bdb8bedb3.png)
_Virtual-Machine-RAM-CPU-Virt-Manager_

下一步中，输入虚拟机磁盘空间，并点击 “向前Forward” 继续。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/f87e6d1e-030d-40d3-9371-8da1d1ad4239.png)
_Storage-for-Virtual-Machine-KVM-Virt-Manager_

如你需要将虚拟机网卡连接至网桥，点击 “选择网络Network selection” 并选择 `br0` 网桥。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/d682ae71-d0a7-49e2-8ff1-2dc7073f6b92.png)
_Network-Selection-KVM-Virtual-Machine-Virt-Manager_

最后，点击 “完成Finish” 按钮结束设置虚拟机。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/ad329cff-3cb3-47ef-8610-88c97a33ee32.png)
_Choose-Finish-to-OS-Installation-KVM-VM_

稍等片刻，虚拟机的创建过程将开始。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/15b036cb-519f-416a-8dc7-13b7863272fe.png)
_Creating-Domain-Virtual-Machine-Virt-Manager_

当创建结束时，虚拟机将开机并进入系统安装界面。如下是 Debian 11 的安装选项。在这里你可以根据需要进行系统安装。

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231122/9340f13c-c779-4296-9e96-cdadf474c309.png)
_Virtual-Machine-Console-Virt-Manager_

# 小结

至此，本文向你演示了如何在 Ubuntu 22.04 上 安装 KVM 虚拟化引擎。你的反馈对我们至关重要。

* * *

via: [https://www.linuxtechi.com/how-to-install-kvm-on-ubuntu-22-04/](https://www.linuxtechi.com/how-to-install-kvm-on-ubuntu-22-04/)

作者：[James Kiarie](https://www.linuxtechi.com/author/james/) 选题：[lkxed](https://github.com/lkxed) 译者：[turbokernel](https://github.com/turbokernel) 校对：[wxy](https://github.com/wxy)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创编译，[Linux中国](https://linux.cn/article-14661-1.html) 荣誉推出
