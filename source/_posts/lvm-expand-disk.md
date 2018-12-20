---
title: LVM扩展根目录空间
date: 2018-01-01
tags: CentOS
---
### LVM扩展根目录空间
```bash
df -h #查看挂载 /dev/mapper/前缀的表示支持LVM扩展
yum install lvm2* part* #必要工具
fdisk -l #查看新硬盘
fdisk /dev/sdb #硬盘分区 需手动输入指令
#输入n进行分区
#输入p为主分区
#输入1(一个主分区)
#输入回车(起始磁盘数默认)
#输入回车(磁盘大小默认全部)
#输入t修改分区格式
#输入8e(LVM必须为此格式)
#输入p查看分区
#输入w写入分区

mkfs.ext4 /dev/sdb1 #格式化分区
partprobe #重新读取分区表 无需重启
pvcreate /dev/sdb1 #创建物理卷(physical volume)
pvscan #查看物理卷
pvdisplay #查看物理卷详情
vgscan #查看卷组(Volume Group)
vgdisplay #查看卷组详情
vgextend VolGroup00 /dev/sdb1    #扩展VolGroup00
lvscan #查看逻辑卷(logical volume)
lvdisplay #查看逻辑卷详情 以lv_root为例
lvextend -L +100G /dev/VolGroup00/lv_root #扩展lv_root  精确扩展可用-l指定PE扩展
resize2fs /dev/VolGroup00/lv_root #重设lv_root大小
df -h #查看挂载情况，已经扩容

#非LVM扩展空间
fdisk -l		#查看硬盘
fdisk /dev/sdb   	#硬盘分区 需手动输入指令
#输入n进行分区
#输入p为主分区
#输入1(一个主分区)
#输入回车(起始磁盘数默认)
#输入回车(磁盘大小默认全部)
#输入p查看分区
#输入w写入分区

mkfs.ext4 /dev/sdb1 #格式化分区
mv /var/lib/redis /backup #先备份
mkdir /var/lib/redis
mount /dev/sdb1 /var/lib/redis
mv /backup/redis/* /var/lib/redis
echo "/dev/sdb1 /var/lib/redis ext4 defaults 0 0" >> /etc/fstab   
#修改/etc/fstab 加入启动挂载
```

