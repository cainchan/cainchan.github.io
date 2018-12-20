---
title: CentOS6上安装和配置KVM
date: 2016-11-14 00:00:00
tags:
 - CentOS
 - KVM
---

检查CPU是否支持虚拟化

```bash
# egrep 'vmx|svm' /proc/cpuinfo 
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts mmx fxsr sse sse2 ss syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts xtopology tsc_reliable nonstop_tsc aperfmperf unfair_spinlock pni vmx ssse3 cx16 sse4_1 sse4_2 x2apic popcnt hypervisor lahf_lm arat dts tpr_shadow vnmi ept vpid
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts mmx fxsr sse sse2 ss syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts xtopology tsc_reliable nonstop_tsc aperfmperf unfair_spinlock pni vmx ssse3 cx16 sse4_1 sse4_2 x2apic popcnt hypervisor lahf_lm arat dts tpr_shadow vnmi ept vpid
```

安装KVM

```bash
# yum install kvm kmod-kvm qemu kvm-qemu-img virt-viewer virt-manager \
libvirt libvirt-python python-virtinst
```

安装完后重启系统，然后确认一下是否安装成功：

```bash
# reboot
# lsmod | grep kvm
kvm_amd                69416  0 
kvm                   226208  1 kvm_amd
# ll /dev/kvm 
crw-rw---- 1 root kvm 10, 232 Jun 25 15:56 /dev/kvm
```

至此，KVM环境已安装完成，接下来，开始安装虚拟机。
安装之前需要准备一个系统镜像ISO，我这里用的是WindowsServer2003。路径为/kvmdata/kaychen/cn_win_srv_2003_r2_enterprise_x64_with_sp2.iso

先创建一块虚拟硬盘

```bash
# qemu-img  create -f qcow2 /kvmdata/kaychen/winserver2003.img 20G
```

安装系统

```bash
# virt-install \
--name winserver2003 \
--ram 2048 \
--vcpus=2 \
--disk path=/kvmdata/kaychen/winserver2003.img,device=disk,format=qcow2,bus=ide,cache=writeback,size=15 \
--accelerate \
--os-type=windows \
--virt-type=kvm \
--disk /kvmdata/kaychen/cn_win_srv_2003_r2_enterprise_x64_with_sp2.iso,device=cdrom \
--vnc --vncport=9125 --vnclisten=0.0.0.0 \
--noautoconsole
```

虚拟机启动成功后，我们需要用VNC连接IP:9125来安装操作系统，直到系统安装完成。