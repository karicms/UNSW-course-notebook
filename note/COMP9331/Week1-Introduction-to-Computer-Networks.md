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

| 层 | 课件职责 | 典型协议/对象 |
| --- | --- | --- |
| Application | supporting network applications | HTTP、SMTP、FTP |
| Transport | process-to-process data transfer | TCP、UDP；segment |
| Network | source-to-destination datagram routing | IP；datagram |
| Link | data transfer between neighboring network elements | Ethernet、Wi-Fi；frame |
| Physical | bits “on the wire” | copper、fiber、radio；bits |

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
