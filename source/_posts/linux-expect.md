---
title: 利用expect实现自动交互ssh/scp
date: 2021-08-20 17:00:00
tags:
 - expect
 - Linux
---

#### 自动ssh

```bash
#!/usr/bin/expect
set host 192.168.10.210
set user protest2
set password 1qazXDR%
set port 22

spawn ssh $user@$host -p $port
expect {
"(yes/no)?" { send "yes\r";exp_continue }
"*assword:*" { send "$password\r" }
}
interact
```

#### 自动scp

```bash
#!/usr/bin/expect
set password 1qazXDR%
spawn scp /tmp/1.txt protest2@192.168.10.210:/tmp/
expect {
  "(yes/no)?" { send "yes\n";exp_continue}
  "*assword:" { send "$password\n" }
}
expect "100%"
expect eof
```





