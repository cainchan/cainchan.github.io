---
title: iptables笔记
date: 2018-01-01 00:00:00
tags: Iptables
---

```bash
iptables -P INPUT ACCEPT/DROP
iptables -P OUTPUT ACCEPT/DROP
iptables -P FORWARD ACCEPT/DROP

iptables -F #清除预设表filter中的所有规则链的规则
iptables -X #清除预设表filter中使用者自定链中的规则
iptables -Z #清除预设表filter中使用者自定链中的规则
iptables -L -n #查看
iptables-save  #预览规则内容
service iptables save #保存到默认文件
service iptables restart #重启


#接纳属於现存及相关连接的压缩
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#允许icmp包通过,也就是允许ping
iptables -A INPUT -p icmp -j ACCEPT
#允许loopback(不然会导致DNS无法正常关闭等问题)
iptables -A INPUT -i lo -j ACCEPT
#添加必要端口
iptables -A INPUT -s 192.168.0.0/16 -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -j REJECT #拒绝其他
iptables -A FORWARD -j REJECT #拒绝转发


iptables -L --line-numbers #显示行号
iptables -D INPUT 8 #删除INPUT的第8条规则
```

