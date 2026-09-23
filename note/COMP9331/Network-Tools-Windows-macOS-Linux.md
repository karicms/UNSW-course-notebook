# COMP9331：网络工具速查与跨平台命令

> 本文是实验中常见网络工具的快速参考。课程 guide 的命令多数以 Linux/Unix 为例；Windows、macOS 和 Linux 的可用命令并不完全相同。

## 0. 先记住：三个系统的差别

| 目标 | Windows（PowerShell / CMD） | macOS Terminal | Linux Terminal / VLAB |
| --- | --- | --- | --- |
| 路径追踪 | `tracert host` | `traceroute host` | `traceroute host` |
| IP 配置 | `ipconfig` | `ifconfig` 或 `ipconfig getifaddr en0` | `ip addr` |
| 路由表 | `route print` / `Get-NetRoute` | `netstat -rn` / `route -n get default` | `ip route` |
| 连接与监听端口 | `netstat -ano` / `Get-NetTCPConnection` | `netstat -an` / `lsof -i` | `ss -tuln` |
| DNS 查询 | `Resolve-DnsName host` / `nslookup host` | `dig host` / `host host` / `nslookup host` | `dig host` / `host host` / `nslookup host` |
| 文本搜索 | `Select-String pattern file` | `grep pattern file` | `grep pattern file` |
| 测试 TCP port | `Test-NetConnection host -Port 443` | `nc -vz host 443` | `nc -vz host 443` |

`ping`、`ssh` 和 `netstat` 在三种系统都常见；选项和输出可能略有区别。

---

## 1. ping：连通性与 RTT

`ping` 发送 ICMP Echo Request，并等待 Echo Reply。它可初步判断目标是否可达，也能显示 RTT（round-trip time，往返时间）。

| Windows | macOS | Linux |
| --- | --- | --- |
| `ping google.com` | `ping google.com` | `ping google.com` |
| `ping -n 4 google.com`（发 4 次） | `ping -c 4 google.com` | `ping -c 4 google.com` |

注意：能 ping 通不代表 HTTP/HTTPS 服务一定正常；有些主机或防火墙会禁止 ICMP，因此 ping 不通也未必代表网站不存在。

---

## 2. traceroute / tracert：数据包走过的路径

此工具显示到目标主机途中经过的 routers（hops）。它通过不断提高 TTL 值发送探测包：TTL 在某台 router 降为 0 时，该 router 通常回覆 ICMP Time Exceeded。

```text
你的电脑 -- hop 1 -- hop 2 -- hop 3 -- ... -- 目标主机
```

| Windows | macOS | Linux |
| --- | --- | --- |
| `tracert google.com` | `traceroute google.com` | `traceroute google.com` |

你在 PowerShell 运行 `traceroute` 报“not recognized”是正常的：Windows 原生命令叫 **`tracert`**，少了 `ou`。

输出中：

- 每一行是一跳；第一跳通常是家庭 router，例如 `192.168.1.1`。
- 延迟通常会显示多个探测结果。
- `* * *` 不一定表示网络坏了；该 router 可能只是过滤或忽略了这种探测包。

---

## 3. telnet：旧式远程登录；现常用于端口诊断

Telnet 以前用于远程登录，但它不加密，密码和数据可能被窃听，因此**不应用于登录**。SSH 是现代、安全的替代方案。

Telnet client 的实用保留用途：检查某台主机的 TCP port 是否可连接。

```text
host + TCP port -> TCP connection succeeds? -> 服务端口大致可达
```

| Windows | macOS | Linux |
| --- | --- | --- |
| `Test-NetConnection google.com -Port 443` | `nc -vz google.com 443` | `nc -vz google.com 443` |
| `tnc google.com -Port 443`（缩写） | `telnet google.com 443`（若已安装） | `telnet google.com 443`（若已安装） |

Windows 的 `Test-NetConnection` 是最推荐的替代：查看 `TcpTestSucceeded : True` 即表示 TCP 连接可建立。

常见 TCP ports：

```text
22   SSH
80   HTTP
443  HTTPS
```

---

## 4. ssh：安全远程连接

SSH（Secure Shell）用于安全、加密地远程登录主机。

| Windows | macOS | Linux |
| --- | --- | --- |
| `ssh username@hostname` | `ssh username@hostname` | `ssh username@hostname` |

现代系统通常使用 key-based authentication（密钥认证），而不只依赖密码。之后连接 CSE / VLAB 的 Linux 环境时可能会见到它。

---

## 5. 查看 IP、网卡与路由

### IP 与网卡信息

| Windows | macOS | Linux |
| --- | --- | --- |
| `ipconfig` | `ifconfig` | `ip addr` |
| `Get-NetIPAddress` | `networksetup -listallhardwareports` | `ip -s link` |

你电脑当前有效 Ethernet 网卡的 IPv4 是 `192.168.1.106`，subnet mask 是 `255.255.255.0`，default gateway 是 `192.168.1.1`。

`ip addr` 是 Linux 的 `iproute2` 命令，因此在 PowerShell 报错完全正常。Windows 用 `ipconfig`；较新的 PowerShell 也可用 `Get-NetIPAddress`。

### 路由表

| Windows | macOS | Linux |
| --- | --- | --- |
| `route print` | `netstat -rn` | `ip route` |
| `Get-NetRoute` | `route -n get default`（查看默认路由） | `ip route get 8.8.8.8`（查特定目的地） |

---

## 6. Route Table（路由表）是什么？

路由表是操作系统用来决定“**一个 IP packet 下一步该交给谁**”的规则表。

例如你要访问 Google：

```text
你的电脑
  | 查 route table：目的地不在本地 192.168.1.0/24 网络
  v
Default Gateway 192.168.1.1（家中 router）
  v
ISP / Internet
  v
Google
```

一行 route 通常包含：

| 字段 | 含义 |
| --- | --- |
| Network Destination | 此规则匹配的目的 IP 网络。|
| Netmask | 哪些 IP 位属于网络前缀；与 Destination 一起决定可匹配范围。|
| Gateway | 下一跳 router；`On-link` 表示目标就在同一个本地链路，不需经过 router。|
| Interface | 用来发送 packet 的本机网卡／本机 IP。|
| Metric | 多条规则都可用时的优先级成本；通常数值越小越优先。|

### 选路原则：最长前缀匹配

系统优先使用最具体的匹配规则（longest-prefix match），而不是简单选择表格第一行。

```text
目标 192.168.1.50
  -> 匹配 192.168.1.0 / 255.255.255.0（本地网络）
  -> 不会走默认路由

目标 8.8.8.8
  -> 不匹配本地网络
  -> 匹配 0.0.0.0 / 0.0.0.0（默认路由）
  -> 交给默认网关
```

### 你当前 IPv4 route table 的解读

#### 1. 默认路由：所有不认识的外部目的地

```text
0.0.0.0   0.0.0.0   192.168.1.1   192.168.1.106   25
```

`0.0.0.0/0` 是 **default route（默认路由）**：若没有更具体的匹配，packet 经本机 Ethernet（`192.168.1.106`）交给 gateway `192.168.1.1`。

所以访问 Google、UNSW 或其他外网时，第一跳通常是你家的 router `192.168.1.1`。

#### 2. Loopback：电脑访问自己

```text
127.0.0.0        255.0.0.0         On-link   127.0.0.1
127.0.0.1        255.255.255.255   On-link   127.0.0.1
127.255.255.255  255.255.255.255   On-link   127.0.0.1
```

`127.0.0.0/8` 是 **loopback network**，即本机内部网络；最常见地址为 `127.0.0.1` 或名称 `localhost`。数据不会离开电脑，因此 gateway 是 `On-link`。

#### 3. 你的本地家庭 LAN

```text
192.168.1.0    255.255.255.0       On-link   192.168.1.106
192.168.1.106  255.255.255.255     On-link   192.168.1.106
192.168.1.255  255.255.255.255     On-link   192.168.1.106
```

`192.168.1.0` + `255.255.255.0` 等同 `192.168.1.0/24`，表示本地网络范围：

```text
192.168.1.1   到   192.168.1.254
```

同一 LAN 内，例如访问 router `192.168.1.1` 或局域网设备 `192.168.1.50`，无需通过 gateway；你的电脑会直接在 Ethernet LAN 上寻找目标设备（通常借助 ARP）。

其中：

- `192.168.1.106/32`：你的电脑自己的 IPv4 host route。
- `192.168.1.255/32`：这个 `/24` LAN 的 directed broadcast address。

#### 4. Multicast routes

```text
224.0.0.0  240.0.0.0  On-link  127.0.0.1
224.0.0.0  240.0.0.0  On-link  192.168.1.106
```

`224.0.0.0/4` 是 IPv4 multicast 地址范围。Multicast 是“一对多”传输机制，系统为 loopback 和 Ethernet interface 都保留相关路由。普通浏览网页通常不需要你手动处理它。

#### 5. Limited broadcast

```text
255.255.255.255  255.255.255.255  On-link  127.0.0.1
255.255.255.255  255.255.255.255  On-link  192.168.1.106
```

`255.255.255.255` 是 IPv4 limited broadcast address：只在当前本地链路传播，routers 不会将它转发到 Internet。

### Persistent Routes: None

你的输出最后写：

```text
Persistent Routes: None
```

这表示没有人为额外配置、并要求 Windows 重启后仍保留的静态路由。当前表中的路由通常由你的网卡配置、DHCP 和 Windows 自动生成，是正常状态。

---

## 7. ss / netstat：socket、连接与监听端口

这类工具回答两个问题：

1. 本机有哪些服务正在监听某个 port？
2. 本机目前建立了哪些网络连接？

| Windows | macOS | Linux |
| --- | --- | --- |
| `netstat -ano` | `netstat -an` | `ss -tuln` |
| `Get-NetTCPConnection` | `lsof -i` | `ss -tan` |

Linux：

```bash
ss -tuln
```

其中 `-t` 为 TCP、`-u` 为 UDP、`-l` 只显示 listening socket、`-n` 显示数字 IP/port。

Windows：

```powershell
netstat -ano
Get-NetTCPConnection -State Listen
```

`netstat -ano` 的 `PID` 可用来定位程序：

```powershell
Get-Process -Id <PID>
```

Linux 中 `ss` 是较现代的替代；`netstat` 在 Linux 通常被视为旧工具，但 Windows 中仍很实用。

---

## 8. netperf 与 iperf3：测网络性能

两者都用于受控地测量两台主机间的网络性能，而不是普通网页测速。

| 工具 | 作用 | 现况 |
| --- | --- | --- |
| `netperf` | 可测试 TCP stream、request/response 延迟等 | 可能没有默认安装，较少见 |
| `iperf3` | 常用的 client/server 吞吐量测试工具 | 更常见；lab 中更值得认识 |

`iperf3` 基本流程：

```text
Machine A                         Machine B
iperf3 -s  <----------------  iperf3 -c <A 的 IP>
server                               client
```

实际命令：

```bash
# 机器 A
iperf3 -s

# 机器 B
iperf3 -c 192.168.1.10
```

它会报告端到端 TCP throughput，例如 `940 Mbits/sec`。两端都需要安装 iperf3，且 server 的默认 TCP port（通常 5201）必须可达。

| Windows | macOS | Linux |
| --- | --- | --- |
| 安装后：`iperf3.exe -s` / `iperf3.exe -c host` | `brew install iperf3` 后运行同样命令 | 包管理器安装后运行同样命令 |

现在不必专门在 Windows 安装 `netperf` 或 `iperf3`；如果后续 lab 要求，优先在 VLAB/Linux 环境按要求运行。

---

## 9. DNS：nslookup、dig 与 host

DNS（Domain Name System）负责把域名转换为 IP：

```text
google.com -> 142.250.x.x（IPv4 A record）
           -> 2404:...（IPv6 AAAA record）
```

| 工具 | 特点 | Windows | macOS | Linux |
| --- | --- | --- | --- | --- |
| `nslookup` | 常见、较旧、简单 | `nslookup google.com` | `nslookup google.com` | `nslookup google.com` |
| `dig` | 详细、DNS troubleshooting 首选 | 默认没有 | `dig google.com` | `dig google.com` |
| `host` | 简洁 DNS 查询 | 不适用 | `host google.com` | `host google.com` |
| `Resolve-DnsName` | PowerShell 原生命令 | `Resolve-DnsName google.com` | 不适用 | 不适用 |

你已成功运行：

```powershell
nslookup google.com
```

其中 `Non-authoritative answer` 通常表示你的 DNS resolver 从缓存或递归查询取得答案，而不是 Google DNS authoritative server 直接给出的答案。

PowerShell 推荐：

```powershell
Resolve-DnsName google.com
Resolve-DnsName google.com -Type A       # IPv4
Resolve-DnsName google.com -Type AAAA    # IPv6
```

为什么你的 `host google.com` 报错？PowerShell 将 `host` 解释为本身的 `Get-Host`（PowerShell 主机信息）别名，而不是 Unix 的 DNS `host` 工具。改用 `Resolve-DnsName` 或 `nslookup`。

---

## 10. grep：文本模式搜索

`grep` 在文件中搜索匹配文本，网络实验里常用于在输出、日志或配置中查找信息。

| Windows PowerShell | macOS | Linux |
| --- | --- | --- |
| `Select-String 'smtp' C:\path\file.txt` | `grep smtp /etc/services` | `grep smtp /etc/services` |
| `Select-String -SimpleMatch 'smtp' C:\path\file.txt` | `grep -F smtp /etc/services` | `grep -F smtp /etc/services` |

PowerShell 常用简写：

```powershell
sls 'smtp' C:\path\file.txt
```

`grep -F` 或 PowerShell 的 `-SimpleMatch` 表示按普通字符串搜索，不把关键词解释为正则表达式。

---

## 11. Windows 本机最实用命令清单

```powershell
# 连接与延迟
ping google.com

# 路径追踪
tracert google.com

# 网卡、IP、默认网关
ipconfig
Get-NetIPAddress

# 路由表
route print
Get-NetRoute

# 端口和连接
netstat -ano
Get-NetTCPConnection -State Listen

# 检查 TCP 服务端口
Test-NetConnection google.com -Port 443

# DNS
nslookup google.com
Resolve-DnsName google.com

# 文本查找
Select-String '关键词' C:\path\to\file.txt
```

## 12. 本周应掌握的程度

目前不需要死记所有命令参数。你应该能回答：

1. `ping` 测连通性和 RTT；`tracert/traceroute` 看路径。
2. SSH 是安全远程登录；Telnet 已不适合登录，但可作简单 TCP port 测试。
3. `ipconfig` / `ip addr` 看接口与地址；route table 决定 packet 的下一跳。
4. `ss` / `netstat` 看 sockets、连接与监听端口。
5. `iperf3` 测两主机之间的吞吐量。
6. DNS 工具将域名查成 IP；Windows 优先用 `Resolve-DnsName` 或 `nslookup`。
7. `grep` / `Select-String` 是文本搜索工具。
