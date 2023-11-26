[极1S（hc5661)、极2、极硬货刷不死uboot（breed）及N种固件 - 简书](https://www.jianshu.com/p/8e8c65df6437) 

2018.06.06 11:20:01字数 1,170阅读 28,753

正文： 本人小白，最近看了大神的帖子给极1s刷了不死uboot,刷了几个固件玩，现在给其他跟我差不多的兄弟分享下：我的极1s固件版本已经是9012了，只能用官方的root方法，我没备份key，所以以后就没保修了。

帖子参考地址 [http://bbs.hiwifi.com/forum.php? ... amp;highlight=uboot](http://bbs.hiwifi.com/forum.php?mod=viewthread&tid=74992&highlight=uboot)

1.从极1S-云平台-路由器信息-高级设置里获得你的极路由1s/2/hd的root权限（安装开发者那个app，里面有详细操作介绍）

2.在[http://www.right.com.cn/forum/thread-161906-1-1.html](http://www.right.com.cn/forum/thread-161906-1-1.html)下载适合极路由1s/2/hd的 不死uboot（breed）breed-mt7620-hiwifi-hc5761.bin（作者：hackpascal）

3.使用winscp登入你的极路由，账号：root、密码 ：你的后台密码 端口：22/1022 模式：SCP，登陆成功后进入/tmp目录，将刚才下载的breed-mt7620-hiwifi-hc5761.bin上传到这个目录

4.使用putty登入你的极路由，账号密码端口与上述相同，登入成功后键入以下命令 mtd -r write /tmp/breed-mt7620-hiwifi-hc5761.bin u-boot

5.等待路由重启成功，不死uboot已经完成了刷入了。

不死uboot（breed）使用方法：  
PC用网线连路由器LAN，设置为自动获取IP。  
路由器断电，按住reset 加电（不松开reset）。  
保持按住reset 5秒左右，路由器灯闪。  
PC网卡获取到192.168.1.x的地址 （如未获取到手工设置）  
浏览器访问 192.168.1.1  
接着你就会看到一个uboot控制台的界面。  
以后就想刷什么固件刷什么固件了。

  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/51427dc3-d2c3-440c-a967-607f202527b6.webp)

第二项“固件更新”刷入固件即可。

下面是uboot（breed）和各种固件，都是可以在breed下直接刷入的：  
不死breed及三种固件下载：[http://pan.baidu.com/s/1o6vI0wq](http://pan.baidu.com/s/1o6vI0wq)  
爱快官方论坛固件发布帖子：[http://bbs.ikuai8.com/thread-26628-1-1.html](http://bbs.ikuai8.com/thread-26628-1-1.html)

RippleOS官方固件发布帖子：[http://forum.rippletek.com/thread-6064-1-1.html](http://forum.rippletek.com/thread-6064-1-1.html)

海蜘蛛官方固件发布地址：[http://yun.baidu.com/s/1mgBOlUK](http://yun.baidu.com/s/1mgBOlUK)  
宽带宝固件：[http://www.91kdb.com/forum.php?mod=viewthread&tid=5](http://www.91kdb.com/forum.php?mod=viewthread&tid=5) （ 这个固件是单线多拨固件，一拨免费，多拨要收费，多拨前5天是免费的，主要是多拨设置非常简单，测试自己的线路能否多拨比较简单一点，我刷了以后发现能双拨，然后才换成潘多拉并发多拨）  
2016年11月29日更新潘多拉固件下载地址：[http://downloads.pandorabox.com.cn/Snapshoot/2016-10-07/mt7620/](http://downloads.openwrt.org.cn/PandoraBox/HiWiFi_1S_HD/testing/)

hiwifi for openwrt 固件：[http://rssn.cn/roms/](http://rssn.cn/roms/) 刷5761那个，极1s hc5661、极2、极硬货通用,这个固件最大的好处就是SS设置简单。

winscp和putty：[http://pan.baidu.com/s/1o6N2NAq](http://pan.baidu.com/s/1o6N2NAq)  
这两个软件可能大部分人都有，但是像我这样的小白还得去百度下载，一并附上吧

三种固件，RippleOS就是商用AP，手机上网各种认证方式很全，注册商家后可以配置自己的web认证。

爱快固件比较早，没有更新，功能很少，但是有单线多拨、7层智能流控，跟我用的爱快软路由界面差不多，就是功能少了很多。

海蜘蛛的完全就是用asuswrt改的（我有个rt-n16，界面完全一样），刷这个固件lan4变成wan，这个固件有双wan，就是可以把某个lan当做wan2，跟rt-n16一样，我曾经用ubnt裸板蹭邻居的网然后接到wan2。我这个极1s是跟别人换的，已经无损改usb，插上U盘，海蜘蛛固件里就显示出U盘来了。这固件功能挺多，除了海蜘蛛加入的AP/ac功能，其他的跟华硕的固件一样。  
海蜘蛛的截图：

  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/e05de237-024f-453f-8b3c-989d8ee23336.webp)

image.png

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/a711a7f7-0742-42af-9954-ff37f58c1caa.webp)

  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/4b157e3b-789d-4d94-8110-4713a316eb1e.webp)

  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/9329a5aa-8275-4bc4-9451-803dae282abc.webp)

  
后来又刷过newifi、小云路由、hiwifi for openwrt、宽带宝、潘多拉等固件，因为我需要单线多拨，所以现在一直用着潘多拉固件：[http://downloads.openwrt.org.cn/ ... -r1068-20150618.bin](http://downloads.openwrt.org.cn/PandoraBox/HiWiFi_1S_HD/testing/PandoraBox-ralink-mt7620-ji2-squashfs-sysupgrade-r1068-20150618.bin) ，这个固件极1s-hc5661 、hc5761、极硬货都可刷。

hiwifi for openwrt的SS插件很好用，设置简单，潘多拉自带的SS设置起来太复杂了，有个坛友还出了个配置教程：[http://www.right.com.cn/forum/fo ... 6orderby%3Ddateline](http://www.right.com.cn/forum/forum.php?mod=viewthread&tid=174131&extra=page%3D1%26filter%3Dauthor%26orderby%3Ddateline) ，对我等小白来说是极好的。

hiwifi for openwrt固件：[http://rssn.cn/roms/](http://rssn.cn/roms/) ，这个固件自带的SS插件设置起来非常简单，就是自带的插件很少，没有虚拟多wan口。

最后编辑于：2018.06.06 13:46:45
