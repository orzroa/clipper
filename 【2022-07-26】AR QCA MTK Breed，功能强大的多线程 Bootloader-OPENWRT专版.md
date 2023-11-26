[【2022-07-26】AR/QCA/MTK Breed，功能强大的多线程 Bootloader-OPENWRT专版-恩山无线论坛](https://www.right.com.cn/forum/thread-161906-1-1.html) 

| _本帖最后由 hackpascal 于 2022-7-26 19:14 编辑_  
  
**重要提醒：  
后续 Breed 更新日志和补充说明将首先发布在**  
**[https://blog.hackpascal.net/](https://blog.hackpascal.net/)  
如有问题可以在这里面留言**  
  

* * *

最新更新内容：  
[https://blog.hackpascal.net/2022/07/2022-07-24-breed-update/](https://blog.hackpascal.net/2022/07/2022-07-24-breed-update/)  
  
**\[2022-07-24 r1416****\]**

*   重写整个 Web UI 更新框架：  
    　1\. 提供更完善的 NAND 支持：现在全部使用 NAND 的版本均支持完善的坏块管理功能，包括升级时自动跳过坏块、备份编程器固件时自动跳过坏块。同时使得升级 NAND 编程器固件的功能实用化（从这个版本开始，Breed 将只支持升级由新版本的备份编程器固件功能备份出的“可升级编程器固件”）。  
    　2\. 提供更灵活的升级文件选择：现在部分机型支持升级Bootloader、固件、ART/EEPROM以外的文件，例如单独的kernel/rootfs或者机器的出厂key。  
    　3\. 提供更多的固件备份选择：  
    　　a) 对于 NAND 机器，支持备份两种类型的编程器固件：可升级的编程器固件和Raw 数据。这两种编程器固件数据均包含 OOB 数据。其中可升级编程器固件按照分区表消除了坏块，且备份时开启了ECC；Raw 数据则是NAND中的原始数据，未开启ECC，且保留坏块数据。  
    　　b) 根据机型的不同，部分机型会提供特定分区数据的备份功能。  
    　并非全部机型都将立即使用该新 Web UI，已经使用的机型将在后面列出。其余部分将逐渐更新。
*   修复 MT7621 NAND 驱动在部分情况下读取数据出错的问题
*   HC5962/B70 专用版支持备份和升级 bdinfo
*   小米R3G支持直接升级OpenWrt固件的kernel1和rootfs0；现在OpenWrt、Padavan和原厂固件默认从kernel1启动；PandoraBox固件默认从kernel0启动；支持备份和更新Bdata分区；环境变量和原厂共用同一分区。
*   DW33D 专用版支持升级和启动 NAND 版本的 OpenWrt 固件
*   新增极路由4Pro HC5961 专用版，默认使用 512MB 内存时序
*   新增 ZTE E8820S 专用版，支持极路由4 HC5962/B70 固件；支持 MTK SDK 分区的固件；支持启动原厂固件以及升级原厂编程器固件。
*   使用新 Web UI的机型的版本号升级至 1.2  
    

  
**说明：**   
\- 小米R3G 如果要直接升级 OpenWrt 的 kernel1和rootfs0，需要将闪存布局选择为 "小米 R3G OpenWrt"；如果要升级 Bdata，需要将闪存布局选择为 "小米 R3G 原厂"。  
\- DW33D 需要在 "固件启动设置" 页面选择从 NAND 启动 OpenWrt 还是从 SPI-NOR 启动原厂固件。从 OpenWrt 固件切换回原厂固件时，需要同时回复一次出厂设置，以免原厂固件挂载 jffs2 出错。  
\- 所有使用 NAND 的专用版，都只支持升级 OpenWrt 的 -factory.bin，不支持 TAR 类型的 -sysupgrade.bin。  
\- 因作者工作原因，精力有限，因此剩余的型号将逐步更新至 1.2 版本。此外如果 TP-LINK 的专用版因文件体积超过限制，将停止更新。  
\- E8820 支持的 MTK SDK 固件分区表为：512k(u-boot),512k(u-boot-env),256k(factory),4096k(kernel),-(ubi)  
  
**拒绝伸手党**  
**想要某设备的专用 Breed，就得提供对应的设备，否则一切免谈。** **做不做**对我来说完全无所谓。  
  
这是楼主从去年年中自行设计开发的一个全新的 Bootloader，并用于取代 U-Boot。  
此 Bootloader 暂取名为 Breed，**不是 U-Boot，也不是 U-Boot 的改进版**，是全新、独立的、跟 U-Boot 平级的 Bootloader。  
  
科普一下：  
Bootloader 意思为引导加载器，即为用于加载操作系统的程序。它是一大类此类功能程序的统称。现在的 BIOS、UEFI、GRUB、RedBoot、U-Boot、CFE、Breed 等都是 Bootloader。  
因此，还是上面那句话，Breed 不是由什么东西改名出来的，这就是一个新的东西。看着有些人的话我真的觉得很搞笑。  
此外，由上面两句话，如果想从 Breed 刷到其他任何 Bootloader，例如 U-Boot，请在 Breed 固件更新页面选择更新 Bootloader。。。。。。。。。。。。  
  
**免费、无限制、不开源**  
  
**特别提醒：“不死”指的是所有固件更新操作均在 Breed 里面完成。因为有些官方升级固件自带 Bootloader，如果从官方固件的 Web 进行升级，那么会导致 Breed 被覆盖。Breed 在刷入固件时会自动去掉固件自带的 Bootloader，因此能够保证 Breed 本身是“不死”的。**   
  
**Breed 不支持启动非 Linux 类型的固件，例如 TP/水星/迅捷的 VxWorks 系统。因此如果固件大小小于等于 2MB，那就肯定不能刷了。**   
  
**Breed 不能智能识别【任何】固件，能支持的固件都是要写代码做判断的。又不是人看一眼就知道哪里是固件。。。**   
  
Breed 拥有以下新特性：

*   实时刷机进度，进度条能准确反映刷机进度
*   Web 页面快速响应
*   最大固件备份速度，依 Flash 而定，一般能达到 1MB/s
*   免按复位键进入 Web 刷机模式
*   Telnet 功能，免 TTL 进入 Breed 命令控制台
*   复位键定义测试功能
*   固件启动失败自动进入 Web 刷机模式
*   可自定义位置和大小的环境变量块  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/b771af72-d835-4db4-9d91-7907e97f261a.png)
  
**\[本帖内容\]**
*   2楼 - 更新日志
*   3楼 - 适用机型和 Flash 说明
*   4楼 - Breed 命令控制台说明及 TTL 刷机
*   5楼 - 复位键测试说明
*   6楼 - 环境变量说明、自定义复位键说明、小米 Mini 固件启动设置  
    

  
**\[进入 Web 刷机模式\]**  
电脑网络连接设置为自动获取 IP 地址  
打开 CMD，运行 ping 192.168.1.1 -t  
注意从 r979 开始，这个 IP 地址是可以被修改的，所以在实际操作时，需要替换为修改后的 IP  
按住复位键或者WPS键再给路由通电，如果看到路由器的部分或全部LED连闪4次，或 ping 通即表明进入 Web 刷机模式  
  
**\[免按复位键进入 Web 刷机模式\]**  
通过一个 Breed Enter 工具实现 (需要 Npcap 支持)  
[https://github.com/nmap/npcap/releases/download/v0.10-r7/npcap-0.10-r7.exe](https://github.com/nmap/npcap/releases/download/v0.10-r7/npcap-0.10-r7.exe) 下载 Npcap，安装时 WinPcap 兼容模式  
**还是支持一下 Windows XP 吧**  
点此下载 Windows XP 专用测试版 BreedEnter (使用 WDK 7.1.0 编译): [http://breed.hackpascal.net/BreedEnter-VC80-XP.zip](http://breed.hackpascal.net/BreedEnter-VC80-XP.zip)  
  
**确保路由与电脑通过网线相连**  
  
1\. 启动 BreedEnter.exe  
![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/6979d506-2f70-4e43-afcc-808ebff57e8a.png)
  
2\. 路由断电再通电  
![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/9e790525-7e23-422e-bbe6-c4488e9b1f9d.png)
  
3\. 如果程序界面提示如下即表明已进入 Web 刷机模式  
![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/7aa4fe47-c2b0-436c-ae8e-313ee7b394d6.png)


**\[修改串口波特率\]**  
*   进入 Breed 命令控制台
*   执行命令 setbrg <波特率> 即可
*   重启生效      


**\[文件说明\]**  
**不再更新的 Breed 文件已****移入** **[https://breed.hackpascal.net/EOL/](https://breed.hackpascal.net/) ，文件名后面会注明最后的修订号。**   

| **文件名** | **说明** |
| --- | --- | 
| BreedEnter.exe | Breed 启动中断工具，实现免按复位键进入 Web 刷机模式 |
| md5sum.txt | 当前版本所有 Breed 文件的 MD5 值，用于校验文件的完整性 |
| breed-mt7620-reset1.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#1 |
| breed-mt7620-reset2.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#2 |
| breed-mt7620-reset11.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#11 |
| breed-mt7620-reset12.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#12 |
| breed-mt7620-reset13.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#13 |
| breed-mt7620-reset26.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#26 |
| breed-mt7620-reset30.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#30 |
| breed-mt7620-rt-n14u.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#1，WPS 键 GPIO#2 |
| breed-mt7620-whr-1166dhp.bin | MT7620A / MT7620N 全通用，波特率 57600，复位键 GPIO#52，AOSS 键 GPIO#53 |
| breed-mt7620-lenovo-y1.bin | 联想 Y1 (newifi mini) 专用，波特率 115200，复位键 GPIO#11 |
| breed-mt7620-lenovo-y1s.bin | 联想 Y1S (newifi) 专用，千兆口可用，波特率 115200，复位键 GPIO#11 |
| breed-mt7620-zte-q7.bin | 中兴 ZTE Q7 专用，波特率 57600，复位键 GPIO#26 |
| breed-mt7620-youku-yk1.bin | 优酷路由宝专用，波特率 57600，复位键 GPIO#1 |
| breed-mt7620-xiaomi-mini.bin | 小米 Mini 专用，波特率 115200，复位键 GPIO#30 |
| breed-mt7620-fir302m.bin | 斐讯 FIR300M/302M 专用，波特率 57600，复位键 GPIO#2 |
| breed-mt7620-phicomm-psg1208.bin | 斐讯 PSG1208 (K1)/ PSG1218 (K2) 专用，波特率 57600，复位键 GPIO#1 |
| breed-mt7620-hiwifi-hc5761.bin | 极路由 极壹S (HC5661)/极贰 (HC5761) 专用，波特率 115200，复位键 GPIO#12 |
| breed-mt7620-hiwifi-hc5861.bin | 极路由 极叁 (HC5861) 专用，千兆LAN可用，波特率 115200，复位键 GPIO#12 |
| breed-mt7620-oye-0001.bin | 哦耶 Oye-0001 专用，波特率 115200，复位键 GPIO#1 |
| breed-mt7620-airmobi-iplay2.bin | AirMobi iPlay2 专用，波特率 57600，复位键 GPIO#26 |
| breed-mt7621-newifi-d1.bin | 联想 Newifi D1 专用，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 115200，复位键 GPIO#15，WPS 键 GPIO#18 |
| breed-mt7621-newifi-d2.bin | 联想 Newifi D2 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 115200，复位键 GPIO#3，WPS 键 GPIO#7 |
| breed-mt7621-xunlei-timeplug.bin | 迅雷时光机 (时光云) 专用，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 115200，复位键 GPIO#4 |
| breed-mt7621-youku-l2.bin | 优酷路由宝 YK-L2 专用，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18，WPS 键 GPIO#17 |
| breed-mt7621-phicomm-k2p.bin | 斐讯 K2P 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 57600，复位键 GPIO#3 |
| breed-mt7621-pbr-m1.bin | PandoraBox PBR-M1 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18 |
| breed-mt7621-totolink-a3004ns.bin | TOTOLINK A3004NS 专用，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 57600，复位键 GPIO#4，WPS 键 GPIO#3 |
| breed-mt7621-xiaomi-r3g.bin | 小米路由器 3G 专用，**NAND** 启动，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18 |
| breed-mt7621-creativebox-v1.bin | CreativeBox v1 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18 |
| breed-mt7621-hiwifi-hc5962.bin | 极路由4/HC5962/B70 专用，**NAND** 启动，DDR3 内存适用，默认 256MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18 |
| breed-mt7621-r6220.bin | Netgear R6220 专用，**NAND** 启动，DDR2 内存适用，固定 128MB DDR AC 时序参数，波特率 57600，复位键 GPIO#14，WPS 键 GPIO#7，RFKILL 键 GPIO#8 |
| breed-mt7621-wndr3700v5.bin | Netgear WNDR3700 v5 专用，DDR2 内存适用，固定 128MB DDR AC 时序参数，波特率 57600，复位键 GPIO#14，WPS 键 GPIO#7，RFKILL 键 GPIO#8 |
| breed-mt7621-gehua-ghl-r-001.bin | 歌华 GHL-R-001 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 57600，复位键 GPIO#18 |
| breed-mt7621-jd-cloud-1.bin | 京东云路由宝 RE-SP-01B 专用，DDR3 内存适用，默认 512MB DDR AC 时序参数，波特率 115200，复位键 GPIO#18 |
| breed-mt7628-hiwifi-hc5661a.bin | 极路由 极壹S (HC5661A) 专用，波特率 115200，复位键 GPIO#38 |
| breed-mt7628-oye-0006.bin | 哦耶 OYE-0006 专用，波特率 115200，复位键 GPIO#38 |
| breed-mt7688-reset38.bin | MT7628AN/KN 全通用，波特率 57600，复位键 GPIO#38 |
| breed-mt7688-wrtnode2r.bin | MT7628AN/KN 全通用，波特率 115200，复位键 GPIO#5 |
| breed-rt3050-buffalo-wcr-hp-gn.bin | Buffalo WCR-HP-GN 专用，**SPI** 启动，波特率 57600，复位键 GPIO#10，WPS 键 GPIO#0 |
| breed-rt3050-di-524m-b1.bin | D-LINK DI-624M B1 专用，**SPI** 启动，波特率 57600，复位键 GPIO#10 |
| breed-rt305x-nor-reset0.bin | RT305X 通用，**NOR** 启动，波特率 57600，复位键 GPIO#0 |
| breed-rt305x-nor-reset10.bin | RT305X 通用，**NOR** 启动，波特率 57600，复位键 GPIO#10 |
| breed-rt3052-dir-605-b1.bin | D-LINK DIR-605 B1 专用，**NOR** 启动，波特率 57600，复位键 GPIO#10，WPS 键 GPIO#0 |
| breed-rt3052-hg255d.bin | 华为 HG255D 专用，**NOR** 启动，波特率 115200，复位键 GPIO#4，WPS 键 GPIO#10 |
| breed-rt5350-airmobi-iplay.bin | AirMobi iPlay 专用，波特率 57600，复位键 GPIO#12 |
| breed-rt5350-hame-a5.bin | 华美 A5 专用，波特率 57600，复位键 GPIO#0 |
| breed-rt5350-zm-10.bin | 中沃 ZM-10 专用，波特率 57600，复位键 GPIO#10 |
| breed-ar7161-dir-825-b1.bin | D-LINK DIR-825 B1 专用，波特率 115200，复位键 GPIO#3，WPS 键 GPIO#8 |
| breed-ar724x.bin | AR724X 通用，百兆有线，波特率 115200，复位键 GPIO#11，QSS 键 GPIO#12 |
| breed-ar724x-reset11.bin | AR724X 通用，百兆有线，波特率 115200，复位键 GPIO#11 |
| breed-ar724x-reset12.bin | AR724X 通用，百兆有线，波特率 115200，复位键 GPIO#12 |
| breed-ar7240-wnr1000v2.bin | Netgear WNR1000 v2 专用，百兆有线，波特率 115200 |
| breed-ar7242-wr2543nd.bin | TP-LINK WR2543ND 专用，波特率 115200，复位键 GPIO#11，QSS 键 GPIO#12 |
| breed-ar7242-aruba-ap93.bin | Aruba-AP93 专用，千兆有线，波特率 115200，复位键 GPIO#11，WPS 键 GPIO#12 |
| breed-ar913x.bin | AR913X 通用，百兆有线，波特率 115200，复位键 GPIO#7，WPS 键 GPIO#3 |
| breed-ar9132-wr1043ndv1.bin | TP-LINK WR1043ND v1 专用，波特率 115200，复位键 GPIO#7，WPS 键 GPIO#3 |
| breed-ar9331.bin | AR9331 通用，波特率 115200，复位键 GPIO#11 |
| breed-ar9331-mr12u.bin | TP-LINK MR12U 专用，波特率 115200，复位键 GPIO#11 |
| breed-ar9331-pisen.bin | 品胜云路由 (云座易充 WMM003N/无线音乐路由 WPR001N) 专用，波特率 115200，复位键 GPIO#12 |
| breed-ar9331-wr710n.bin | TP-LINK WR710N/WR720N v3 专用，波特率 115200，复位键 GPIO#11 |
| breed-ar9331-hiwifi-hc6361.bin | 极路由 极壹 (HC6361) 专用，仅支持 TP 类固件，波特率 115200，复位键 GPIO#11 |
| breed-ar9341.bin | AR9341 通用，波特率 115200，复位键 GPIO#17 |
| breed-ar9341-wnr2000v4.bin | Netgear WNR2000 v4 专用，波特率 115200，复位键 GPIO#4 |
| breed-ar9341-pisen-wmp002n.bin | 品胜云追剧 WMP002N 专用，波特率 115200，复位键 GPIO#17 |
| breed-ar9341-wr800n.bin | TP-LINK WR800N 专用，波特率 115200，复位键 GPIO#18 |
| breed-ar9342-wr1041nv2.bin | TP-LINK WR1042N v2 专用，波特率 115200，复位键 GPIO#14 |
| breed-ar9342-huawei-ws322.bin | 华为 WS322 专用，波特率 115200，复位键 GPIO#0，WPS 键 GPIO#16 |
| breed-ar9344.bin | AR9344 百兆版，通用，波特率 115200，复位键 GPIO#16 |
| breed-ar9344-ar8327n.bin | AR9344 + AR8327N 千兆版，通用，波特率 115200，复位键 GPIO#16 |
| breed-ar9344-wdr3320v2.bin | TP-LINK WDR3320  v2 专用，波特率 115200，复位键 GPIO#16 |
| breed-ar9344-wr941nv6.bin | TP-LINK WR941N v6 专用，波特率 115200，复位键 GPIO#12 |
| breed-ar9344-mw4530r.bin | 水星 MW4530R 专用，波特率 115200，复位键 GPIO#17，QSS 键 GPIO#16 |
| breed-ar9344-wndr4300-nand.bin | Netgear WNDR4300/WNDR3700 v4 专用，**NAND** 启动，波特率 115200，复位键 GPIO#21，QSS 键 GPIO#12 |
| breed-ar9344-wndr4300-spi.bin | Netgear WNDR4300/WNDR3700 v4 专用，**SPI** 启动，波特率 115200，复位键 GPIO#21，QSS 键 GPIO#12 |
| breed-ar9344-wndr4300-spi-recovery.bin | Netgear WNDR4300/WNDR3700 v4 专用，**SPI** 启动，仅用于恢复目的，波特率 115200，复位键 GPIO#21，QSS 键 GPIO#12 |
| breed-ar9344-belair20e11.bin | BelAir20E-11 专用，波特率 115200，复位键 GPIO#17，WPS 键 GPIO#12 |
| breed-ar9344-sgr-w500-n85b-v2.bin | 国人通信 GRENTECH SGR-W500-N85b v2 专用，波特率 115200，支持 RTL8211E，复位键 GPIO#3 |
| breed-qca953x.bin | QCA9531/QCA9533，通用，波特率 115200，复位键 GPIO#12 |
| breed-qca953x-letv-lba-047-ch.bin | 乐视路由专用，波特率 115200，复位键 GPIO#17 |
| breed-qca9558-wr941nv7.bin | TP-LINK WR941N v7 专用，波特率 115200，复位键 GPIO#17 |
| breed-qca9558-ar8236.bin | QCA9558 + AR8236 百兆版，通用，波特率 115200，复位键 GPIO#16 |
| breed-qca9558-ar8327n.bin | QCA9558 + AR8327N 千兆版，通用，波特率 115200，复位键 GPIO#16 |
| breed-qca9558-wr2041nv2.bin | TP-LINK WR2041N v2 专用，波特率 115200，复位键 GPIO#17 |
| breed-qca9558-wr1043ndv2.bin | TP-LINK WR1043ND v2 专用，波特率 115200，复位键 GPIO#16 |
| breed-qca9558-dw33d.bin | 大麦 DW33D 专用，波特率 115200，复位键 GPIO#17 |
| breed-qca956x-uart\_rx18\_tx20-reset1.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#18，TX GPIO#20，复位键 GPIO#1 |
| breed-qca956x-uart\_rx18\_tx20-reset2.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#18，TX GPIO#20，复位键 GPIO#2 |
| breed-qca956x-uart\_rx18\_tx22-reset1.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#18，TX GPIO#22，复位键 GPIO#1 |
| breed-qca956x-uart\_rx18\_tx22-reset2.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#18，TX GPIO#22，复位键 GPIO#2 |
| breed-qca956x-uart\_rx19\_tx20-reset1.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#19，TX GPIO#20，复位键 GPIO#1 |
| breed-qca956x-uart\_rx19\_tx20-reset2.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#19，TX GPIO#20，复位键 GPIO#2 |
| breed-qca956x-uart\_rx19\_tx20-reset1.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#19，TX GPIO#22，复位键 GPIO#1 |
| breed-qca956x-uart\_rx19\_tx22-reset2.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#19，TX GPIO#22，复位键 GPIO#2 |
| breed-qca956x-uart\_rx20\_tx22-reset1.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#20，TX GPIO#22，复位键 GPIO#1 |
| breed-qca956x-uart\_rx20\_tx22-reset2.bin | QCA956X 通用，百兆/千兆自适应，波特率 115200，UART RX GPIO#20，TX GPIO#22，复位键 GPIO#2 |
| breed-qca956x-reset2.bin | QCA956X 百兆版，通用，波特率 115200，复位键 GPIO#2 |
| breed-qca9561-wdr6500v2.bin (不再更新) | TP-LINK WDR6500 v2 专用，波特率 115200，复位键 GPIO#1 |
| breed-qca9563-wndr4500v3.bin | Netgear WNDR4500 v3 专用，波特率 115200，复位键 GPIO#2，WPS 键 GPIO#1 |
| breed-qca9563-phicomm-k2t.bin | 斐讯 K2T 专用，波特率 115200，复位键 GPIO#2 |
| breed-qca9563-rosinson-wr818.bin | ROSINSON WR818 专用，波特率 115200，复位键 GPIO#2 |
| breed-qca9563-jhr-848q.bin | JHR-848Q 专用，波特率 115200，复位键 GPIO#2 |
| breed-qca9563-dir-859-a.bin | D-Link DIR-859 A1/A2 专用，波特率 115200，复位键 GPIO#2 |
| breed-tp9343.bin | TP9343，通用，波特率 115200，复位键 GPIO#1，WPS 键 GPIO#1 |  
注：专用版能够点亮所有LED  
  
以下是可以支持自定义复位键 GPIO 的特别版  

| **文件名** | **说明** |
| ------- | ------- |
| breed-ar7161-blank.bin | AR7161 专用，支持 AR8035 IP1001 MV88E1116 BCM5481 千兆 PHY |
| breed-ar913x-blank.bin | AR913X 专用，仅支持 88E6060 百兆交换机 |
| breed-ar724x-blank.bin | AR724X 专用，支持内置百兆交换机和 AR8021 千兆 PHY |
| breed-ar9331-blank.bin | AR9331 专用，仅支持内置百兆交换机 |
| breed-ar934x-blank.bin | AR934X 专用，支持内置百兆交换机和  AR8327(N) 千兆交换机、AR8035 RTL8211E 千兆 PHY、RTL8201 百兆 PHY |
| breed-mt7620-blank.bin | MT7620 专用，仅支持内置百兆交换机 |
| breed-mt76x8-blank.bin | MT7628/MT7688 专用，仅支持内置百兆交换机 |
| breed-rt305x-nor-blank.bin | RT305X 专用，从 **NOR** 闪存启动，仅支持内置百兆交换机 |
| breed-rt305x-spi-blank.bin | RT305X 专用，从 **SPI** 闪存启动，仅支持内置百兆交换机 |
| breed-rt5350-blank.bin | RT5350 专用，仅支持内置百兆交换机 |  
**不再维护的 CPU 才会有此 Blank 版，正常维护的其它的 CPU 依然出专用版和固定复位键的版本**  
  
**\[刷入方式\]**  
跟 U-Boot 相同的刷入方法：  

*   从 PandoraBox U-Boot 中刷入
*   在固件中使用 mtd 命令刷入
*   在 U-Boot TTL 中刷入
*   用编程器刷入  
    

  
**\[下载\]**  
  
**360 路由 C301 不能刷，否则变砖后果自负！**  
  
**请勿在极1原厂固件里刷breed，否则必砖无疑。此hc6361的breed只是用给极1刷TP类型的固件的。**   
  
**注意：TP-LINK TL-WR710N TL-WR720N v3 只能刷 breed-ar9331-wr710n.bin 专用版。刷成其他的变砖后果自负！**  
  
**注意：新老版极壹S CPU不同，不能互刷，刷机前请仔细确认。刷成其他的变砖后果自负！**  
  
**AR/QCA 芯片从 U-Boot 更新到 Breed 后请一定记得检查 MAC 地址是否有效，如果全部是FF，请自行修改！！**  
  
**楼主搭建的下载服务器链接：**   
[http://breed.hackpascal.net/](http://breed.hackpascal.net/)  
  
**鉴于百度网盘实在是太恶心，因此暂时不通过网盘共享了**  

