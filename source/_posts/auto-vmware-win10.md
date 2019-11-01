---
title: Win10开机自动挂载目录到Vmware Linux
date: 2019-11-1 15:00:00
tags:
 - Vmware
 - Win10
---

#### 1.共享目录设置

 1. 查看共享目录,用`vmware-hgfsclient`命令可以看到你的共享目录，这个命令是你安装了vmwaretools成功后生成的

 2. 默认挂载目录为`/mnt/hgfs`

 3. 手动挂载目录命令

    ```bash
    sudo /usr/bin/vmhgfs-fuse .host:/ /mnt/hgfs -o subtype=vmhgfs-fuse,allow_other
    ```

#### 2.设置开启自启动虚拟机

 1. 编写startLinuxVM.bat

    ```bash
    "C:\Program Files (x86)\VMware\VMware Workstation\vmrun.exe" start "E:\VM\Ubuntu\Ubunut1810.vmx" nogui
    ```

2. 将startLinuxVM.bat添加自启动目录

   ```
   C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
   ```
