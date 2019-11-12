---
title: SSH自动加载密钥
date: 2019-11-11 20:29:15
tags:  git
---

# 如何让SSH自动加载密钥?
## ssh 依次从下面的三个地方读取配置

1. 命令行选项（就是指上面的ssh -i）
2. 用户级配置文件 ~/.ssh/config
3. 系统级配置文件 /etc/ssh/ssh_config

---

我们操作用户级配置文件，命令如下：
```
touch config

AddKeysToAgent yes
IdentityFile ~/.ssh/id_rsa_github
IdentityFile ~/.ssh/id_rsa 

# Company account
Host *.51qq.club
HostName gitlab.51qq.club
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa

# Personal account
Host *.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_github```

参考链接：
https://segmentfault.com/a/1190000013798839?utm_source=tag-newest


