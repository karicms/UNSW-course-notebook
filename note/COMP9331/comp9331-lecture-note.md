# COMP9331 Week 1--2：Internet 分层与 Application Layer

> 本笔记整合 `2.Intro_Networks2.pdf`（Networking decomposition / Internet layering）及 `3.Intro_Applications.pdf`（Application Layer: Principles, Web, Email）。术语与范围以课件为准；每节均包含定义、解释和例子。

## Internet：network of networks 与服务基础设施

### 定义

Internet 是由许多互联 networks 构成的 network of networks，也是为 Web、email、streaming、games 等 distributed applications 提供通信服务的基础设施。

### 解释

从 nuts-and-bolts view 看，hosts/end systems 通过 communication links 与 packet switches（routers/switches）相连，data 通常以 packets 传送；从 service view 看，Internet 向 apps 提供可编程的 distributed communication service。不同 access ISPs、regional/global ISPs、content-provider networks 通过 peering/transit 互联，而不是让所有 access ISPs 两两直接连线（后者约需 `O(N^2)` links，无法扩展）。

### 例子

```text
home host -> access ISP -> regional/global ISP -> content-provider network -> server
```

## Protocol（协议）

### 定义

Protocol 是 communicating entities 共同遵循的规则，规定 exchanged messages 的 format、order，以及发送/接收 message 时采取的 actions。

### 解释

协议不仅是 message 的字节布局；它还决定什么时候发送、收到某种 message 后如何响应。Internet 中 HTTP、SMTP、TCP、IP、Wi-Fi/Ethernet 都是协议，分层使每个协议只处理它所属的通信任务。

### 例子

```text
client -- HTTP request --> server
client <-- HTTP response -- server
```

双方都必须理解 request/response 的格式和顺序。

## Network edge、access network 与 physical media

### 定义

Network edge 是运行 applications 的 hosts/end systems；access network 将 end system 接到 first router；physical media 是实际承载 bits 的介质。

### 解释

access methods 包括 DSL、cable、FTTH/fiber、Ethernet、Wi-Fi、cellular。physical media 可为 guided media（twisted-pair copper、coax、fiber）或 unguided media（radio）。access network 解决“host 如何接入 Internet”的问题，不等同于 Internet core。

### 例子

```text
laptop -> Wi-Fi -> home router -> fiber access link -> ISP
```

Wi-Fi radio 是 physical/link 技术；laptop 是 edge host；ISP router 之后进入 core。

## Network core、packet switching 与 store-and-forward

### 定义

Network core 是互连 routers 的集合；packet switching 让 routers 按 packet forwarding；store-and-forward 是 router 必须完整接收 packet 后再开始向下一条 link 发送的行为。

### 解释

router 根据 packet 的 destination/forwarding information 选择 output link。若 packet length 为 `L` bits、link rate 为 `R` bits/s，将整个 packet 推上单一 link 需 `L/R`；多个 hops 的 store-and-forward 会累积相应的 transmission delays（未计 queue/propagation）。

### 例子

`A -> R1 -> R2 -> B` 中，R1 先收完整 packet，才可在 `R1-R2` link 上发送；它不是刚收到第一个 bit 就已经把整个 packet 转发完。

## Circuit switching（电路交换）与 packet switching

### 定义

Circuit switching 在 communication 前预留 end-to-end resources；packet switching 让 users demand-share resources，不预留每个用户的固定份额。

### 解释

circuit switching 可用 frequency/time division multiplexing 分配固定 circuit，idle 时仍可能浪费 capacity；packet switching 特别适合 bursty data、无需 call setup、资源利用率高，但 congestion 会引入 delay/loss。统计复用的概率优势不等于 guarantee：active users 太多时仍会 overload。

### 例子

`1 Mbps` link、每 active user 要 `100 kbps`：circuit switching 最多同时 provision 10 users；packet switching 可服务更多 users，只要很少同时 active。

## Nodal delay：processing、queueing、transmission、propagation

### 定义

Nodal delay 是 packet 在一个 router/node 所经历的总延迟：

$$d_{nodal}=d_{proc}+d_{queue}+d_{trans}+d_{prop}$$

### 解释

`d_proc` 是检查 bit errors、决定 output link 的处理时间；`d_queue` 是等待 output link 的时间，取决于 congestion；`d_trans=L/R` 是将 `L` bits 推进 rate `R` link 的时间；`d_prop=d/s` 是信号跨越距离 `d`、以传播速率 `s` 前进的时间（课件约 `2×10^8 m/s`）。

### 例子

`L=1000 bits`、`R=1000 bits/s` 时 transmission delay 为 `1 s`。这像把所有车从收费站放上路；propagation 则像第一辆车在道路上行驶到下一收费站，取决于距离而非 packet 大小。

## Packet loss、throughput 与 bottleneck

### 定义

Packet loss 是 packet 到达已满 buffer/queue 时被 dropped；throughput 是 sender 到 receiver 实际获得的数据传输速率；bottleneck link 是限制 end-to-end throughput 的最慢/最小容量 link。

### 解释

当 arrival rate 长期高于 output capacity，有限 router buffer 会满，新的 packets loss；是否重传取决于上层 protocol/application。路径中的每段 capacity 不同，端到端数据流不能稳定快于 bottleneck。

### 例子

一条 `100 Mbps` home link 后接 `10 Mbps` path segment 时，长时间 file transfer 的 end-to-end throughput 至多约 `10 Mbps`；若 router queue 满，新 packets 被丢弃。

## Week 1 考试范围提示

### 定义

课件的 scope marker 用于区分需掌握的内容与自主阅读内容。

### 解释

Week 1 原始导论将 Security 与 History 标为 self-study / not on exam；随后分层内容在 `2.Intro_Networks2.pdf` 中实际展开。复习应重点掌握 Internet edge/core、switching、delay/loss/throughput 和本笔记后续的 layering。

### 例子

遇到课件页标 `SELF STUDY` 或 `NOT ON EXAM` 时，仍可理解其概念，但不应与明确考试核心混为一谈。

## Statistical Multiplexing（统计复用）与容量

### 定义

Statistical multiplexing 是 packet switching 中多个用户按实际活动状态共享同一条链路容量的方式；它并不在通信开始前为每个用户预留固定容量。

### 解释

若有 `N` 位独立用户，每位以概率 `p` active、active 时速率为 `R`，则 active 用户数的期望为 `Np`，期望 aggregate traffic 为：

$$E[\text{traffic}] = NpR$$

这解释了 packet switching 为什么能支持比 circuit switching 更多的用户：不是每个人同时发送。但它只是在统计意义上有效；瞬时 active 用户数仍可能超过链路容量。

### 例子

课件的 `N=100`、`p=0.2`、`R=1 Mbps` 例子中，packet-switched network 的期望 aggregate traffic 是 `100 × 0.2 × 1 = 20 Mbps`；若要用 circuit switching **guarantee** 所有用户服务，则必须 provision `100 Mbps`。

## Transient overload、Persistent overload 与 Buffer

### 定义

当输入 packet 的到达速率暂时超过输出 link 的发送能力时，称为 transient overload；持续超过时称为 persistent overload。Buffer（也称 queue）是 router 用来暂存等待发送 packets 的有限空间。

### 解释

Buffer 可以吸收短暂 burst，使 packet 先排队、稍后发送，代价是 queueing delay。若 overload 持续，queue 最终填满；之后到达的 packet 无法存放而被 drop/lost。课件强调 transient overload 并不罕见。

### 例子

```text
packets --> [ buffer / queue ] --> 1 Mbps output link
                   ^
             burst 先排队
```

短 burst 结束后 queue 可排空；若长期输入 `2 Mbps` 而出口始终 `1 Mbps`，有限 buffer 最终满并发生 packet loss。

## Networking decomposition（网络问题分解）

### 定义

Networking decomposition 是将“把数据送到目的地”这个复杂问题拆成任务、组织任务，并决定各组件的职责的设计方法。

### 解释

课件将大问题分为：Application 准备数据；Transport 确保到达 destination process；Network 负责跨网的 global packet delivery；Datalink 把 packet 送至 next hop；Physical 在介质上传送 bits。这种划分形成 layered architecture。

### 例子

浏览器访问网页时，HTTP 产生 message；TCP 在两端 process 间传输；IP 跨 routers 转送；Wi-Fi/Ethernet 把 frame 送到下一跳；radio/copper/fiber 传输 bits。

## Internet protocol stack（五层 Internet 协议栈）

### 定义

Internet protocol stack 是 Internet 使用的五层分层模型：Application、Transport、Network、Link（课件也写 Datalink）、Physical。

### 解释

| 层           | 课件职责                                               | 典型协议/对象                 |
| ----------- | -------------------------------------------------- | ----------------------- |
| Application | supporting network applications                    | HTTP、SMTP、FTP           |
| Transport   | process-to-process data transfer                   | TCP、UDP；segment         |
| Network     | source-to-destination datagram routing             | IP；datagram             |
| Link        | data transfer between neighboring network elements | Ethernet、Wi-Fi；frame    |
| Physical    | bits “on the wire”                                 | copper、fiber、radio；bits |

### 例子

```text
HTTP message -> TCP segment -> IP datagram -> Wi-Fi frame -> bits
```

接收端以相反方向交付给 HTTP application。

## Layering（分层）的服务关系与优点

### 定义

Layering 是将网络组织成相邻层服务关系的架构：每一层依赖下层、支持上层，并对其他层保持相对独立。

### 解释

分层提供 abstraction：上层不必理解下层的实现细节。它让一个 layer 可有 multiple versions，也让新协议或下层技术较容易引入；例如共同的中间 abstraction 可以让 HTTP、SSH、Skype 同时运行在 Ethernet、fiber 或 wireless 上。

### 例子

HTTP 不需要为 Wi-Fi、Ethernet、fiber 分别设计网页协议；它使用 transport 提供的服务，transport 再使用 network 的服务。

## Layering 的代价与限制

### 定义

Layering 的代价是由抽象边界与重复功能带来的效率或信息损失。

### 解释

课件指出两项具体问题：layer `N` 可能 duplicate lower-layer functionality（例如都做 error recovery/retransmission）；information hiding 也可能伤害 performance（例如上层无法区分 packet loss 是 corruption 还是 congestion）。因此分层是可管理性与效率之间的设计取舍，不是自动使 packet 更快。

### 例子

若 link layer 与 transport layer 都试图处理丢失，都可能重传；但 transport 若不知道丢失由拥塞而非损坏造成，难以采取最合适的行为。

## Host 与 Router 实现哪些层

### 定义

Host（end system）和 router 因职责不同，实现的协议栈层数不同。

### 解释

Host 需要产生/接收 application message，因此实现完整五层。Router 只需把收到的 bits 交给 physical、通过 link 处理 next-hop transfer、通过 network 选择/转发 datagram；它通常不执行 end-user application 或 end-to-end transport。

### 例子

```text
Host:    Application / Transport / Network / Link / Physical
Router:                  Network / Link / Physical
```

一封 HTTP message 从 source host 向下包装，经过 routers 的后三层，再在 destination host 向上交付。

## Logical communication（逻辑通信）与 Physical communication（物理通信）

### 定义

Logical communication 是同层 peer 看起来直接交换信息的抽象；physical communication 是数据实际沿栈向下、跨相邻网络节点、再向上移动的过程。

### 解释

Application peer 看似与远端 application 对话，transport peer 看似与远端 transport 对话；实际没有 HTTP 或 TCP message 直接跨越 Internet。真实发送只能借由 physical network 在相邻节点之间逐跳传 bits。

### 例子

```text
logical:  HTTP(client) <--------------------> HTTP(server)
physical: client -> router -> router -> server （逐跳 bits/frame）
```

## Encapsulation 与 Decapsulation

### 定义

Encapsulation 是 source host 的每层在上层 data 前添加本层 header（及必要控制信息）；decapsulation 是 receiver 逐层检查/移除对应 header 并把 payload 上交。

### 解释

课件的对象依次为 `message`、`segment`、`datagram`、`frame`。各层只需理解自己的 header 和要向上交付的 payload；因此可在不改变 HTTP message 内容的前提下使其穿过不同 link technologies。

### 例子

```text
source: M -> [Ht|M] -> [Hn|Ht|M] -> [Hl|Hn|Ht|M]
receiver: frame -> datagram -> segment -> message M
```

其中 `Ht`、`Hn`、`Hl` 分别代表 transport、network、link header。

## Network applications 的端系统实现

### 定义

Network application 是在不同 end systems 上运行、彼此通过 network 通信的程序；application-layer software 不运行在 network-core routers 中。

### 解释

创建 application 时，开发者编写运行在 clients、servers 或 peers 上的程序；它们调用 transport service，而不负责实现 router 的 forwarding/routing。常见 app 包括 Web、email、text messaging、VoIP、video conference、search、remote login 和 network games。

### 例子

浏览器与 Web server 是两台 end systems 上的程序；它们通过 Internet 交换 HTTP messages，途中 routers 只转发下层 packets。

## Client-server architecture

### 定义

Client-server architecture 是由 client 向 server 发起通信的应用架构。

### 解释

课件中 server 是 always-on host，具有 permanent IP address，常位于 data centers 以便 scaling；client 是 initiates communication 的 process。一个 server 可同时服务许多 clients。

### 例子

浏览器（client）连到 `www.example.com` 的 Web server；浏览器请求对象，server 返回 response。

## Peer-to-peer（P2P）architecture

### 定义

P2P architecture 是 arbitrary end systems 直接通信的架构，没有 always-on server。

### 解释

Peers 可请求和提供服务，规模可随 peers 加入而扩大；与集中 server 架构不同，参与节点可能间歇在线、IP 也可能改变。

### 例子

多个 peers 可直接彼此传递文件 chunks，而不是都从一个固定 server 下载。

**考试范围：课件明确标注 `SELF STUDY`、`NOT ON EXAM`。**

## Process（进程）与通信方向

### 定义

Process 是在 host 内运行的 program。不同 hosts 上的 processes 通过 exchange messages 通信。

### 解释

同一 host 的两个 processes 可按 operating system 机制通信；网络语境关注不同 hosts 的 processes。Client process initiates communication；server process waits to be contacted。

### 例子

浏览器 process 启动连接并发送 request，因此是 client process；Web-server process 接收请求并回复，因而是 server process。

## Socket（套接字）

### 定义

Socket 是 process 与 transport infrastructure 之间发送/接收 messages 的接口，课件类比为 door。

### 解释

Sending process 将 message “shoves out the door”，依赖门另一侧的 transport infrastructure 将其交付至 receiving process 的 socket。一次网络通信涉及两端各一个 socket。

### 例子

```text
browser process -> client socket == Internet/TCP == server socket -> Web-server process
```

## IP address 与 Port number 的 process addressing

### 定义

要接收 message，process 必须有 identifier；该 identifier 包含 host 的 IP address 和该 host 上 process 的 port number。

### 解释

课件将 host device 的 IPv4 address 描述为 unique 32-bit IP address。IP 先定位 host，但一台 host 可运行许多 processes；port 再定位其中的服务/process。

### 例子

HTTP server 的 well-known port 是 `80`。对 `203.0.113.8:80` 的通信中，IP 指向 host，`:80` 指向该 host 的 HTTP server process。

## Application-layer protocol（应用层协议）

### 定义

Application-layer protocol 定义 communicating processes 如何交换 application messages。

### 解释

它规定：message types（如 request/response）、message syntax（字段及其 delimiter/表示）、message semantics（字段含义），以及 process 何时、如何发送或响应。Open protocols 通常在 RFC 中定义，任何人都可实现；也存在 proprietary protocols。

### 例子

HTTP 定义 client 的 request 与 server 的 response；`GET /index.html HTTP/1.1` 是符合其 request syntax 的例子。

## Transport service requirements

### 定义

Transport service requirements 是 application 对下层 transport 所需性质的集合：data integrity、throughput、timing 和 security。

### 解释

file transfer、Web transactions、email 往往要求 100% reliable transfer，许多 elastic apps 没有最低 throughput 要求；multimedia 常需要最低 throughput 才有效。real-time audio/video、interactive games 通常 time sensitive，可容忍某些 loss；不同 apps 对 confidentiality/integrity 的需求也不同。

### 例子

下载可为完整文件多等一会儿但不能少一个 byte；视频通话宁愿跳过过期 audio packet，也不能让它迟到很久才播放。

## TCP service

### 定义

TCP 是 Internet transport protocol，提供 sending process 到 receiving process 的 reliable transport。

### 解释

TCP 提供 reliable delivery、flow control（sender 不压垮 receiver）、congestion control 和 connection-oriented service；但课件强调它不提供 timing 或 minimum throughput guarantee。

### 例子

FTP、SMTP、HTTP/1.1（课件表中）均使用 TCP；若传输中的 data 丢失，TCP 的可靠性机制负责使 application 获得正确数据流。

## UDP service

### 定义

UDP 是 Internet transport protocol，提供 processes 间 unreliable data transfer。

### 解释

UDP 不提供 reliability、flow control、congestion control、timing guarantee、throughput guarantee、security 或 connection setup。选择 UDP 不等于网络绝不丢包，而是应用/协议不从 UDP 获得这些 TCP 式保证。

### 例子

课件表中 Internet telephony 的 SIP/RTP 可使用 TCP 或 UDP；对于实时媒体，应用可能优先低延迟而容忍部分 loss。

## TLS 与安全 TCP

### 定义

TLS（Transport Layer Security）是 application layer 实现的安全库/协议，应用可通过 TLS 再使用 TCP。

### 解释

Vanilla TCP 和 UDP sockets 没有 encryption；cleartext password 放入普通 socket 会以明文穿越 Internet。TLS 可提供 encryption、data integrity 与 end-point authentication（课件称 TLS socket API 是 TCP socket API 的 enhancement）。

### 例子

```text
application -> TLS library -> TCP -> Internet
```

HTTPS 使用这一思路：HTTP messages 通过 TLS-encrypted connection 发送，而非裸 HTTP 明文。

## World Wide Web、Web page 与 Object

### 定义

WWW 是由 Hypertext Transfer Protocol（HTTP）链接的 distributed database of “pages”；Web page 由一个 base HTML file 和多个 objects 组成。

### 解释

objects 可为 HTML、JPEG、audio 等，且可存于不同 Web servers。base HTML file 会引用 embedded objects；每个 object 可以由 URL addressing。课件记载 first HTTP implementation 是 Tim Berners-Lee 于 CERN 在 1990 年完成。

### 例子

一个新闻页的 base HTML 引用 logo、图片、CSS/脚本等 objects；浏览器需分别取回它们来显示页面。

## URL（Uniform Resource Locator）

### 定义

URL 是用于定位 Web resource 的统一资源定位形式：

```text
protocol://host-name[:port]/directory-path/resource
```

### 解释

`protocol` 可为 `http`、`https`、`ftp`、`smtp` 等；hostname 可是 DNS name 或 IP address；port 未写时使用协议 standard port，例如 HTTP `80`、HTTPS `443`。

### 例子

`https://www.example.com:443/news/a.html` 指定 HTTPS、host、显式 port 与 resource path。

## HTTP 的 client-server、TCP 与 stateless 特性

### 定义

HTTP（HyperText Transfer Protocol）是 Web 的 application-layer protocol，采用 client/server model 且是 stateless。

### 解释

client 对 server port `80` 建立 TCP connection/socket；server accept connection 后交换 HTTP messages。Stateless 指 server 不维护关于过去 client requests 的信息，故多步交互的 state 需另行处理。

### 例子

浏览器的 HTTP GET 得到 response 后，单独看下一次 GET，HTTP server 不会仅凭协议本身记得它先前做过什么。

## HTTP request message 与 methods

### 定义

HTTP request 是 client-to-server message；general format 由 request line、零或多个 header lines、空行和可选 entity body 组成。

### 解释

request line 是 `method SP URL SP version CRLF`，常见 request messages 用 ASCII、以 `CRLF` 分行。`GET` 取回 object；`POST` 将 form input 放在 entity body；`HEAD` 只请求若以 GET 请求会返回的 headers；`PUT` 将 entity body 中 object 上传至 URL 指定路径。

### 例子

```http
GET /index.html HTTP/1.1
Host: example.com
```

## HTTP response、status line 与 status codes

### 定义

HTTP response 是 server-to-client message；开头为 status line，后接 header lines、空行与 object data/body。

### 解释

status line 包含 protocol version、status code 和 status phrase。课件示例包括：`200 OK`（请求成功，object 在后续 message）、`301 Moved Permanently`（object 有新 URL）、`400 Bad Request`、`404 Not Found`、`505 HTTP Version Not Supported`。

### 例子

```http
HTTP/1.1 200 OK
Content-Type: text/html

<html>...</html>
```

## HTTP text format 与 message delimiting

### 定义

课件将 HTTP 描述为 all text 的协议：headers/lines 使用 text 和 `\r\n` delimiter。

### 解释

这使 messages 较易 delineate、相对 human-readable，也避免字段编码/格式化的一些复杂性；同时 HTTP 可传输 variable-length data，body 可承载非文本 object。

### 例子

request header 的 `Host: example.com\r\n` 以 CRLF 结束；header 与 entity body 之间的空行标出 header 结束。

## Cookies 与 Web state

### 定义

Cookie 是 Web site 与 browser 用来在多次 stateless HTTP transactions 间维护 state 的机制。

### 解释

课件列出四组件：HTTP response 中的 `Set-cookie` header、后续 HTTP request 中的 `Cookie` header、用户 host 的 cookie file（browser 管理）、Web site 的 back-end database。server 创建 ID，browser 保存并在后续请求带回。

### 例子

首次访问商店时 server 返回 `Set-cookie: 8734` 并在 database 记录 ID；以后浏览器请求带 `Cookie: 8734`，站点据此恢复 shopping cart 或 authorization state。

## Cookies 的用途与隐私问题

### 定义

Cookie 既可支撑 personalization/state，也可能成为跨站追踪的标识符。

### 解释

课件列举 authorization、shopping carts、recommendations、user session state（Web e-mail）等用途；同时指出 cookies 使 sites 了解许多用户信息，third-party cookies（如 ad network）可跨不同网站跟踪用户。

### 例子

网站 A 与 B 都嵌入同一广告网络资源时，该广告服务器可读取/设置自己的 cookie ID，并将两个站点访问关联起来。

## Page Load Time（PLT）与 HTTP 性能目标

### 定义

PLT 是从用户 click 或输入 URL 到用户看到页面的时间，是 Web performance 的重要 metric。

### 解释

用户希望 fast downloads/high availability；content provider 也希望快、可靠且成本合理。性能受 content size、HTTP connection/requests、network bandwidth/RTT、server load 等影响。课件的两类核心思路是减少传输 content size（smaller images、compression）和更好利用 available bandwidth。

### 例子

同一页面把超大图片压缩后，object transmission time 降低，PLT 通常更短。

## RTT、Non-persistent HTTP 与 HTTP/1.0

### 定义

RTT（round-trip time）是 small packet 从 client 到 server 再返回的时间。Non-persistent HTTP 为每个 Web resource 使用一条 TCP connection，是 HTTP/1.0 的模式。

### 解释

naively sequential fetch 时，base page 和每个 embedded object 均需建立 connection；单个 object 从 TCP setup 到 request/first response 需要约 `2 RTT` 加 transmission time，因此 multi-object PLT 较差。

### 例子

base file 大小 `S0`、`N` 个 inline objects 各 `S`，link capacity `C`、RTT `D`，不并行的 non-persistent HTTP 下载时间：

$$2(N+1)D + \frac{S_0+NS}{C}$$

## Parallel HTTP connections

### 定义

Parallel HTTP connections 是 client 同时开多条 TCP connections 来并发请求多个 objects 的方式。

### 解释

并行可缩短多个 embedded objects 的等待，但 responses 不一定保持 object 的原始顺序；过多 parallel connections 会增加 server/client/网络负担，并竞争共享带宽。

### 例子

浏览器已收到 HTML 后，可同时对三张图片发起三条 connections，而不是完成图片 1 后才请求图片 2。

## Persistent HTTP、pipelining 与 HTTP/1.1

### 定义

Persistent HTTP（HTTP/1.1）在 response 后让 TCP connection 保持 open，并复用于同一 client/server 的后续 HTTP messages；pipelining 允许 client 不等上一个 response 就连续发 requests。

### 解释

没有 pipelining 时，client 等到前一个 response 才发新 request，仍是每个 referenced object 一个 RTT；有 pipelining 时，base HTML 收到后可背靠背请求全部 referenced objects，从而减少 RTT 开销。课件给出 persistent with pipelining（无并行）的时间：

$$2D + \frac{S_0+NS}{C}$$

### 例子

一条已建立的 TCP connection 上，browser 可发送 `GET /a`、`GET /b`、`GET /c`，而不是为每个 object 重新 TCP setup。

## HTTP/2、head-of-line blocking 与 frames

### 定义

HTTP/2 的关键目标是减少 multi-object HTTP requests 的 delay；它将 objects 切为 frames 并可 interleave frame transmission。

### 解释

HTTP/1.1 pipelining 下 server 以 FCFS/in-order 发送，large object 可使 small object 被阻塞（head-of-line blocking）。HTTP/2 的 methods、status codes 和多数 headers 维持 HTTP/1.1 语义，但 server 能按 client-specified priority 调度 interleaved frames。

### 例子

当大视频 object 与三个小图标同时被请求，HTTP/2 可在视频 frames 之间发送小图标 frames，使小对象更早显示。

## HTTP/3

### 定义

HTTP/3 是 2022 年标准化（RFC 9114）的 HTTP 版本，目标是在 HTTP/2 之外继续降低 delay 与 PLT。

### 解释

课件强调 HTTP/3 运行于新的、比 TCP 更适合的 transport 之上，可避免 TCP connection-setup handshake 带来的一些启动等待，并改善 multi-object transfer 的 delay；HTTP/2/3 的应用采用率是课件的行业背景，不是协议语义本身。

### 例子

用户访问高 RTT 的页面时，HTTP/3 旨在让数据更早开始流动并减少部分队头阻塞影响。

## Web cache（proxy server）

### 定义

Web cache/proxy server 的目标是在不涉及 origin server 的情况下满足 client request；它在体系中同时 acting as server（对 client）和 client（对 origin）。

### 解释

browser 可配置为将 HTTP request 发给 cache：hit 时 cache 直接 response；miss 时 cache 向 origin server 请求、获得 response 后再交付/保存。它利用 locality of reference，可降低 client response time、减少 institution access link traffic，并降低 origin load。

### 例子

```text
browser -> proxy cache -> origin server
             | hit
             +--> cached response directly to browser
```

## Cache hit rate 与 caching performance

### 定义

Cache hit rate 是由 cache 直接满足的请求比例；miss rate 为 `1 - hit rate`。

### 解释

在课件 caching example 中，access link `1.54 Mbps`、Internet RTT `2 s`、object `100 Kbits`。增加更快 access link 可减低 utilization/delay，但安装 cache 常以较低成本消除部分外部请求。若 hit rate 为 `0.4`，40% requests 在 cache 满足，只有 60% 使用 access link/origin path。

### 例子

若 cache-hit 的 local delay 为 `0.01 s`、miss path delay 约 `2.01 s`，average delay 为：

$$0.4(0.01) + 0.6(2.01) \approx 1.21\text{ s}$$

## Conditional GET、If-Modified-Since 与 ETag

### 定义

Conditional GET 是 client/cache 仅在 cached object 不是最新时才请求完整 object 的 HTTP 机制。

### 解释

client 在 request 加 `If-Modified-Since: <date>`；若 origin object 自该日期未修改，server 回 `304 Not Modified` 而不发送 object。课件也展示 ETag：通常是 content 的 cryptographic hash，server 可借它识别版本（尤其 dynamic content）。

### 例子

```http
GET /logo.png HTTP/1.1
If-Modified-Since: Tue, 30 Oct 2007 17:00:00 GMT
```

未修改时 server 回 `HTTP/1.1 304 Not Modified`，节省 object transfer。

## Replication 与 CDN

### 定义

Replication 是将 popular Web site/content 复制到多台机器；CDN（Content Distribution Network）将 caching and replication 作为大规模 distributed storage service。

### 解释

复制可 spread load、把 content 放到更靠近 clients 的位置，并帮助不可缓存 content。难点是 replicas consistency；CDN 通常由一个 entity 运营，课件以 Akamai 的大量 locations 为例。

### 例子

澳洲用户可由邻近 CDN server 获得热门视频，而不必每次从远端 origin server 跨洲取回。

## HTTPS

### 定义

HTTPS 是运行在 TLS-encrypted connection 上的 HTTP。

### 解释

普通 HTTP 不安全；HTTP Basic Authentication 只是 base64 encoding，能轻易还原为 plaintext，不能视为 encryption。HTTPS 将 HTTP 放到 TLS 的 encryption/integrity/authentication 保护之下。

### 例子

`https://...` 默认 port `443`；浏览器与 server 先建立安全 TLS connection，再在其中传 HTTP request/response。

## E-mail system 的组件

### 定义

Internet e-mail system 的三个主要组件是 user agents（UA）、mail servers 与 SMTP。

### 解释

UA 让用户 compose/read messages；mail server 有 user mailbox（存放到达用户的 messages）与 outgoing message queue（等待发送 messages）；SMTP 在 mail servers 间 transfer messages。mail server 通常 always-on。

### 例子

Alice 在邮件客户端写信，客户端将信交 Alice 的 mail server queue；该服务器再用 SMTP 转交 Bob 的 mail server，Bob 从 mailbox 读取。

## SMTP（Simple Mail Transfer Protocol）

### 定义

SMTP 是在 mail servers 之间 exchange e-mail messages 的 application-layer protocol，定义于 RFC 5321。

### 解释

SMTP 以 TCP reliable transfer，server port `25`；sending mail server 扮演 TCP client，直接连接 receiving mail server。一次传送有三个 phases：handshaking/greeting、message transfer、closure。它使用 persistent connections，并要求整个 message（header 和 body）均为 7-bit ASCII（非 ASCII/attachments 需 MIME encoding）。

### 例子

```text
S: 220 hamburger.edu
C: HELO crepes.fr
C: MAIL FROM:<alice@crepes.fr>
C: RCPT TO:<bob@hamburger.edu>
C: DATA
```

## E-mail delivery scenario

### 定义

E-mail delivery 是 UA、sender mail server、receiver mail server 和 receiver UA 协作的 store-and-forward process。

### 解释

课件的 Alice-to-Bob sequence：Alice UA compose；UA 将 message 放入 Alice mail server queue；sending SMTP client 打开到 Bob mail server 的 TCP connection；SMTP 发送 message；Bob server 将其放入 Bob mailbox；Bob 调用 UA 读取。

### 例子

```text
Alice UA -> Alice mail server (queue) --SMTP/TCP:25--> Bob mail server (mailbox) -> Bob UA
```

## SMTP 与 HTTP 的比较

### 定义

SMTP 与 HTTP 都是 application-layer text protocols，但其请求方向与消息处理方式不同。

### 解释

HTTP 是 pull：client 请求 object；SMTP 是 push：sending server 推送 message。SMTP 使用 persistent connection，并要求 message header/body 按其格式准备；HTTP 对 object retrieval 使用 request/response。

### 例子

浏览器只有在请求 `GET /x` 时才从 Web server 拉取 `x`；Alice 的 mail server 则主动连接 Bob 的 mail server 推送 email。

## E-mail message format：RFC 822

### 定义

RFC 822 定义 e-mail message 自身的 syntax；RFC 5321 定义 SMTP 交换该 message 的方式，关系类似 HTML 内容与 HTTP transfer protocol。

### 解释

message 由 header lines 与 body 构成，header 例如 `To:`、`From:`、`Subject:`；header/body 之间以 blank line 分隔。这和 SMTP envelope commands（如 `MAIL FROM`、`RCPT TO`）是不同层面的信息。

### 例子

```text
To: bob@someschool.edu
From: alice@crepes.fr
Subject: hello

See you tomorrow.
```

## Mail access protocols：POP 与 IMAP

### 定义

POP/IMAP 是 receiver 从 mail server mailbox 访问 e-mail 的 protocols；SMTP 主要负责 server-to-server push transfer。

### 解释

课件指出 POP3 是 authorization 与 download；IMAP 保留 messages 在 server，并让 user agent 能组织/管理 folders。Web-based mail 也可让 browser/UA 通过 HTTP 与 mail server 交互。它们解决“邮件已抵达 receiver mail server 后，用户如何读到”的问题。

### 例子

手机与笔电使用 IMAP 访问同一 mailbox 时，server 保存邮件/文件夹状态；SMTP 则仍用于发送邮件的 server-to-server transfer。

**考试范围：课件明确在此页标注 `Not on exam`。**

## 本份 Application Layer PPT 的范围边界

### 定义

课程 outline 列出 DNS、P2P、video streaming/CDNs、socket programming 等章节，但本次 87 页课件的实际讲授内容以 Principles、Web/HTTP 与 E-mail 结束。

### 解释

DNS 出现在 outline/后续章节提示中，未在此 PDF 的后续页面实际展开；P2P 被明确 self-study/not-on-exam。本笔记因此不把未在这份 PPT 展开的 DNS/streaming/socket-programming 细节混入，以保持与该课件范围一致。

### 例子

第 87 页 summary 列出 `Principles of Network Applications`、`HTTP`、`E-mail`，并标示下一主题，而不是给出 DNS protocol 的记录、层次或解析流程。

# COMP9331 Week 3 - Application Layer Part 2

> 主要依据：`4.Application_Part2.pdf`（83 页 PPT）。  
> 主题：DNS、P2P（Self Study / NOT ON EXAM）、Video Streaming and CDN、MCP、RPC/JSON-RPC、Socket Programming with UDP/TCP。  
> 说明：以下内容按 PPT 原有结构整理；少量为帮助理解的内容会明确标注为“补充理解”。

## 2. Application Layer: outline

### 知识点：本讲覆盖范围

**定义：**  
本 PPT 属于 Application Layer 的后半部分，覆盖 DNS、P2P、视频流与 CDN、MCP，以及 UDP/TCP socket programming。

**解释：**  
课程结构来自 PPT 第 2 页：  

- 2.4 DNS  
- 2.5 P2P applications（self study）  
- 2.6 video streaming and content distribution networks（CDNs）  
- 2.7 Model Context Protocol（MCP）  
- 2.8 socket programming with UDP and TCP

**使用场景：**  
这些内容分别对应应用层中的名字解析、内容分发、AI 工具协议通信，以及自己编写网络 client/server 程序。

**使用方法 / 工作流程：**  
学习时可以按照“应用需要什么服务 -> 底层如何通信 -> 协议如何组织消息 -> 实际编程如何使用 socket”的顺序理解。

**例子：**  
浏览器访问 `www.unsw.edu.au` 时，先通过 DNS 找 IP；看视频时可能从 CDN 服务器取分片；AI host 调工具时可能通过 MCP/JSON-RPC 通信；自己写 UDP PingClient 时通过 socket 向服务器端口发数据。

## 2.4 DNS - Domain Name System

### 知识点：DNS 的基本问题

**定义：**  
DNS（Domain Name System）是用于在 hostname 和 IP address 之间做映射的分布式系统。

**解释：**  
人类更容易记住名字，例如 `cs.umass.edu`；Internet 中 host/router 实际转发 datagram 时使用 IP address，例如 32-bit IPv4 地址。DNS 解决的问题是：

```text
hostname <-> IP address
```

PPT 强调：DNS 是 Internet 的核心功能，但实现为 application-layer protocol，复杂性放在网络边缘。

**使用场景：**  
当用户输入 URL、邮件程序寻找邮件服务器、程序调用 `gethostbyname()` 或类似 API 时，都需要 DNS。

**使用方法 / 工作流程：**  
应用拿到 hostname 后，向 local DNS server 发起 DNS request；local DNS server 可能从 cache 返回，也可能继续询问 DNS hierarchy 中的 root、TLD、authoritative name server。

**例子：**  
用户访问 `gaia.cs.umass.edu`，主机需要先解析这个 hostname 对应的 IP address，之后才能建立 TCP 连接或发送 packet。

### 知识点：DNS 历史

**定义：**  
DNS 出现前，host-address mapping 曾经集中放在一个 `hosts.txt` 文件中。

**解释：**  
早期 `hosts.txt` 由 Stanford Research Institute（SRI）维护；变更通过 email 提交，新版本通过 FTP 分发。Internet 变大后，这种方式无法扩展：SRI 负载过高、名字不唯一、不同主机可能拿到过期文件。

**使用场景：**  
理解 DNS 为什么必须分布式、分层，而不能靠一个中心文件维护所有名字。

**使用方法 / 工作流程：**  
DNS 用分布式数据库和分层管理替代单一 `hosts.txt`。

**例子：**  
如果全世界所有网站名字都要发邮件给一个机构更新，再让所有主机 FTP 下载新表，新增网站和更新 IP 都会非常慢，也容易冲突。

### 知识点：DNS services

**定义：**  
DNS 提供 hostname-to-IP translation、host aliasing、mail server aliasing、load distribution 等服务。

**解释：**  
PPT 列出的 DNS services 包括：

- hostname to IP address translation
- host aliasing：canonical name 和 alias name
- mail server aliasing
- load distribution：多个 IP address 对应一个 name，常用于 replicated Web servers

**使用场景：**  
网站访问、邮件投递、负载均衡和服务别名都依赖 DNS。

**使用方法 / 工作流程：**  
客户端查询某个 name 的 DNS record；DNS server 根据 record type 返回 IP、canonical name、mail server 或 authoritative server 信息。

**例子：**  
`www.ibm.com` 可以是一个 alias，真实 canonical name 可能是类似 `servereast.backup2.ibm.com` 的名字。

### 知识点：为什么不能 centralize DNS

**定义：**  
Centralized DNS 指用一个中心数据库或少数中心服务器完成所有 DNS 解析。

**解释：**  
PPT 给出的原因是：  

- single point of failure  
- traffic volume 太大  
- distant centralized database 导致远距离访问  
- maintenance 困难  
- 不具备 scalability

PPT 例子：Comcast DNS servers alone 每天约 600B DNS queries。

**使用场景：**  
解释 DNS 设计为什么必须分布式、层次化。

**使用方法 / 工作流程：**  
DNS 通过 hierarchy、caching、delegation、replication 来避免中心化瓶颈。

**例子：**  
如果全球所有浏览器访问网站前都要问同一台 DNS server，那么该 server 故障时 Internet 大量应用会无法解析名字。

### 知识点：DNS 设计目标

**定义：**  
DNS 的设计目标是保证命名唯一、可扩展、分布式自治管理、高可用、快速查询。

**解释：**  
PPT 列出目标：

- No naming conflicts（uniqueness）
- Scalable：many names；frequent updates
- Distributed, autonomous administration：能更新自己 domain 的名字，不必跟踪所有人的更新
- Highly available
- Lookups should be fast

**使用场景：**  
用于理解 DNS 的 namespace、administration 和 server hierarchy。

**使用方法 / 工作流程：**  
每个 domain 对自己的子树负责；不同层级的 DNS server 管理不同范围；cache 提升速度。

**例子：**  
UNSW 可以管理 `unsw.edu.au` 下的名字，不需要知道全世界其他学校如何更新自己的 hostname。

### 知识点：DNS 的三种 hierarchy

**定义：**  
DNS 的 key idea 是 hierarchy，包含 hierarchical namespace、hierarchically administered、distributed hierarchy of servers。

**解释：**  
PPT 强调 DNS 有三种互相关联的层次结构：

- Hierarchical namespace：区别于早期 flat namespace
- Hierarchically administered：区别于 centralised administration
- Distributed hierarchy of servers：区别于 centralised storage

**使用场景：**  
用于避免命名冲突、分散管理压力、让 DNS 数据分布存储。

**使用方法 / 工作流程：**  
root 在最上层；下面是 TLD，例如 `.edu`、`.com`、`.au`；再下面是具体 domain 和 subdomain。

**例子：**  
`instr.eecs.berkeley.edu.` 是从 leaf 到 root 的路径；`.edu.` 是 TLD，`berkeley.edu.` 是 domain，`eecs.berkeley.edu.` 是 subdomain。

### 知识点：Hierarchical namespace

**定义：**  
Hierarchical namespace 是树状命名空间，名字由 leaf-to-root path 形成。

**解释：**  
PPT 中 root 在最上方，下面有 `edu`、`com`、`gov`、`mil`、`org`、`net`、`uk`、`fr` 等。Top Level Domains 位于顶部；domains 是 sub-trees；tree depth arbitrary，但限制为 128。

**使用场景：**  
用于组织 Internet 名字，避免不同组织之间直接冲突。

**使用方法 / 工作流程：**  
每个 domain 对自己子树中的名字负责，因此 name collision 容易避免。

**例子：**  
`instr.eecs.berkeley.edu.` 和其他大学的 `instr` 不冲突，因为它们位于不同 domain 子树下。

### 知识点：Hierarchical administration 和 Zone

**定义：**  
Zone 是 DNS namespace 中由某个 administrative authority 管理的一段连续区域。

**解释：**  
PPT 例子：UCB controls `*.berkeley.edu` 和 `*.sims.berkeley.edu`；EECS controls `*.eecs.berkeley.edu`。Authoritative NS 对自己负责的 zone 提供权威答案。

**使用场景：**  
用于让不同组织或部门自治管理自己的 DNS 名字。

**使用方法 / 工作流程：**  
上级 domain 可以把某个子域的管理委派给下级 authoritative name server。

**例子：**  
Berkeley 可以把 `eecs.berkeley.edu` 的名字管理交给 EECS 的 authoritative DNS server。

### 知识点：DNS server hierarchy

**定义：**  
DNS server hierarchy 包含 root servers、TLD servers 和 authoritative DNS servers。

**解释：**  
PPT 列出：

- Root servers：位于 hierarchy 顶部，位置 hardwired into other servers
- TLD servers：负责 `.com`、`.edu` 等顶级域
- Authoritative DNS servers：保存 name-to-address mapping，由对应 administrative authority 维护

每个 server 只保存 total DNS database 的一个小 subset。Authoritative DNS server 为自己有 authority 的 DNS names 保存 resource records。

**使用场景：**  
用于逐级找到负责目标 hostname 的 authoritative server。

**使用方法 / 工作流程：**  
每个 server 能发现负责其他 hierarchy 部分的 server；每个 server 知道 root server，root server 知道所有 TLD。

**例子：**  
解析 `robot.cs.washington.edu.` 时，local DNS server 可以沿 root -> `.edu` TLD -> `washington.edu` / `cs.washington.edu` authoritative server 逐步查询。

### 知识点：Root name servers

**定义：**  
Root name servers 是 DNS 中的 official contact-of-last-resort，当 name server 无法解析名字时会向 root 查询。

**解释：**  
PPT 强调 root servers 是极其重要的 Internet function；Internet 无法没有 root servers。全球有 13 个 logical root name servers，每个 server replicated many times。ICANN 管理 root DNS domain。DNSSEC 提供 security，包括 authentication 和 message integrity。

**使用场景：**  
当 local DNS server 不知道某个 hostname 如何解析时，常从 root server 开始。

**使用方法 / 工作流程：**  
Root server 通常不会直接给最终 IP，而是告诉查询者应该问哪个 TLD server。

**例子：**  
local DNS server 不知道 `www.pollev.com`，可以先问 root，root 指向 `.com` TLD server。

### 知识点：TLD servers 和 Authoritative DNS servers

**定义：**  
TLD servers 负责 top-level domains；Authoritative DNS servers 负责组织自己的 hostname-to-IP mappings。

**解释：**  
PPT 中 TLD 包括 `.com`、`.org`、`.net`、`.edu`、`.aero`、`.jobs`、`.museums`，以及 country domains，例如 `.cn`、`.uk`、`.fr`、`.ca`、`.jp`。Network Solutions 是 `.com`、`.net` 的 authoritative registry；Educause 是 `.edu` TLD。

**使用场景：**  
TLD server 帮助找到某 domain 的 authoritative DNS server；authoritative DNS server 给出最终权威记录。

**使用方法 / 工作流程：**  
查询者从 root 得到 TLD server；从 TLD server 得到 authoritative DNS server；再从 authoritative DNS server 得到目标 record。

**例子：**  
查询 `www.networkutopia.com` 时，`.com` TLD server 可返回 `networkutopia.com` 的 authoritative DNS server。

### 知识点：Local DNS name server

**定义：**  
Local DNS server 是每个 ISP、公司或大学通常提供的 default name server，但它不严格属于 DNS hierarchy。

**解释：**  
Host 通过 host configuration protocol（例如 DHCP）学习 local DNS server 地址。Local DNS server 有 recent name-to-address translation pairs 的 local cache，但 cache 可能过期。

**使用场景：**  
普通主机发 DNS query 时通常先发给 local DNS server。

**使用方法 / 工作流程：**  
应用获得 hostname 后调用 `gethostbyname()` 触发 DNS request 到 local DNS server；local DNS server 作为 proxy，可能返回 cache，也可能 forward query into hierarchy。

**例子：**  
UNSW 校园网中的主机访问 `gaia.cs.umass.edu`，先问 UNSW 或 ISP 提供的 local DNS server。

### 知识点：Iterated query

**定义：**  
Iterated query 是被联系的 DNS server 不一定完成全部解析，而是回复“我不知道最终答案，但你可以去问这个 server”。

**解释：**  
PPT 原话：contacted server replies with name of server to contact；“I don’t know this name, but ask this server”。

**使用场景：**  
常用于 local DNS server 向 root、TLD、authoritative server 逐级查询。

**使用方法 / 工作流程：**  
以 `engineering.nyu.edu` 查询 `gaia.cs.umass.edu` 为例：

1. requesting host 问 local DNS server `dns.nyu.edu`
2. local DNS server 问 root DNS server
3. root 返回 TLD DNS server
4. local DNS server 问 TLD DNS server
5. TLD 返回 authoritative DNS server
6. local DNS server 问 `dns.cs.umass.edu`
7. authoritative server 返回 `gaia.cs.umass.edu` 的 IP
8. local DNS server 把答案给 requesting host

**例子：**  
local DNS server 每一步都自己继续问下一个 server，而不是要求 root 替它问到底。

### 知识点：Recursive query

**定义：**  
Recursive query 是被联系的 DNS server 负责继续解析，并最终返回答案。

**解释：**  
PPT 强调 recursive query puts burden of name resolution on contacted name server。这样可能让 hierarchy 上层 server 承担 heavy load。

**使用场景：**  
主机向 local DNS server 查询时通常希望 local DNS server 替自己完成解析。

**使用方法 / 工作流程：**  
请求者只问一个 server；该 server 再去问其他 server，并把最终结果返回。

**例子：**  
主机问 `dns.nyu.edu`：`gaia.cs.umass.edu` 的 IP 是什么？`dns.nyu.edu` 负责继续问 root、TLD、authoritative server，最后把 IP 返回给主机。

### 知识点：DNS caching 与 TTL

**定义：**  
DNS caching 是 name server 学到 mapping 后将其暂存；TTL（Time To Live）控制 cache entry 多久后过期。

**解释：**  
PPT 指出：任何 name server 学到 mapping 后都会 cache；cache entries timeout after TTL。TLD servers typically cached in local name servers，所以 root name servers 不会经常被访问。

Cached entries 可能过期，因此 DNS 是 best-effort name-to-address translation。如果 host 改 IP，可能要等所有 TTL 过期后才 Internet-wide 可见。

**使用场景：**  
用于减少 DNS latency、降低 root/TLD server 负载。

**使用方法 / 工作流程：**  
DNS server 查询到 RR 后缓存，之后 TTL 未过期时可直接回答；TTL 到期后再重新查询。

**例子：**  
local DNS server 解析过 `.com` TLD server 后，会缓存该结果，因此下一次解析其他 `.com` 域名时可能不用再问 root。

### 知识点：DNS updating 和 Negative caching

**定义：**  
DNS updating 是修改 DNS records 的过程；Negative caching 是缓存“不存在/失败”的查询结果。

**解释：**  
PPT 提到 IETF standard RFC 2136 提供 update/notify mechanisms。Negative caching 可记住无效查询，例如 `www.cnn.comm` 和 `www.cnnn.com`。

**使用场景：**  
更新域名 IP、减少重复拼写错误查询造成的负载。

**使用方法 / 工作流程：**  
修改 record 前需要考虑旧记录可能在其他 DNS server 中缓存直到 TTL 过期。

**例子：**  
用户打错 `www.cnn.comm`，local DNS server 可短时间记住这个错误结果，避免每次都向上级 DNS 查询。

### 知识点：DNS resource records

**定义：**  
DNS 是存储 resource records（RR）的 distributed database。RR format：

```text
(name, value, type, ttl)
```

**解释：**  
PPT 列出的 record type：

- `A`：`name` 是 hostname，`value` 是 IP address
- `NS`：`name` 是 domain，`value` 是该 domain 的 authoritative name server hostname
- `CNAME`：`name` 是 alias name，`value` 是 canonical name
- `MX`：`value` 是与 name 关联的 mailserver name

**使用场景：**  
不同查询目的使用不同 RR type：访问 Web 常用 A/CNAME，邮件投递常用 MX，委派 domain 常用 NS。

**使用方法 / 工作流程：**  
DNS query 指定 name 和 type；DNS reply 在 answers、authority、additional 等区域返回 RR。

**例子：**  
`(networkutopia.com, dns1.networkutopia.com, NS)` 表示 `networkutopia.com` 的 authoritative name server 是 `dns1.networkutopia.com`。

### 知识点：DNS protocol messages

**定义：**  
DNS query 和 reply 使用相同 message format。

**解释：**  
PPT 给出的 header fields：

- identification：16-bit number；reply 使用相同 identification
- flags：query/reply、recursion desired、recursion available、reply is authoritative
- `# questions`
- `# answer RRs`
- `# authority RRs`
- `# additional RRs`

Message body 包含：

- questions：query 的 name/type fields
- answers：RRs in response to query
- authority：records for authoritative servers
- additional info：可能有用的附加信息

**使用场景：**  
用于 DNS client 和 DNS server 之间的请求/响应通信。

**使用方法 / 工作流程：**  
client 发送包含 identification 的 query；server 回复时带相同 identification，使 client 能匹配 request 和 response。

**例子：**  
查询 `www.example.com` 的 A record 时，question section 放 name 和 type；answer section 可能返回 IP；authority section 可能给 authoritative server 信息。

### 知识点：插入 DNS records

**定义：**  
将新 domain 加入 DNS 需要注册 domain，并为其建立 authoritative DNS records。

**解释：**  
PPT 例子是 startup “Network Utopia”：

1. 在 DNS registrar（例如 Network Solutions）注册 `networkutopia.com`
2. 提供 authoritative name server 的 names 和 IP addresses（primary and secondary）
3. registrar 向 `.com` TLD server 插入：

```text
(networkutopia.com, dns1.networkutopia.com, NS)
(dns1.networkutopia.com, 212.212.212.1, A)
```

4. 本地创建 authoritative server，IP 为 `212.212.212.1`
5. authoritative server 中包含 `www.networkuptopia.com` 的 A record
6. authoritative server 中包含 `networkutopia.com` 的 MX record

**使用场景：**  
新公司、网站或邮件域上线时需要配置 DNS。

**使用方法 / 工作流程：**  
先注册 domain；再配置 authoritative server；最后添加 A、MX 等记录。

**例子：**  
`www.networkuptopia.com` 可以通过该 domain 的 authoritative DNS server 返回 Web server 的 IP。

### 知识点：更新 DNS records 的 TTL 流程

**定义：**  
更新 DNS record 时，需要考虑旧记录可能在其他 DNS server 中缓存到 TTL 结束。

**解释：**  
PPT 给出的 general guidelines：

1. Record the current TTL value of the record
2. Lower the TTL of the record to a low value，例如 30 seconds
3. Wait the length of the previous TTL
4. Update the record
5. Wait for some time，例如 1 hour
6. Change the TTL back to previous time

**使用场景：**  
网站迁移 IP、替换邮件服务器、切换 CDN 或负载均衡配置。

**使用方法 / 工作流程：**  
先降低 TTL 并等待旧 TTL 过期，再改值；改完确认稳定后恢复原 TTL。

**例子：**  
如果 `www.example.com` 的 TTL 原来是 24 小时，要迁移服务器时应先把 TTL 降到 30 秒，等 24 小时后再改 A record。

### 知识点：DNS reliability

**定义：**  
DNS reliability 指 DNS 服务通过 replication、timeout retry 和协议支持保证高可用。

**解释：**  
PPT 重点：

- DNS servers are replicated（primary/secondary）
- 至少一个 replica up，name service 就可用
- queries 可在 replicas 间 load-balanced
- DNS query 通常使用 UDP
- 需要可靠性时必须在 UDP 之上实现
- DNS spec 也支持 TCP，但不一定总被实现
- DNS uses port 53
- timeout 时尝试 alternate servers
- retry same server 时使用 exponential backoff
- same identifier for all queries，不关心哪个 server response

**使用场景：**  
DNS server 故障、packet loss、UDP response 丢失或多个 replica 并存时。

**使用方法 / 工作流程：**  
client 发 UDP query 到 DNS server；若 timeout，换 server 或 exponential backoff 重试；response 用 identification 匹配。

**例子：**  
local DNS server 问某 authoritative DNS server 没有回应，可以等待后改问 secondary authoritative server。

### 知识点：CDN 与 DNS 的关系

**定义：**  
CDN 常借助 DNS 将用户导向合适的 CDN server。

**解释：**  
PPT 在 DNS 部分用 `dig` 展示：很多知名网站由 CDN hosting，可以通过 DNS 查询观察到 CNAME 或 CDN 相关记录。

**使用场景：**  
视频、图片、网页静态资源等内容分发。

**使用方法 / 工作流程：**  
用户访问 origin domain；DNS 解析过程中可能返回 CDN domain 或离用户更合适的 CDN server。

**例子：**  
查询某视频网站域名时，DNS 可能返回指向 CDN provider 的 CNAME。

### 知识点：是否信任你的 DNS server

**定义：**  
DNS server 可以影响用户访问结果，也可能记录用户访问行为。

**解释：**  
PPT 提到两个问题：

- Censorship：DNS 可被用于审查或屏蔽
- Logging：DNS server 可能记录 IP address、websites visited、geolocation data 等

**使用场景：**  
选择 public DNS、公司/学校 DNS、ISP DNS 时需要考虑隐私和可信度。

**使用方法 / 工作流程：**  
用户或系统配置 DNS resolver；所有 hostname 查询会经过该 resolver，因此 resolver 可能观察到访问意图。

**例子：**  
使用 Google DNS 时，需要注意其 public DNS privacy policy 中关于记录信息的说明。

### 知识点：DNS Cache Poisoning

**定义：**  
DNS cache poisoning 是攻击者诱导 DNS server 缓存错误 IP mapping 的攻击。

**解释：**  
PPT 示例中，攻击者控制 `drevil.com` 的 name server。当它收到 `www.drevil.com` 请求时，在 additional section 中夹带：

```text
google.com 600 IN A 129.45.212.222
```

这个 IP 实际是 attacker 机器而不是 Google。如果 DNS server 错误缓存这个 mapping，之后访问 `google.com` 可能被导向攻击者。

PPT 给出的 solution：不要允许 DNS servers 缓存 IP address mappings，除非这些 mapping 来自 authoritative name servers。

**使用场景：**  
DNS 安全、防止伪造记录污染 cache。

**使用方法 / 工作流程：**  
DNS resolver 在缓存 additional records 前检查这些记录是否来自有 authority 的 server。

**例子：**  
`drevil.com` 的 authoritative server 可以回答 `drevil.com` 相关记录，但不应该让 resolver 接受它声称的 `google.com` IP。

### 知识点：DoH 和 DoT（NOT ON EXAM）

**定义：**  
DoT 是 DNS over Transport Layer Security（TLS）；DoH 是 DNS over HTTPS 或 HTTP/2。

**考试范围：**  
PPT 明确标注：`NOT ON EXAM`。

**解释：**  
PPT 内容：

- DoT：port 853
- DoH：port 443
- 目标是增加 user privacy and security
- DoH traffic masked with other HTTPS traffic
- Cloudflare、Google 等有 publicly accessible DoT resolvers
- Chrome 和 Mozilla 支持 DoH，OS support 也可用或正在到来

**使用场景：**  
希望 DNS query 更隐私、更难被中间人观察或篡改时。

**使用方法 / 工作流程：**  
client 将 DNS 查询放进 TLS 或 HTTPS 通道中，而不是普通 UDP/53。

**例子：**  
使用 DoH 时，DNS request 通过 HTTPS 的 443 端口发送，看起来和普通 HTTPS traffic 混在一起。

### 知识点：DNS Quiz 汇总

**定义：**  
PPT 提供 DNS 小测用于检查 root server、local DNS server、authoritative server、MX record 等概念。

**解释：**  
Quiz 涉及：

- local DNS server 没线索时会问 root DNS server
- client-side ISP 维护 local DNS server；domain name owner 维护 authoritative DNS server
- 给 `mahbub@unsw.edu.au` 发邮件会触发 MX query
- local DNS server 获取 `www.pollev.com` IP 的最小 DNS request 数可能为 0（如果 cache 已有）

**使用场景：**  
用于考试前确认 DNS hierarchy 和 record type。

**使用方法 / 工作流程：**  
看题时先判断 cache 是否存在、查询目标是 Web 还是 mail、server 由谁维护。

**例子：**  
如果 local DNS server cache 中已有 `www.pollev.com` 的 A record，则它不需要再向外发送 DNS request，最小数量为 0。  
**补充理解：** 这一题答案依赖 “minimum” 和是否允许 cache 命中；若 cache 没有，则通常需要 root、TLD、authoritative 等查询。

## 2.5 P2P Applications（Self Study / NOT ON EXAM）

### 知识点：P2P architecture（Self Study / NOT ON EXAM）

**定义：**  
Peer-to-peer（P2P）architecture 中没有 always-on server，arbitrary end systems 直接通信。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
Peers 既 request service from other peers，也 provide service in return。P2P 具有 self scalability：new peers 带来 new service capacity，同时也带来 new service demands。缺点是 peers intermittently connected 且 change IP addresses，管理更复杂。

**使用场景：**  
PPT 例子包括 P2P file sharing（BitTorrent）、streaming（KanKan）、VoIP（Skype）、Cryptocurrency（Bitcoin）。

**使用方法 / 工作流程：**  
peer 加入系统后寻找其他 peers，与它们直接交换数据或服务。

**例子：**  
BitTorrent 中，下载者一边从其他 peers 下载 chunks，一边把已有 chunks 上传给其他 peers。

### 知识点：File distribution - client-server vs P2P（Self Study / NOT ON EXAM）

**定义：**  
File distribution 问题是：从一个 server 向 N 个 peers 分发大小为 F 的文件需要多久。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
PPT 使用变量：

- `F`：file size
- `N`：number of peers
- `us`：server upload capacity
- `di`：peer i download capacity
- `ui`：peer i upload capacity
- `dmin`：minimum client download rate

**使用场景：**  
比较集中式下载和 P2P 下载在大量用户场景下的扩展性。

**使用方法 / 工作流程：**  
计算时关注 server 上传瓶颈、最慢 client 下载瓶颈、以及 P2P 中所有 peers 的 aggregate upload capacity。

**例子：**  
同一个 1GB 文件发给 1000 个用户，client-server 主要受 server 上传影响；P2P 中每个 peer 也能上传，整体服务能力随 peer 数增加。

### 知识点：Client-server file distribution time（Self Study / NOT ON EXAM）

**定义：**  
Client-server 分发时间由 server 上传 N 份文件和最慢 client 下载一份文件共同限制。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
PPT 公式：

```text
Dc-s > max{NF/us, F/dmin}
```

含义：

- server must sequentially send/upload N file copies
- time to send one copy：`F/us`
- time to send N copies：`NF/us`
- each client must download one copy
- slowest client download time：`F/dmin`

**使用场景：**  
中心服务器向大量 client 分发文件。

**使用方法 / 工作流程：**  
分别算 server 总上传时间和最慢 client 下载时间，取较大者作为下界。

**例子：**  
如果 server 上传很慢，即使每个 client 下载很快，总时间也会随 `N` 线性增加。

### 知识点：P2P file distribution time（Self Study / NOT ON EXAM）

**定义：**  
P2P 分发时间不仅依赖 server 上传，也依赖所有 peers 的 aggregate upload capacity。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
PPT 公式：

```text
DP2P > max{F/us, F/dmin, NF/(us + Σui)}
```

含义：

- server 至少上传 one copy：`F/us`
- 最慢 client 至少下载 one copy：`F/dmin`
- 所有 clients 总共需要下载 `NF` bits
- 最大可用上传速率是 `us + Σui`

PPT 强调：随着 `N` 增加，`NF` 线性增加，但 `Σui` 也增加，因为每个 peer 带来 service capacity。

**使用场景：**  
适合大规模文件分发，例如 torrent。

**使用方法 / 工作流程：**  
计算 server、client 和 aggregate upload 三个下界，取最大值。

**例子：**  
如果每个新 peer 都有上传能力，P2P 的最小分发时间增长通常比 client-server 慢。

### 知识点：BitTorrent chunks 与 tracker（Self Study / NOT ON EXAM）

**定义：**  
BitTorrent 将 file 分成 256KB chunks；torrent 是 exchanging chunks of a file 的 peers group；tracker tracks peers participating in torrent。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
新 peer 加入 torrent 时没有 chunks，但会逐渐从其他 peers 获得。它向 tracker 注册并获得 peers list，然后连接其中一部分 peers（neighbors）。

**使用场景：**  
分布式文件下载和上传。

**使用方法 / 工作流程：**  
peer 加入 -> 向 tracker 获取 peers -> 连接 neighbors -> 下载 chunks 的同时上传 chunks -> peers 可能 come and go（churn）-> 下载完成后可 leave 或 remain。

**例子：**  
Alice 加入 torrent，先从 tracker 获得 peer list，然后开始与 torrent 中其他 peers 交换 file chunks。

### 知识点：BitTorrent requesting chunks（Self Study / NOT ON EXAM）

**定义：**  
Requesting chunks 指 peer 决定向哪些 peers 请求哪些缺失 chunks。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
PPT 内容：

- 不同 peers 在任意时刻有不同 file chunks subset
- Alice 定期询问每个 peer 拥有哪些 chunks
- Alice 从 peers 请求自己 missing chunks
- 策略是 rarest first

**使用场景：**  
让 torrent 中稀有 chunks 更快扩散，避免某些 chunks 成为瓶颈。

**使用方法 / 工作流程：**  
peer 收集 neighbors 的 chunk list，优先请求自己缺失且最稀有的 chunk。

**例子：**  
如果 chunk #12 只有一个 peer 有，而 chunk #5 有很多 peers 有，Alice 应优先请求 chunk #12。

### 知识点：BitTorrent tit-for-tat（Self Study / NOT ON EXAM）

**定义：**  
Tit-for-tat 是 BitTorrent 中决定向哪些 peers 上传 chunks 的策略。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
PPT 内容：

- Alice sends chunks to four peers currently sending her chunks at highest rate
- other peers are choked，不从 Alice 接收 chunks
- 每 10 seconds 重新评估 top 4
- 每 30 seconds 随机选择另一个 peer，开始发送 chunks，称为 optimistically unchoke
- 新选 peer 可能进入 top 4

**使用场景：**  
鼓励 peers 贡献上传带宽；上传越多越容易找到更好的 trading partners，下载更快。

**使用方法 / 工作流程：**  
peer 周期性统计谁给自己传得快，把上传资源给 top peers，同时保留随机探索。

**例子：**  
Alice optimistically unchokes Bob；Bob 因此把 Alice 作为 top-four provider；Bob 反过来给 Alice 上传；Bob 也可能成为 Alice 的 top-four provider。

### 知识点：BitTorrent Quiz（Self Study / NOT ON EXAM）

**定义：**  
PPT 小测检查 tit-for-tat 的作用。

**考试范围：**  
PPT 明确标注：`Self Study`、`NOT ON EXAM`。

**解释：**  
题目问 BitTorrent uses tit-for-tat in each round to do what。  
**补充理解：** 根据 PPT，tit-for-tat 用于 determine to which peers to upload chunks，对应选项 c。

**使用场景：**  
区分 rarest first（决定下载哪些 chunks）和 tit-for-tat（决定给谁上传）。

**使用方法 / 工作流程：**  
看到 “request chunks” 想 rarest first；看到 “send chunks/upload” 想 tit-for-tat。

**例子：**  
Alice 下载 chunk 时用 rarest first；Alice 决定把 chunk 上传给谁时用 tit-for-tat。

## 2.6 Video Streaming and Content Distribution Networks（CDNs）

### 知识点：Video streaming and CDNs context

**定义：**  
Video streaming 是通过 Internet 连续传输和播放视频内容；CDN 是用于大规模分发内容的 application-level infrastructure。

**解释：**  
PPT 指出 stream video traffic 是 Internet bandwidth 的主要消费者。Netflix、YouTube、Amazon Prime 在 2020 年占 residential ISP traffic 的 80%。挑战包括：

- scale：如何 reach about 1B users
- single mega-video server won’t work
- heterogeneity：不同用户能力不同，例如 wired vs mobile、bandwidth rich vs bandwidth poor

**使用场景：**  
视频点播、直播、在线课程、短视频平台。

**使用方法 / 工作流程：**  
使用 distributed, application-level infrastructure，让用户从更近或更合适的服务器获取视频。

**例子：**  
YouTube 不会让全球用户都从一台 mega-server 下载视频，而会用分布式服务器和 CDN。

### 知识点：Multimedia video 基础

**定义：**  
Video 是以固定速率显示的 image sequence；digital image 是 pixel array，每个 pixel 用 bits 表示。

**解释：**  
PPT 例子：video 可为 24 images/sec。为了降低 encoding bits，video coding 利用 redundancy：

- spatial coding：within image
- temporal coding：from one image to next

**使用场景：**  
视频压缩、视频传输、流媒体编码。

**使用方法 / 工作流程：**  
编码器检测图像内重复内容和相邻帧之间差异，只发送必要信息。

**例子：**  
Spatial coding：如果连续 N 个像素都是 green，不发送 N 次 green，而发送 color value = Green 和 repeated count = N。  
Temporal coding：frame i+1 与 frame i 很相似时，只发送 differences。

### 知识点：CBR 和 VBR

**定义：**  
CBR（constant bit rate）表示 video encoding rate fixed；VBR（variable bit rate）表示 encoding rate 随 spatial/temporal coding 的变化而变化。

**解释：**  
PPT 给出的例子：

- MPEG1（CD-ROM）：1.5 Mbps
- MPEG2（DVD）：3-6 Mbps
- MPEG4（often used in Internet）：64 Kbps - 12 Mbps

**使用场景：**  
选择视频编码方式、估计网络带宽需求。

**使用方法 / 工作流程：**  
CBR 用固定码率编码；VBR 根据画面复杂度和变化程度调整码率。

**例子：**  
静态讲课画面变化少，VBR 可能用较低码率；动作电影画面变化大，VBR 可能临时提高码率。

### 知识点：DASH - Dynamic Adaptive Streaming over HTTP

**定义：**  
DASH 是 Dynamic, Adaptive Streaming over HTTP，一种基于 HTTP 的动态自适应视频流技术。

**解释：**  
PPT 中 server 端：

- divides video file into multiple chunks
- each chunk stored, encoded at different rates
- manifest file provides URLs for different chunks

client 端：

- periodically measures server-to-client bandwidth
- consulting manifest, requests one chunk at a time
- chooses maximum coding rate sustainable given current bandwidth
- can choose different coding rates at different points in time

**使用场景：**  
网络带宽变化时仍要平滑播放视频。

**使用方法 / 工作流程：**  
server 准备多码率 chunks 和 manifest；client 测量带宽后按 chunk 选择合适码率并通过 HTTP 请求。

**例子：**  
用户手机网络从 5G 切到弱 Wi-Fi，DASH client 可从 1080p chunk 降到 480p chunk，避免 buffer starvation。

### 知识点：DASH client intelligence

**定义：**  
DASH 的 intelligence 位于 client，client 决定何时请求、请求什么码率、从哪里请求。

**解释：**  
PPT 中 client determines：

- when to request chunk：避免 buffer starvation 或 overflow
- what encoding rate to request：bandwidth more available 时请求 higher quality
- where to request chunk：可从 close to client 或 high available bandwidth 的 URL/server 请求

PPT 总结：

```text
Streaming video = encoding + DASH + playout buffering
```

**使用场景：**  
视频播放器自适应码率、选择 CDN server、维持播放缓冲。

**使用方法 / 工作流程：**  
播放器监测 buffer 和带宽，根据 manifest 按需下载下一段视频。

**例子：**  
Netflix 播放器检测到 buffer 快耗尽时，可能请求较低码率 chunk 来保证不中断。

### 知识点：CDN 的动机

**定义：**  
CDN（Content Distribution Network）通过在多个地理位置分布式存储和提供内容，服务大量并发用户。

**解释：**  
PPT 提出挑战：如何把 millions of videos 中选出的内容 stream 给 hundreds of thousands simultaneous users。  
Option 1：single large mega-server，不可扩展，因为：

- single point of failure
- point of network congestion
- long path to distant clients
- multiple copies of video sent over outgoing link

PPT 总结：this solution doesn’t scale。

**使用场景：**  
大规模视频、图片、网页和软件下载。

**使用方法 / 工作流程：**  
避免单一 mega-server，把内容复制到多个 CDN nodes。

**例子：**  
如果所有澳洲用户都从美国一个服务器看视频，路径长、拥塞高、故障影响大。

### 知识点：CDN 部署方式 - enter deep 和 bring home

**定义：**  
CDN 的 option 2 是在多个 geographically distributed sites 存储/服务多份 video copies。

**解释：**  
PPT 列出两种部署思路：

- enter deep：把 CDN servers push deep into many access networks，close to users；Akamai 例子是 2015 年在超过 120 个国家部署 240,000 servers
- bring home：在 access networks 附近的 POPs 中部署较少数量（10’s）的更大 clusters；Limelight 使用这种方式

**使用场景：**  
CDN provider 设计节点位置和规模。

**使用方法 / 工作流程：**  
根据成本、覆盖范围、性能需求选择更深入接入网或更集中 POP cluster。

**例子：**  
Akamai 更倾向把服务器放得离用户接入网很近；Limelight 更倾向在少数大型 POPs 中放大集群。

### 知识点：CDN content access

**定义：**  
CDN content access 是用户请求内容时，被导向某个 CDN copy 并取回内容的过程。

**解释：**  
PPT 内容：

- CDN stores copies of content at CDN nodes
- subscriber requests content from CDN
- directed to nearby copy, retrieves content
- may choose different copy if network path congested

**使用场景：**  
点播视频、网页静态资源、软件下载。

**使用方法 / 工作流程：**  
用户请求内容 -> CDN 选择/返回附近或合适节点 -> 用户通过 HTTP/DASH 获取内容。

**例子：**  
Netflix 可把 `MadMen` 的 copies 存在多个 CDN nodes；用户请求时被导向附近 copy。

### 知识点：OTT - over the top

**定义：**  
OTT（over the top）指把 Internet host-host communication 当作 service 来承载应用内容。

**解释：**  
PPT 中 OTT challenges 包括：

- coping with a congested Internet
- from which CDN node to retrieve content
- viewer behavior in presence of congestion
- what content to place in which CDN node

**使用场景：**  
Netflix、YouTube 等通过公共 Internet 提供内容，而不是完全控制底层网络。

**使用方法 / 工作流程：**  
应用层基础设施需要自己处理 CDN 节点选择、拥塞下用户体验、内容放置策略。

**例子：**  
某热门剧集应提前放到哪些 CDN nodes，取决于预计观众位置和需求。

### 知识点：CDN access closer look - DNS redirection

**定义：**  
CDN 可通过 DNS CNAME 和 authoritative DNS 把用户导向 CDN server。

**解释：**  
PPT 例子：Bob 请求 `http://netcinema.com/6Y7B23V`，video 实际存在 CDN：`http://KingCDN.com/NetC6y&B23V`。

流程：

1. Bob 从 `netcinema.com` web page 获取 video URL
2. Bob 的 local DNS 解析 `http://netcinema.com/6Y7B23V`
3. `netcinema` authoritative DNS 返回 CNAME，指向 `http://KingCDN.com/NetC6y&B23V`
4. local DNS 继续与 KingCDN authoritative DNS 交互
5. Bob 得到 KingCDN server 信息
6. Bob 向 KingCDN server 请求视频，视频通过 HTTP streamed

**使用场景：**  
CDN 为 origin site 代发内容并选择合适节点。

**使用方法 / 工作流程：**  
origin domain 的 DNS 返回 CDN alias；CDN 的 DNS 根据用户位置/网络状态返回具体 CDN server。

**例子：**  
访问 `netcinema.com` 的视频 URL，最终实际向 `KingCDN.com` 的某个服务器下载视频。

### 知识点：Case study - Netflix

**定义：**  
Netflix 使用云端注册/计费服务器和 CDN servers 协同完成浏览、manifest 返回和 DASH streaming。

**解释：**  
PPT 流程：

1. Bob manages Netflix account，与 Netflix registration/accounting servers 交互
2. Bob browses Netflix video
3. 系统返回特定 video 的 manifest file
4. DASH server selected, contacted, streaming begins
5. Amazon cloud 上传 multiple versions of video copies 到 CDN servers

**使用场景：**  
真实大规模视频流服务架构。

**使用方法 / 工作流程：**  
账户和浏览逻辑可在 cloud；视频多码率内容放到 CDN；client 根据 manifest 与 DASH/CDN server 通信。

**例子：**  
Bob 选择一部电影后，Netflix 返回 manifest，播放器选择 CDN server 并开始请求 DASH chunks。

### 知识点：CDN Quiz

**定义：**  
PPT 小测检查 CDN provider authoritative DNS name server 的作用。

**解释：**  
题目问 CDN provider authoritative DNS name server 的作用。  
**补充理解：** 根据 PPT 的 CDN DNS redirection，最符合的是：map the query for each CDN object to the CDN server closest to the requestor，对应选项 b。

**使用场景：**  
区分 origin server、CDN DNS、CDN server 的职责。

**使用方法 / 工作流程：**  
CDN authoritative DNS 根据请求者位置或网络情况返回合适 CDN server。

**例子：**  
澳洲用户解析 CDN object 时，CDN DNS 可能返回悉尼附近 CDN node，而不是美国节点。

## 2.7 Model Context Protocol（MCP）

### 知识点：MCP 基本定义

**定义：**  
MCP（Model Context Protocol）是一个 open application-layer protocol，用于连接 AI hosts 与 external context and tools，并 standardizes messages and discovery。

**解释：**  
PPT 明确：MCP does not define the LLM, UI or backend logic。也就是说，MCP 定义通信协议和发现机制，不规定模型本身、用户界面或后端业务逻辑。

**使用场景：**  
AI 应用需要访问外部工具、文件、天气服务、数据库上下文等。

**使用方法 / 工作流程：**  
AI host 通过 MCP client 与 MCP server 交互；server 暴露 tools、resources、prompts 等能力。

**例子：**  
一个 AI host 需要查询天气，可通过 MCP weather server 调用 `get_weather` 工具，而不是把天气逻辑写进 LLM。

### 知识点：MCP architecture - host, clients and servers

**定义：**  
MCP architecture 包含 host、MCP clients 和 MCP servers。

**解释：**  
PPT 中 host 包含 UI + LLM + orchestration + consent policy。Host 可以有多个 MCP clients；每个 client 连接一个 server。Server 可以是 local files MCP server（stdio）或 remote weather MCP server（Streamable HTTP）。

PPT 强调：The host enforces boundaries between servers。

**使用场景：**  
AI 应用同时连接多个工具或数据源时，需要隔离和权限控制。

**使用方法 / 工作流程：**  
host 管理用户同意和边界；client 与 server 建立 transport；server 提供工具或上下文。

**例子：**  
一个 AI host 同时连本地文件 server 和远程天气 server；host 决定模型能否调用每个 server 的工具。

### 知识点：RPC - Remote Procedure Calls

**定义：**  
RPC（Remote Procedure Call）是让一个 process 调用另一个 process 中 procedure 的通信方式。

**解释：**  
Local call 在同一 process 中，例如 `get_weather("Sydney")` 返回 `22 °C`。RPC 则跨 process，request 包含 method、parameters、id；response 包含 result or error 和 same id。

PPT 强调：network may delay a call or prevent a response；one call does not map to one packet。

**使用场景：**  
客户端调用远程服务方法，例如查询天气、调用工具、访问后端 API。

**使用方法 / 工作流程：**  
RPC client 发送 method/parameters/id；RPC server 执行并返回 result/error；client 用 id 匹配响应。

**例子：**  
client 发送 `get_weather("Sydney")` 请求给 server，server 返回 `22 °C`；如果网络失败，client 可能收不到 response。

### 知识点：JSON structured data

**定义：**  
JSON 是 written as text 的 structured data 格式。

**解释：**  
PPT 示例 JSON-RPC request：

```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "method": "get_weather",
  "params": {"city": "Sydney"}
}
```

JSON 语法：

- `{ }` encloses an object
- 每个 member 形式为 `"name": value`
- member names 和 string values 使用 double quotes
- values 可为 numbers、true、false、null、arrays 或 objects

PPT 强调：JSON defines the text syntax；JSON-RPC defines the request and response rules。

**使用场景：**  
Web API、RPC message、MCP message envelope。

**使用方法 / 工作流程：**  
把结构化信息编码为 JSON text，在网络或 stdio 中传输。

**例子：**  
`"params": {"city": "Sydney"}` 表示参数对象中 city 字段为 Sydney。

### 知识点：JSON-RPC 2.0 envelope

**定义：**  
JSON-RPC 2.0 envelope 是用 JSON 表示 request、response 和 error 的规则。

**解释：**  
PPT 中 MCP 位于 JSON-RPC 之上：

```text
MCP methods + schemas
        ↓
JSON-RPC 2.0 envelope
        ↓
two standard bindings:
stdio -> local process I/O
Streamable HTTP -> network stack
```

**使用场景：**  
MCP request/response、工具调用、跨进程方法调用。

**使用方法 / 工作流程：**  
request 使用 `jsonrpc`、`id`、`method`、`params`；response 用相同 `id` 返回 `result` 或 `error`。

**例子：**  
`tools/list` 可以作为 JSON-RPC method 发送；server 返回可用 tools。

### 知识点：MCP adds semantics above JSON-RPC

**定义：**  
MCP 在 JSON-RPC envelope 之上定义 methods、schemas、capabilities，以及 result/error/update semantics。

**解释：**  
PPT 中 MCP defines：

- methods such as `tools/list`
- schemas and capabilities
- result, error and update semantics（meaning）

JSON-RPC 只规定请求/响应格式；MCP 规定这些方法和结果代表什么。

**使用场景：**  
AI host 需要标准化发现工具、读取资源、调用工具。

**使用方法 / 工作流程：**  
先遵守 JSON-RPC 格式，再使用 MCP 定义的方法名、schema 和 capability。

**例子：**  
`tools/list` 的 meaning 是列出工具，而不是任意普通 RPC 方法；这是 MCP 的语义。

### 知识点：id links request to response

**定义：**  
JSON-RPC/MCP 中 `id` 用于把 request 与 response 关联起来。

**解释：**  
PPT 示例：

Request has id：

```json
{"jsonrpc":"2.0","id":7,"method":"tools/list","params":{...}}
```

Success repeats id：

```json
{"jsonrpc":"2.0","id":7,"result":{"resultType":"complete",...}}
```

Error repeats id when readable：

```json
{"jsonrpc":"2.0","id":7,"error":{"code":-32602,"message":"Invalid params"}}
```

Subscribed notification has no id and no JSON-RPC response：

```json
{"jsonrpc":"2.0","method":"notifications/tools/list_changed","params":{"_meta":{"io.modelcontextprotocol/subscriptionId":4}}}
```

**使用场景：**  
当多个 request 并发发送时，client 需要知道哪个 response 对应哪个 request。

**使用方法 / 工作流程：**  
发送 request 时带 `id`；server response 重复该 `id`；notification 不带 `id`，因此不期待 response。

**例子：**  
client 同时发送 `id=7` 的 `tools/list` 和 `id=8` 的 `resources/list`，收到响应后根据 id 匹配。

### 知识点：Current MCP is stateless

**定义：**  
Current MCP 是 stateless：每个 request 都要提供必要状态信息。

**解释：**  
PPT 说 every request supplies：

- protocol version
- relevant client capabilities
- client information（normally）
- method-specific parameters

`server/discover`：

- server MUST support it
- client MAY call it first
- returns supported versions and capabilities
- normally includes self-reported identity

**使用场景：**  
客户端与 server 建立兼容性、能力发现，以及每次请求携带上下文。

**使用方法 / 工作流程：**  
client 可先调用 `server/discover`，确认 protocol version 和 capabilities，再调用具体 MCP methods。

**例子：**  
AI host 首次连接 weather MCP server 时，先 `server/discover`，再 `tools/list` 查看 `get_weather` 是否可用。

### 知识点：MCP three primitives - prompts, resources, tools

**定义：**  
MCP 的三个 primitives 是 prompts、resources、tools。

**解释：**  
PPT 内容：

- Prompts：reusable message templates；user-controlled；方法有 `prompts/list`、`prompts/get`
- Resources：read-only data；identified by URI；方法有 `resources/list`、`resources/read`
- Tools：executable functions；model may select；host policy decides；方法有 `tools/list`、`tools/call`

**使用场景：**  
prompts 用于模板化指令；resources 用于只读上下文；tools 用于执行操作。

**使用方法 / 工作流程：**  
client 列出可用 prompts/resources/tools，再按需 get/read/call。

**例子：**  
一个文件 MCP server 可把本地文件作为 resource；一个天气 MCP server 可把 `get_weather` 暴露为 tool。

### 知识点：MCP standard bindings - stdio and Streamable HTTP

**定义：**  
MCP 有两个 standard bindings：stdio 和 Streamable HTTP。

**解释：**  
stdio（local）：

- host launches a subprocess
- newline-delimited UTF-8 JSON-RPC
- messages use stdin/stdout
- logs use stderr
- cancel with `notifications/cancelled`

Streamable HTTP（typically remote）：

- one MCP endpoint
- each client message is an HTTP POST
- a request receives JSON or request-scoped SSE（Server Sent Events）
- close that SSE response stream to cancel

PPT 强调：same MCP methods + semantics。

**使用场景：**  
本地工具适合 stdio；远程服务适合 Streamable HTTP。

**使用方法 / 工作流程：**  
stdio 中 host 启动 server 进程并通过 stdin/stdout 交换 JSON-RPC；HTTP 中 client 对 MCP endpoint 发 POST。

**例子：**  
本地文件 server 用 stdio；远程 weather server 用 Streamable HTTP。

### 知识点：MCP over HTTP - discover, describe, invoke

**定义：**  
MCP over HTTP 使用 HTTP POST 承载 JSON-RPC/MCP 消息，完成 discover、describe、invoke 流程。

**解释：**  
PPT 流程：

1. MCP client / AI host -> MCP weather server：`server/discover [id 1]`
2. server -> client：versions + capabilities `[id 1]`
3. client -> server：`tools/list [id 2]`
4. server -> client：`get_weather + JSON Schema [id 2]`
5. LLM selects the tool inside the host
6. client -> server：`tools/call get_weather [id 3]`
7. server -> client：tool result `[id 3]`

HTTP example fields:

```http
POST /mcp
Content-Type: application/json
Accept: application/json, text/event-stream
MCP-Protocol-Version: 2026-07-28
Mcp-Method: tools/call
Mcp-Name: get_weather
```

PPT 强调：Each client request uses a separate HTTP POST。

**使用场景：**  
远程 MCP server 提供工具给 AI host 使用。

**使用方法 / 工作流程：**  
先 discover 能力，再 list tools 描述工具 schema，最后 call tool。

**例子：**  
AI host 发现 weather server 支持 `get_weather`，读取 schema 后让 LLM 选择调用，最后通过 `tools/call` 获取 Sydney 天气。

### 知识点：MCP transport Quiz

**定义：**  
PPT 小测检查 MCP transport 的正确理解。

**解释：**  
题目选项中正确说法是：`Streamable HTTP carries JSON-RPC messages in HTTP POSTs`。  
**补充理解：** 这对应选项 B。A 错，因为 one call does not map to one packet；C 错，因为 notification no id；D 错，因为 host enforces consent policy。

**使用场景：**  
区分 MCP、JSON-RPC、HTTP、IP packet 和 host/server 权限边界。

**使用方法 / 工作流程：**  
判断 transport 时看 PPT：HTTP binding 是 separate HTTP POST；notification 无 id；consent policy 在 host。

**例子：**  
`tools/call` 通过 HTTP POST 发送 JSON-RPC message，但底层可能被 TCP/IP 分成多个 packets。

## 2.8 Socket Programming with UDP and TCP

### 知识点：Socket programming 总目标

**定义：**  
Socket programming 是学习如何构建通过 sockets 通信的 client/server applications。

**解释：**  
PPT 定义：socket 是 application process 和 end-to-end transport protocol 之间的 door。Application process 和 socket 由 app developer 控制；transport、network、link、physical layers 由 OS 控制。

**使用场景：**  
自己编写网络应用，例如 UDP Ping client/server、TCP echo server、HTTP-like server。

**使用方法 / 工作流程：**  
应用通过 socket API 创建 socket，选择 UDP 或 TCP，发送/接收数据；OS 负责底层传输和网络层细节。

**例子：**  
Python 程序创建 UDP socket，然后向 server IP + port 发送一段 bytes。

### 知识点：Socket 与分层结构

**定义：**  
Socket 是应用层和传输层之间的接口。

**解释：**  
PPT 图中 application process 通过 socket 接入 transport；transport 再经过 network、link、physical 层进入 Internet。

**使用场景：**  
理解为什么应用程序不直接操作 IP packet 或 Ethernet frame。

**使用方法 / 工作流程：**  
程序员使用 socket API；OS 根据 socket 类型调用 TCP/UDP/IP 等协议栈。

**例子：**  
使用 TCP socket 写入 bytes 后，应用不需要自己封装 TCP segment 或 IP datagram。

### 知识点：Socket programming with UDP

**定义：**  
UDP socket 提供 client/server 间 unreliable transfer of groups of bytes（segments），没有连接。

**解释：**  
PPT 内容：

- UDP: no “connection” between client & server
- no handshaking before sending data
- sender explicitly attaches IP destination address and port # to each packet
- receiver extracts sender IP address and port # from received packet
- transmitted data may be lost or received out-of-order

**使用场景：**  
适合简单请求响应、低延迟、应用自己处理丢包的场景，例如课程 UDP ping。

**使用方法 / 工作流程：**  
发送方每次发送都指定 destination IP 和 destination port；接收方从收到的 segment 中提取 sender IP/port，以便回复。

**例子：**  
UDP client 向 `127.0.0.1:12000` 发送 ping message；server 从 packet 中读出 client 的临时 port，再回复该 client。

### 知识点：UDP client pseudo code

**定义：**  
UDP client pseudo code 描述 UDP 客户端的基本流程。

**解释：**  
PPT 流程：

1. Create socket
2. Loop
   - Send UDP segment to known port and IP addr of server
   - Receive UDP segment as a response from server
3. Close socket

**使用场景：**  
客户端主动向已知 server 地址发送 datagram。

**使用方法 / 工作流程：**  
client 不需要连接 handshake；只要知道 server IP 和 port，就能发送。

**例子：**  
PingClient 每秒向 server 发一个 UDP segment，然后等待 response 或 timeout。

### 知识点：UDP server pseudo code

**定义：**  
UDP server pseudo code 描述 UDP 服务器的基本流程。

**解释：**  
PPT 流程：

1. Create socket
2. Bind socket to a specific port where clients can contact you
3. Loop
   - Receive UDP segment from client X
   - Send UDP segment as reply to client X
4. Close socket

PPT note：client 的 IP address 和 port number 必须从 client message 中 extracted。

**使用场景：**  
服务器在固定端口等待多个 clients 的 UDP datagrams。

**使用方法 / 工作流程：**  
server bind 固定 port；收到每个 datagram 后记录 sender address 并回复。

**例子：**  
UDP server bind `12000`，收到来自 `127.0.0.1:53427` 的消息后，把 response 发回 `53427`。

### 知识点：Socket programming with TCP

**定义：**  
TCP socket 提供 reliable, in-order byte-stream transfer，也就是 client 和 server 之间的 “pipe”。

**解释：**  
PPT 内容：

- client must contact server
- server process must first be running
- server must have created socket that welcomes client contact
- client 创建 TCP socket 时指定 server process 的 IP address 和 port number
- client TCP establishes connection to server TCP
- server 被 client 联系时，TCP creates new socket for server process to communicate with that particular client
- 这样 server 可与多个 clients 通信
- source port numbers used to distinguish clients

**使用场景：**  
需要可靠、有序 byte stream 的应用，例如 HTTP、SMTP、文件传输、远程登录。

**使用方法 / 工作流程：**  
server 先 listen；client connect；server accept 后为该 client 得到 connection socket；双方 read/write bytes。

**例子：**  
Web browser 创建 TCP socket 连接 Web server 的 port 80 或 443；连接建立后发送 HTTP request。

### 知识点：TCP welcoming socket 与 connection socket

**定义：**  
WelcomingSocket 是 server 用来接收新连接的 socket；ConnectionSocket 是用于和某个特定 client 通信的新 socket。

**解释：**  
PPT 中 TCP server 先创建 welcoming socket 并 listen；每次 accept new connection 时获得 connection socket。每个 client 有自己的 connection socket，因此 server 能同时服务多个 clients。

**使用场景：**  
TCP server 支持多 client 连接。

**使用方法 / 工作流程：**  
welcoming socket 负责等待连接；connection socket 负责实际 read/write。

**例子：**  
一个 TCP echo server 在 port `12000` listen；Client A 连接后产生 socket A，Client B 连接后产生 socket B。

### 知识点：TCP client pseudo code

**定义：**  
TCP client pseudo code 描述 TCP 客户端的基本流程。

**解释：**  
PPT 流程：

1. Create socket（ConnectionSocket）
2. Do an active connect specifying IP address and port number of server
3. Read and write data into ConnectionSocket to communicate with client
4. Close ConnectionSocket

**使用场景：**  
客户端主动连接已知 server。

**使用方法 / 工作流程：**  
client 创建 socket 后调用 connect；连接成功后像读写文件一样读写 byte stream。

**例子：**  
TCP client connect 到 `server.example.com:12000`，发送字符串，读取 server 返回的大写版本。

### 知识点：TCP server pseudo code

**定义：**  
TCP server pseudo code 描述 TCP 服务器的基本流程。

**解释：**  
PPT 流程：

1. Create socket（WelcomingSocket）
2. Bind socket to a specific port where clients can contact you
3. Register with OS your willingness to listen on that socket
4. Loop
   - Accept new connection（ConnectionSocket）
   - Read and write data into ConnectionSocket to communicate with client
   - Close ConnectionSocket
5. Close WelcomingSocket

**使用场景：**  
服务器长期运行并接受多个 TCP clients。

**使用方法 / 工作流程：**  
server bind/listen/accept，然后对每个 connection socket read/write/close。

**例子：**  
TCP server bind `12000` 并 listen；client connect 后，server accept 得到 connection socket，读取 client message 并回复。

### 知识点：UDP 与 TCP socket 对比

**定义：**  
UDP 是 connectionless、unreliable datagram/segment transfer；TCP 是 connection-oriented、reliable in-order byte-stream transfer。

**解释：**  
PPT 对比：

- UDP：no handshaking；每个 packet 附带 destination IP/port；可能 lost/out-of-order；receiver 从 packet 中提取 sender IP/port
- TCP：client/server 先建立 connection；server 用 welcoming socket 接收连接；每个 client 有 connection socket；提供 reliable, in-order byte-stream

**使用场景：**  
UDP 适合简单、低延迟、可容忍丢包或应用自管可靠性的场景；TCP 适合需要可靠有序传输的应用。

**使用方法 / 工作流程：**  
UDP server bind 后直接 recvfrom/sendto；TCP server bind/listen/accept 后 read/write。

**例子：**  
UDP Ping 作业用 UDP，因为要观察 packet loss 和 RTT；Web HTTP 通常用 TCP，因为需要可靠传输 HTML、图片和视频 manifest。

## Summary

### 知识点：Application Layer 总结

**定义：**  
本讲完成 network applications 的学习总结。

**解释：**  
PPT summary 覆盖：

- application architectures：client-server、P2P
- application service requirements：reliability、bandwidth、delay
- Internet transport service model：
  - connection-oriented, reliable：TCP
  - unreliable, datagrams：UDP
- specific protocols：
  - HTTP
  - SMTP, IMAP
  - DNS
  - MCP
  - P2P：BitTorrent, DHT（self study）
- video streaming, CDNs
- socket programming：TCP, UDP sockets

**使用场景：**  
复习 Application Layer 时用作总框架。

**使用方法 / 工作流程：**  
先判断应用架构和服务需求，再选择 transport service model，最后理解具体协议和 socket 实现。

**例子：**  
视频网站使用 client-server/CDN 架构，依赖 DNS 和 HTTP/DASH，底层通常使用 TCP；P2P 文件分发则用 peers 互相上传下载。
