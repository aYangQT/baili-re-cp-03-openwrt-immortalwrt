无线宝百里re-cp-03刷openwrt/immortalwrt简易教程
注：本文主要讲京东云无线宝百里（re-cp-03或re-cs-05）简易刷官方openwrt/immortalwrt教程，注意备份，防止变砖丢失数据，刷机有风险！

MediaTek MT7986A JDCloud RE-CP-03路由器
硬件规格：
•	SoC：MediaTek MT7986A（4核A53）
•	闪存：128GB eMMC
•	RAM：1GB DDR4
•	以太网：4个1GbE端口，1个2.5GbE端口（RTL8221B）
•	交换芯片：MediaTek MT7531AE
•	Wi-Fi：MediaTek MT7976C
•	按钮：Reset，Joylink
•	电源：DC 12V 2A
百度网盘分享的文件：百里刷机资源整合
链接：https://pan.baidu.com/s/19IItxvkWqRQwtV_w4KgVVQ?pwd=svwj 
提取码：svwj
夸克网盘分享了「百里刷机资源整合」
链接：https://pan.quark.cn/s/1fd81252b5e3
提取码：htkL
阿里云盘（因分享限制需要双击解压）：百里刷机资源整合
https://www.alipan.com/s/EutEMtKQZrf
提取码: g5m9
刷机步骤（以刷immortalwrt为例，刷openwrt操作一样）：
1.	通过WebUI更新无线宝提供的openwrt迁移固件（为获取ssh权限）：
注意：升级OP后官方宣传会失去保修。
o	名称: openwrt-mediatek-mt7986-mt7986a-ax6000-emmc-re-cp-03-squashfs-sysupgrade-4.1.0.r4005.bin
大小: 27331387 字节 (26 MiB)
SHA256: c371601461c491489d2f28391ee3373e4605f95ff5d16cf7d142b9b2031a444c
o	默认地址：192.168.68.1
o	用户名：root
o	密码：无
2.	重启后，使用MobaXterm或其他软件SSH登录192.168.68.1，端口22，用户名root，没有密码。
3.	备份原厂数据，备份后可以通过MobaXterm下载到电脑上保存：
dd if=/dev/mmcblk0boot0 of=/mnt/mmcblk0p13/mmcblk0boot0_bl2.bin conv=fsync
dd if=/dev/mmcblk0p1 of=/mnt/mmcblk0p13/mmcblk0p1_PMBR.bin conv=fsync
dd if=/dev/mmcblk0 bs=512 skip=34 count=8158 of=/mnt/mmcblk0p13/mmcblk0p1_unpartitioned.bin conv=fsync
dd if=/dev/mmcblk0p2 of=/mnt/mmcblk0p13/mmcblk0p2_u-boot-env.bin conv=fsync
dd if=/dev/mmcblk0p3 of=/mnt/mmcblk0p13/mmcblk0p3_factory.bin conv=fsync
dd if=/dev/mmcblk0p4 of=/mnt/mmcblk0p13/mmcblk0p4_fip.bin conv=fsync
dd if=/dev/mmcblk0p5 of=/mnt/mmcblk0p13/mmcblk0p5_kernel.bin conv=fsync
dd if=/dev/mmcblk0p6 of=/mnt/mmcblk0p13/mmcblk0p6_rootfs.bin conv=fsync
dd if=/dev/mmcblk0p7 of=/mnt/mmcblk0p13/mmcblk0p7_kernel2.bin conv=fsync
dd if=/dev/mmcblk0p8 of=/mnt/mmcblk0p13/mmcblk0p8_rootfs2.bin conv=fsync
dd if=/dev/mmcblk0p9 of=/mnt/mmcblk0p13/mmcblk0p9_rootfs_data.bin conv=fsync
dd if=/dev/mmcblk0p10 of=/mnt/mmcblk0p13/mmcblk0p10_log.bin conv=fsync
dd if=/dev/mmcblk0p11 of=/mnt/mmcblk0p13/mmcblk0p11_plugin.bin conv=fsync
dd if=/dev/mmcblk0p12 of=/mnt/mmcblk0p13/mmcblk0p12_swap.bin conv=fsync
4.	写入新的GPT表：
o	执行命令：dd if=immortalwrt-mediatek-filogic-jdcloud_re-cp-03-gpt.bin of=/dev/mmcblk0 bs=512 seek=0 count=34 conv=fsync
3.	擦除并写入新的BL2：
o	禁用只读模式：echo 0 > /sys/block/mmcblk0boot0/force_ro
o	擦除BL2区域：dd if=/dev/zero of=/dev/mmcblk0boot0 bs=512 count=8192 conv=fsync
o	写入BL2：dd if=immortalwrt-mediatek-filogic-jdcloud_re-cp-03-preloader.bin of=/dev/mmcblk0boot0 bs=512 conv=fsync
4.	擦除并写入新的FIP：
o	擦除FIP区域：dd if=/dev/zero of=/dev/mmcblk0 bs=512 seek=13312 count=8192 conv=fsync
o	写入FIP：dd if=immortalwrt-mediatek-filogic-jdcloud_re-cp-03-bl31-uboot.fip of=/dev/mmcblk0 bs=512 seek=13312 conv=fsync
5.	设置PC的静态IP（有的需要关闭防火墙）
o	IP：192.168.1.254/24
o	网关：192.168.1.1
6.	使用TFTP服务器选择并发送immortalwrt-mediatek-filogic-jdcloud_re-cp-03-initramfs-recovery.itb镜像。
 
7.	切断电源后重新接通，按住reset键，直到蓝灯，等待TFTP恢复完成。
8.	immortalwrt启动后，浏览器192.168.1.1页面进行系统升级immortalwrt-mediatek-filogic-jdcloud_re-cp-03-squashfs-sysupgrade.itb。
9.	(可选)如果需要eMMC恢复引导功能（无需担心！你将始终拥有TFTP恢复引导功能）：
o	执行命令：dd if=immortalwrt-mediatek-filogic-jdcloud_re-cp-03-initramfs-recovery.itb of=/dev/mmcblk0p4 bs=512 conv=fsync



如果不是本文提供的固件请跳过本部分，本文提供的固件是本人自编译的，所以需要修改内核md5配置文件，打开MobaXterm通过ssh连接到路由器，通过左侧小框输入/usr/lib/opkg/回车，双击打开status文件
 
然后双击蓝色部分，ctrl+f替换Depends: kernel (= 5.15.162-1-后面的字符为d024313b339f6a16c640db924eb57f35
 
然后ctrl+s保存，点击Yes to all上传到路由器，就可以正常安装官方源的软件包
 

到此为止享用吧

感谢天灵大佬、加菲猫大佬、群友们支持，本人属于小白，整理教程文档多有不足，欢迎指正，我将不断完善文档。
本文后续更新和评论沟通链接【无线宝百里re-cp-03刷openwrt/immortalwrt】 https://www.bilibili.com/video/BV1W6YXeAE6p/?share_source=copy_web&vd_source=c600849569e47687525a256c1adbadb6
参考链接：
Immortalwrt官方镜像下载地址：ImmortalWrt Firmware Selector
天灵大佬：1715173329 (Tianling Shen) · GitHub
加菲猫大佬：Actions-OpenWrt/刷机教程/京东云AX6000百里单分区刷机+回原厂+TTL救砖教程20240405.md at main · lgs2007m/Actions-OpenWrt · GitHub


