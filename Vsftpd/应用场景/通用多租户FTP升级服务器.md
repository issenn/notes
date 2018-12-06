

---

* [需求整理](#需求整理)
* [解决方案](#解决方案)
* [实验环境](#实验环境)
* [软件介绍](#软件介绍)
* [配置步骤](#配置步骤)
  * [创建虚拟账户](#创建虚拟账户)
  * [创建认证数据库](#创建认证数据库)
  * [创建虚拟用户映射的系统用户](#创建虚拟用户映射的系统用户)
  * [为虚拟用户独立创建工作目录](#为虚拟用户独立创建工作目录)
  * [添加服务对应PAM模块验证块](#添加服务对应PAM模块验证块)
  * [编辑全局主配置文件](#编辑全局主配置文件)
  * [限制虚拟用户向上切换目录](#限制虚拟用户向上切换目录)
  * [为虚拟用户创建独立配置](#为虚拟用户创建独立配置)
  * [为匿名用户设置工作目录](#为匿名用户设置工作目录)

* [启动服务](#启动服务)
* [系统测试](#系统测试)
  * [匿名用户测试](#匿名用户测试)
  * [虚拟用户测试](#虚拟用户测试)
  * [USER1权限测试](#USER1权限测试)
  * [USER2权限测试](#USER2权限测试)
  * [USER3权限测试](#USER3权限测试)

---

# 需求整理

* IOS和客户端升级流程大同小异,都支持自定义升级配置文件,请求解析自定义的配置文件内容决定是否下载升级文件升级

# 解决方案

```
               请求更新配置文件
            C  -------------->  S
            |                   |
        是否需更新               |
            |                   |
      ---—--|————---            |
     是            否           |
     |                          |
     |_________________________>|
     请求升级文件
```

* 使用多租户FTP
* 使用对象存储OSS
* 重写DjangoStrage

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

> vim /etc/vsftpd/vsftpd_vauth

```
user1
user1_pass
user2
user2_pass
user3
user3_pass
```

* 如上文件将作为虚拟用户数据库源文件,规则为基数行为用户名,偶数行为密码

## 创建认证数据库

```bash
cd /etc/vsftpd
# 读取虚拟账户文件中的数据生成系统pam认证模块识别的数据库文件
db_load -T -t hash -f vsftpd_vauth vsftpd_vauth.db
# 设置600权限不允许其它人读取
chmod 600 vsftpd_vauth*
```

## 创建虚拟用户映射的系统用户

```bash
# 创建系统用户家目录,作为FTP用户登陆默认根目录
mkdir -p /workspace/apps/vsftpd/ 
# 创建一个禁止登陆的系统账户vsftpd作为虚拟用户的认证后的映射用户
useradd -M -d /workspace/apps/vsftpd/ -s /sbin/nologin vsftpd
```

## 为虚拟用户独立创建工作目录

```bash
mkdir -p /workspace/apps/vsftpd/user1/
mkdir -p /workspace/apps/vsftpd/user2/
mkdir -p /workspace/apps/vsftpd/user3/
# 设置下面目录目录属主和属组都为vsftpd
chown -R vsftpd:vsftpd /workspace/apps/vsftpd/
# 设置下面目录权限755,允许其它人进入此目录查看
chmod 755 -R /workspace/apps/vsftpd/
```

## 添加服务对应PAM模块验证块

> vim /etc/pam.d/vsftpd

```bash
auth    sufficient      /lib64/security/pam_userdb.so    db=/etc/vsftpd/vsftpd_vauth
account sufficient      /lib64/security/pam_userdb.so    db=/etc/vsftpd/vsftpd_vauth
```

* Vsftpd的PAM认证支持本地系统用户认证(pam_unix),也支持独立用户数据库认证(pam_userdb),也支持网络Ldap数据库认证(pam_ldap)
* 在#%PAM-1.0下添加如上内容, 需要注意的是db=/etc/vsftpd/vsftpd_vauth表示数据库文件为/etc/vsftpd/vsftpd_vauth.db,不要加后缀”.db"

## 编辑全局主配置文件

> vim /etc/vsftpd/vsftpd.conf

```
# 设置默认文件掩码
local_umask=022
# 允许从20端口连接,默认端口21
connect_from_port_20=YES
# 使用本地时间
use_localtime=YES
# 允许本地账户登录
local_enable=YES
# 允许匿名用户访问
anonymous_enable=YES
# 禁止匿名用户上传
anon_upload_enable=NO
# 禁止匿名用户创建目录
anon_mkdir_write_enable=NO
# 禁止匿名用户删除或重命名
anon_other_write_enable=NO
# 启用虚拟用户
guest_enable=YES
# 指定虚拟用户映射的系统用户
guest_username=vsftpd
# 虚拟用户独立配置目录
user_config_dir=/etc/vsftpd/vsftpd_vconf
# 启用IPV4
listen=YES
# PAM验证服务名
pam_service_name=vsftpd
# 启用user_list限制
userlist_enable=YES
# 启用tcp_wrappers限制
tcp_wrappers=YES
# 限制用户只能访问属于自己的目录
allow_writeable_chroot=YES
ascii_upload_enable=YES
ascii_download_enable=YES
# 限制用户向上切换目录
chroot_local_user=YES
# 启用限制用户列表
chroot_list_enable=YES
# 指定限制用户列表文件
chroot_list_file=/etc/vsftpd/chroot_list
xferlog_file=/var/log/vsftpd.log
xferlog_enable=YES
xferlog_std_format=YES
# 开启被动模式
pasv_enable=YES
pasv_min_port=10030
pasv_max_port=10035
```

## 限制虚拟用户向上切换目录

> vim /etc/vsftpd/chroot_list

```
user1
user2
user3
```



## 为虚拟用户创建独立配置

````bash
mkdir -p /etc/vsftpd/vsftpd_vconf/user1
mkdir -p /etc/vsftpd/vsftpd_vconf/user2
mkdir -p /etc/vsftpd/vsftpd_vconf/user3
````

* 需要注意的是/etc/vsftpd/vsftpd_vconf/下的文件名字必须是上面创建的虚拟用户名

> vim /etc/vsftpd/vsftpd_vconf/user1

```
# 设置虚拟用户限制家目录
local_root=/workspace/apps/vsftpd/user1/
# 允许虚拟用户是否可以写入
write_enable=YES
anon_umask=022
# 允许虚拟用户浏览
anon_world_readable_only=NO
# 允许虚拟用户上传
anon_upload_enable=YES
# 允许虚拟用户创建目录
anon_mkdir_write_enable=YES
# 允许虚拟用户删除或重命名
anon_other_write_enable=YES
# 允许同一账户在10个不同的IP登陆
max_per_ip=10
# 允许最大下载速度(字节/秒)
local_max_rate=2500000
# 设置会话超时时间
idle_session_timeout=300
# 连接超时时间
data_connection_timeout=90
# 最多连接数
max_clients=2
```

* user1拥有所有权限

> vim /etc/vsftpd/vsftpd_vconf/user2

```
# 设置虚拟用户限制家目录
local_root=/workspace/apps/vsftpd/user2/
# 允许虚拟用户是否可以写入
write_enable=YES
anon_umask=022
# 允许虚拟用户浏览
anon_world_readable_only=NO
# 允许虚拟用户上传
anon_upload_enable=YES
# 允许同一账户在10个不同的IP登陆
max_per_ip=10
# 允许最大下载速度(字节/秒)
local_max_rate=2500000
# 设置会话超时时间
idle_session_timeout=300
# 连接超时时间
data_connection_timeout=90
# 最多连接数
max_clients=2
```

* user2只有上传下载权限

> vim /etc/vsftpd/vsftpd_vconf/user3

```
# 设置虚拟用户限制家目录
local_root=/workspace/apps/vsftpd/user3/
# 允许虚拟用户是否可以写入
write_enable=YES
anon_umask=022
# 允许虚拟用户浏览
anon_world_readable_only=NO
# 允许虚拟用户上传
anon_upload_enable=YES
# 允许虚拟用户创建目录
anon_mkdir_write_enable=YES
# 允许同一账户在10个不同的IP登陆
max_per_ip=10
# 允许最大下载速度(字节/秒)
local_max_rate=2500000
# 设置会话超时时间
idle_session_timeout=300
# 连接超时时间
data_connection_timeout=90
# 最多连接数
max_clients=2
```

* user3只有上传下载及创建目录权限

## 为匿名用户设置工作目录

> vim /etc/passwd

```
ftp:x:14:50:FTP User:/workspace/apps/vsftpd/:/sbin/nologin 
```

* 由于上面启用匿名用户访问,而它默认家目录为/var/ftp,可将其修改为匿名用户工作目录的上级目录

# 启动服务

```bash
# 重启Vsftpd服务
systemctl restart vsftpd
# 设置开机自启动
ystemctl enable vsftpd
# 查看端口是否监控
netstat -antupl | grep vsftpd
tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      8564/vsftpd 
```



# 系统测试

## 匿名用户测试

```bash
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (127.0.0.1:root): ftp
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
227 Entering Passive Mode (127,0,0,1,39,51).
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Dec 05 23:28 user1
drwxr-xr-x    2 1001     1001         4096 Dec 05 23:28 user2
drwxr-xr-x    2 1001     1001         4096 Dec 05 23:28 user3
226 Directory send OK.
ftp> cd user1
250 Directory successfully changed.
ftp> ls
227 Entering Passive Mode (127,0,0,1,39,51).
150 Here comes the directory listing.
226 Directory send OK.
ftp>                                                             
```



## 虚拟用户测试

```bash
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (127.0.0.1:root): user1
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/"
ftp>                                             
```



## USER1权限测试

```bash
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (127.0.0.1:root): user1
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/"
ftp> mkdir upload
257 "/upload" created
ftp> dir
227 Entering Passive Mode (127,0,0,1,39,50).
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Dec 06 10:22 upload
226 Directory send OK.
ftp> rmdir upload
250 Remove directory operation successful.
ftp> dir
227 Entering Passive Mode (127,0,0,1,39,46).
150 Here comes the directory listing.
226 Directory send OK.
```



## USER2权限测试

```bash
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (127.0.0.1:root): user2
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> mkdir upload
550 Permission denied.
ftp> put /etc/passwd passwd
local: /etc/passwd remote: passwd
227 Entering Passive Mode (127,0,0,1,39,48).
150 Ok to send data.
226 Transfer complete.
2310 bytes sent in 2.5e-05 secs (92400.00 Kbytes/sec)
ftp> ls
227 Entering Passive Mode (127,0,0,1,39,46).
150 Here comes the directory listing.
-rw-r--r--    1 1001     1001         2310 Dec 06 10:24 passwd
226 Directory send OK.
```



## USER3权限测试

```bash
ftp 127.0.0.1
Connected to 127.0.0.1 (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (127.0.0.1:root): user3
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> mkdir upload
257 "/upload" created
ftp> cd upload
250 Directory successfully changed.
ftp> put /etc/passwd passwd
local: /etc/passwd remote: passwd
227 Entering Passive Mode (127,0,0,1,39,48).
150 Ok to send data.
226 Transfer complete.
2310 bytes sent in 1.4e-05 secs (165000.00 Kbytes/sec)
ftp> delete passwd
550 Permission denied.
```

