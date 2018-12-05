

# 解决方案

```

```



# 实验环境

| 操作系统              | Vsftpd |
| --------------------- | ------ |
| 3.10.0-514.el7.x86_64 | 3.0.2  |

# 软件简介

> Vsftpd是"very secure FTP daemon”的缩写,开源,安全是它的最大特点,除此之外还支持带宽限制,虚拟用户等众多特性

# 安装步骤

```bash
# vsftpd为服务端软件
# ftp为命令行客户端软件
yum -y install vsftpd ftp
```

# 配置步骤

## 创建虚拟账户

> vim /etc/vsftpd/vsftpd_auth

```
user1
user1_pass
user2
user2_pass
user3
user3_pass
```

1. 我们的软件其实是
2. 简单介绍一下



