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

Vanilla TCP 和 UDP sockets 本身不提供 encryption；把 cleartext password 写入普通 socket，会以明文穿越 Internet。TLS 可提供 encrypted TCP connections、data integrity 与 end-point authentication（课件称 TLS socket API 是 TCP socket API 的 enhancement）。在本课程的分层模型中，TLS 由 application layer 的 library/协议实现，并在其下使用 TCP：application 写入 TLS socket 的是 cleartext，TLS 加密后在 Internet 上传输的是 encrypted data。

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

`protocol` 可为 `http`、`https`、`ftp`、`smtp` 等；hostname 可是 DNS name 或 IP address；port 未写时使用协议 standard port，例如 HTTP `80`、HTTPS `443`。`directory-path` 是层级路径，例如 `/news/2026/`；传统 Web 中它常反映 server 的文件系统目录，但现代 Web 的 route 不一定对应真实目录。`resource` 才是该 URL 要定位/操作的目标资源，例如 `a.html`；两者合起来形成 URL path。

### 例子

`https://www.example.com:443/news/a.html` 指定 HTTPS、host、显式 port；其中 `/news/` 是 directory path，`a.html` 是 resource。`https://example.com/users/42` 中的 `/users/42` 也可能只是 application route，而不是服务器上真实存在的 `/users/42` 目录。

## HTTP 的 client-server、TCP 与 stateless 特性

### 定义

HTTP（HyperText Transfer Protocol）是 Web 的 application-layer protocol，采用 client/server model 且是 stateless。

### 解释

client 对 server port `80` 建立 TCP connection/socket；server accept connection 后交换 HTTP messages。Stateless 指 HTTP 本身没有靠多条 HTTP messages 完成一次 Web “transaction” 的 multi-step exchange state：各 HTTP requests 相互独立，client/server 不需要在协议层追踪它进行到哪一步，也无需对一个部分完成但最终未完成的 transaction 做协议级恢复。若 application 需要跨请求记住登录、购物车等 state，必须另行处理，例如使用 cookies。

### 例子

浏览器的 HTTP GET 得到 response 后，单独看下一次 GET，HTTP server 不会仅凭协议本身记得它先前做过什么。

## HTTP request message 与 methods

### 定义

HTTP request 是 client-to-server message；general format 由 request line、零或多个 header lines、空行和可选 entity body 组成。

### 解释

request line 是 `method SP URL SP version CRLF`，常见 request messages 用 ASCII、以 `CRLF` 分行。`GET` 取回 object，传给 server 的参数通常写在 URL 的 `?query` 中；`POST` 通常把 form input/data 放在 entity body；`HEAD` 只请求若以 GET 请求会返回的 headers，不取回 body；`PUT` 的 HTTP 语义是用 entity body 完整替换指定 URL 的 resource。要区分 HTTP method 的语义与 Spring Boot 等框架的实现：框架只按 method 映射 handler，业务代码可以让 `PUT` 做部分修改；但按 HTTP 语义，部分更新通常对应 `PATCH`。

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

这使 messages 较易 delineate、相对 human-readable，也避免字段编码/格式化的一些复杂性；同时 HTTP 可传输 variable-length data，body 可承载非文本 object。不同 header fields 通常没有顺序依赖，因此可按任意顺序出现；但 message 的整体结构有固定顺序：request line（或 response status line）→ headers → blank line → optional body，不能把 body 或空行随意放到 headers 前面。

### 例子

request header 的 `Host: example.com\r\n` 以 CRLF 结束；header 与 entity body 之间的空行标出 header 结束。

## Cookies 与 Web state

### 定义

Cookie 是 Web site 与 browser 用来在多次 stateless HTTP transactions 间维护 state 的机制。这正是在 HTTP stateless 后出现 “How to keep state?” 这个 challenge 的原因：HTTP 协议不记住先前 request，但 Web application 仍需要跨多个 requests 识别用户并保存状态。

### 解释

课件列出四组件：HTTP response 中的 `Set-cookie` header、后续 HTTP request 中的 `Cookie` header、用户 host 的 cookie file（browser 管理）、Web site 的 back-end database。server 创建 ID，browser 保存并在后续请求带回；HTTP messages 因而可携带 state identifier，让 site 恢复相应状态。

### 例子

首次访问商店时 server 返回 `Set-cookie: 8734` 并在 database 记录 ID；以后浏览器请求带 `Cookie: 8734`，站点据此恢复 shopping cart 或 authorization state。

## Cookies 的用途与隐私问题

### 定义

Cookie 既可支撑 personalization/state，也可能成为跨站追踪的标识符。

### 解释

课件列举 authorization、shopping carts、recommendations、user session state（Web e-mail）等用途；这些都是 Web application 必须跨 requests 保存 state 的情形。与此同时，cookies 使 sites 了解许多用户信息。third-party persistent cookies（tracking cookies）指网页嵌入的共同第三方（例如 ad network）设下、并在 browser 中持续保存的 cookie：当多个网站都加载该第三方资源时，第三方可在这些网站上看到同一个 cookie value，把不同网站上的访问关联为同一 browser/用户标识，形成 cross-site tracking。这带来隐私问题，因为该第三方能据此推断用户在多个网站的浏览/兴趣行为。

### 例子

网站 A 与 B 都嵌入同一广告网络资源时，广告服务器先在访问 A 时设下持久 `cookie ID=8734`；之后访问 B，browser 再向同一广告服务器带上 `ID=8734`。广告服务器于是可将两个站点访问关联为同一标识，即使 A 和 B 并不共享各自的 first-party cookie。

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


# COMP3331/9331 Week 3 - Application Layer Part 2

> 课件主线：DNS -> P2P（Self Study / NOT ON EXAM）-> Video Streaming & CDN -> MCP -> UDP/TCP Socket Programming。
>
> 以下内容依据 Week 3 `Application Layer (DNS, Video Streaming and CDN, MCP, Socket programming)` 课件整理。定义尽量使用规范语言；其余部分用直观语言说明。

---

## 1. DNS（Domain Name System）

### 1.1 DNS 的作用与设计目标

**标准规范定义**

DNS 是由层次化 name servers 实现的分布式数据库，以及供 hosts 与 name servers 进行 name resolution 的应用层协议。它提供 hostname 与 IP address 的映射，也支持别名、邮件服务器别名和负载分配。

**通俗解释**

人会记住 `www.unsw.edu.au`，但 IP datagram 必须送往 IP address。DNS 就像 Internet 的电话簿：把容易记的名字翻译成机器能路由的地址。它虽是 Internet 的核心功能，却放在 application layer：复杂的名字管理留在网络边缘的应用与服务器中，而不是塞进每个 router。

早期 Internet 用集中维护的 `hosts.txt` 保存所有名称和地址；Internet 变大后，维护者扛不住更新量、名字会冲突、每台机器还会保存旧副本。DNS 用分布、分层的方式解决这些问题。

**工作原理 / 使用方法**

应用从 URL 得到 hostname 后，触发 DNS lookup；得到 IP 后才建立后续 HTTP、TCP 等通信。DNS 还可让一个名字对应多个 IP，使请求分散到多台 replicated Web server。

**使用场景 / 为什么使用**

若用一台 central DNS，会出现 single point of failure、巨大的 traffic volume、远距离访问、维护困难和无法扩展的问题。DNS 的目标是：名称唯一、可扩展、分布且可自治管理、高可用、查询快。

**具体例子**

浏览器输入 `www.example.com`，先解析出如 `93.184.216.34`，再对这个地址发起连接；用户不必记住数字地址。

### 1.2 DNS 的三层“Hierarchy”

**标准规范定义**

DNS 同时具有三种互相关联的层次：hierarchical namespace、hierarchical administration，以及 distributed hierarchy of servers。

**通俗解释**

三者不要混淆：namespace 说明名字怎样组成；administration 说明哪一块由谁负责；server hierarchy 说明查询时服务器怎样分工。

**工作原理 / 使用方法**

1. **Hierarchical namespace**：完整域名是从 leaf 到 root 的路径。例如 `instr.eecs.berkeley.edu.` 中，`.edu`、`berkeley`、`eecs`、`instr` 越往左越具体。domain 是树的 subtree，深度可变（课件指出上限为 128）。每个 domain 管自己的子树，因此命名冲突容易避免。
2. **Hierarchical administration**：zone 是 DNS name space 中由一个 administrative authority 管理的连续部分。一个机构可把子域委派出去，例如 Berkeley 管理 `*.berkeley.edu`，EECS 管理 `*.eecs.berkeley.edu`。
3. **Server hierarchy**：Root servers 在顶层；TLD servers 负责 `.com`、`.edu`、`.au` 等；authoritative DNS servers 保存某个 domain 的最终 resource records。每台 server 只存总数据库的一小部分，并通过 delegation 找到其他部分的负责者。

**使用场景 / 为什么使用**

全球名字与记录不可能由一个组织、一个数据库统一维护。层次让不同学校、公司、注册机构可以独立更新自己的记录，同时全世界仍能沿树查到答案。

**具体例子**

查询 `gaia.cs.umass.edu` 时，root 知道 `.edu` 在哪里，`.edu` TLD 知道 `umass.edu` 的 authoritative server 在哪里，而该 authoritative server 知道 `gaia.cs.umass.edu` 的地址。

### 1.3 Root、TLD、Authoritative 与 Local DNS

**标准规范定义**

Root name servers 是无法解析名称时的最终联系人；TLD servers 对 top-level domains 负责；authoritative name servers 为其受权 domain 保存 authoritative hostname-to-IP mappings。Local DNS server（default name server）通常由 ISP、公司或学校提供，不严格属于该层次结构。

**通俗解释**

Root 不必知道每个网站 IP，它主要知道“`.edu` 该问谁”；TLD 主要知道“`unsw.edu.au` 或 `umass.edu` 的负责人是谁”；authoritative server 才接近最终答案。你的电脑一般不亲自一层层找，而是先把问题交给 local DNS；它像代理和缓存管理员。

**工作原理 / 使用方法**

主机通过配置协议（如 DHCP）获知 local DNS。应用调用如 `gethostbyname()` 触发查询，local DNS 先查自己的 cache；没有结果就转入 DNS hierarchy。Root 有 13 个 logical root server names，但每个被复制到许多实际位置；所有 DNS server 都知道 root 的位置。

**使用场景 / 为什么使用**

local DNS 减少每台主机的复杂度，并共享缓存。authoritative servers 可由组织自己维护，也可交给 service provider；root/TLD 则提供全局查找入口和委派信息。

**具体例子**

NYU 的主机 `engineering.nyu.edu` 想查 `gaia.cs.umass.edu`，先问 `dns.nyu.edu`，再由后者按缓存或 hierarchy 查找。

### 1.4 Iterative query 与 Recursive query

**标准规范定义**

在 iterative query 中，被询问 server 若不能给出最终结果，会返回下一台应联系的 server；请求方继续查询。在 recursive query 中，被询问 server 承担完成后续 name resolution 并返回最终结果的责任。

**通俗解释**

区别只在“谁继续跑腿”。Iterative 是“我不知道最终答案，但下一站去问它”，请求方接着问；recursive 是“你替我查到底，再把答案给我”。它们不是整个 DNS 系统只能二选一：客户端到 local DNS 常以“请给最终答案”的方式请求，而 local DNS 到 root/TLD/authoritative 往往使用 iterative referrals。

**工作原理 / 使用方法**

对 `gaia.cs.umass.edu`：

```text
Iterative: local DNS -> Root -> 得到 .edu referral
           local DNS -> .edu TLD -> 得到 umass.edu referral
           local DNS -> authoritative -> 得到 IP

Recursive: 请求传给被联系的 server；该 server 继续向下查询，
           最终答案沿链路返回。
```

**使用场景 / 为什么使用**

recursive 让最初请求者简单，但若 root/TLD 都为大量请求递归查到底，上层负担会很重。iterative 把继续查询工作留给 resolver，令上层主要做 referral，较易扩展。

**具体例子**

Root 对 iterative request 可回答：“我不知道 `gaia` 的 IP，但请问 `.edu` TLD。”它不需要自己替请求者问完所有后续服务器。

### 1.5 Caching、TTL 与更新

**标准规范定义**

DNS server 一旦学习到 mapping，可将其缓存；cache entry 在 TTL（Time To Live）到期后失效。DNS dynamic update/notify 的相关标准包括 RFC 2136。

**通俗解释**

如果每次查 `google.com` 都从 root 开始，会非常浪费。cache 就是“刚查过，先记住”。TTL 是记录的保鲜期：它避免旧 IP 永远流传，但也意味着更换 IP 后，Internet 各处会在 TTL 尚未到期的一段时间里继续看到旧值。DNS 是 best-effort name-to-address translation，不保证瞬间全网一致。

**工作原理 / 使用方法**

local DNS 特别常缓存 TLD 信息，因此 root 并不常被访问。也可做 negative caching，暂时记住不存在的结果，避免反复查询 `www.cnn.comm`、`www.cnnn.com` 之类拼错的名字。计划换地址时，课件建议：记录旧 TTL -> 先把 TTL 降低（如 30 秒）-> 等待旧 TTL 完成 -> 更新记录 -> 等待传播 -> 把 TTL 恢复。

**使用场景 / 为什么使用**

缓存换来更快响应和更少上游流量；TTL 则在性能与更新速度之间取平衡。临近迁移前临时降低 TTL，可减少用户被旧地址导向错误位置的时间。

**具体例子**

`www.example.com` 从旧服务器迁到新 IP：若旧 TTL 为一天，先等待/降低 TTL，再切换记录；否则有些用户可能一天内仍访问旧机器。

### 1.6 Resource Records（RR）：A、NS、CNAME、MX

**标准规范定义**

DNS distributed database 以 resource record 存储数据，格式为 `(name, value, type, ttl)`。课件重点 record types 为 A、NS、CNAME 和 MX。

**通俗解释**

RR 就是一条有类型的“DNS 事实”。type 告诉你这条记录在回答什么：地址、谁负责一个域、别名真正叫什么，还是邮件该投递给谁。

**工作原理 / 使用方法**

- **A**：`name` 是 hostname，`value` 是 IPv4 address；用于 hostname -> address。
- **NS**：`name` 是 domain，`value` 是这个 domain 的 authoritative name server hostname；用于 delegation。
- **CNAME**：`name` 是 alias，`value` 是 canonical（真实）name；得到 canonical name 后通常还要继续查其地址。
- **MX**：`value` 是与 `name` 关联的 mail server；邮件系统借它决定应将邮件交给哪台服务器。

**使用场景 / 为什么使用**

A 供 Web 等网络连接使用；NS 让层次查询能继续；CNAME 让公开名称与后端机器名称分离；MX 让一个 domain 的 Web 与 mail 服务可分别部署。

**具体例子**

`(www.example.com, 93.184.216.34, A, TTL)`；`(foo.com, dns1.foo.com, NS, TTL)`；`www.ibm.com` 可 CNAME 到 `servereast.backup2.ibm.com`；向 `mahbub@unsw.edu.au` 发送邮件时需要查询 `unsw.edu.au` 的 MX record。

### 1.7 DNS message、插入/更新、可靠性与安全

**标准规范定义**

DNS query 与 reply 使用同一基本 message format：header、questions、answers、authority、additional information。header 含 16-bit identification、flags、各 section 的数量；reply 使用与 query 相同的 identification。DNS 常用 UDP port 53，也支持 TCP。

**通俗解释**

ID 是查询的“单号”，让客户端知道哪个 reply 对应哪个 query；question 写“我要查什么”；answer 放结果；authority 给相关 authoritative server；additional 放有帮助的附加资料。DNS 虽通常跑在 UDP 上，但不能假装 UDP 永不丢包，因此 resolver 自己要超时重试。

**工作原理 / 使用方法**

新建 `networkuptopia.com` 时，向 registrar 注册并提供 primary/secondary authoritative server。registrar 在 `.com` TLD 写入 NS record 和对应 name server 的 A record；组织自己的 authoritative server 再保存 `www.networkuptopia.com` 的 A record 和 domain 的 MX record。为可靠性，DNS servers 采用 primary/secondary replication；至少一个 replica 可用即可服务，查询也能负载分担。UDP 超时后可试 alternate servers；重试同一 server 采用 exponential backoff，且同一 logical query 使用相同 identifier，收到任一 server 的有效回复即可。

**使用场景 / 为什么使用**

这些机制让一个 domain 可以上线、迁移、在部分 server 故障时继续被解析。安全上 DNS 会暴露访问日志，也可能受到审查；cache poisoning 的攻击者会试图把假的 mapping 混入 cache。课件的防护原则是：不要缓存非 authoritative server 提供的 IP mapping。DNSSEC 提供 authentication 与 message integrity。DoT（TLS，port 853）和 DoH（HTTPS/HTTP2，port 443）提高隐私与安全，但课件明确标注 **DoH/DoT NOT ON EXAM**。

**具体例子**

攻击者控制 `drevil.com` 的 DNS 时，不能借回答 `www.drevil.com` 偷塞一条 `google.com -> attacker IP` 而让 resolver 缓存；只应接受 `google.com` authoritative data。

---

## 2. P2P 与 BitTorrent（Self Study / NOT ON EXAM）

### 2.1 P2P architecture 与文件分发

**标准规范定义**

Peer-to-peer architecture 没有 always-on server；任意 end systems（peers）直接通信，既从其他 peers 请求服务，也向其他 peers 提供服务。课件明确标注本节 **Self Study / NOT ON EXAM**。

**通俗解释**

client-server 像所有人都从同一个仓库领文件；P2P 像先领到的人也开始帮忙分发。新 peer 的到来确实增加需求，但也带来 upload capacity，这叫 self scalability。代价是 peers 会上下线、IP 会变，系统管理更复杂。

**工作原理 / 使用方法**

向 N 个 peers 分发大小为 F 的文件时，client-server 至少受 `NF/us` 与最慢下载者 `F/dmin` 限制：`Dcs > max{NF/us, F/dmin}`。P2P 的 server 至少上传一份，所有 peers 的 upload 也加入供应：`Dp2p > max{F/us, F/dmin, NF/(us + sum ui)}`。因此人数增加时 P2P 仍增长，却多出 peer 上传能力来对冲。

**使用场景 / 为什么使用**

适合大量节点共享大文件或分散服务能力的场景，例如 BitTorrent、P2P streaming、VoIP、cryptocurrency；不适合需要简单集中控制的服务。

**具体例子**

1000 人下载同一 ISO：client-server 要由源站发送 1000 份；P2P 中先下载到 chunks 的用户立即上传 chunks 给后来者。

### 2.2 BitTorrent

**标准规范定义**

BitTorrent 是 P2P file-distribution protocol。文件被分成 256 KB chunks；参与同一文件交换的 peers 构成 torrent，tracker 追踪参与 peers。本节同样 **Self Study / NOT ON EXAM**。

**通俗解释**

Alice 加入 torrent 时起初什么都没有。她向 tracker 获取 peers 列表、连接一部分 neighbours；下载的同时也上传。大家各自拥有不同 chunks，整个群体像拼图互换。peer 可能随时加入离开（churn）；下完可离开，也可留下做 seed。

**工作原理 / 使用方法**

Alice 定期向 neighbours 获取它们的 chunk list，再按 **rarest first** 请求自己缺少且最稀少的 chunks，避免某块只在一人手里而消失。发送侧采用 tit-for-tat：Alice 向当前以最高速率向她上传的四个 peers 上传，约每 10 秒重估；约每 30 秒随机 optimistic unchoke 一个 peer，借此发现更好的交换伙伴并让新 peer 有机会开始交换。

**使用场景 / 为什么使用**

rarest first 提升文件完整性与可用性；tit-for-tat 抑制只下载不上传的 free-riding，并奖励贡献 upload 的 peers。

**具体例子**

Bob 被 Alice optimistic unchoke 后，开始高速回传；Bob 进入 Alice 的 top-four，Alice 也进入 Bob 的 top-four，双方获得更快下载速度。

---

## 3. Video Streaming 与 CDN

### 3.1 Video coding、spatial/temporal coding、CBR/VBR

**标准规范定义**

数字视频是以固定速率显示的 image sequence；每张 image 是由 bits 表示的 pixel array。video coding 利用图像内 spatial redundancy 与相邻帧间 temporal redundancy 减少编码所需 bits。CBR（constant bit rate）采用固定 encoding rate；VBR（variable bit rate）随空间/时间编码量变化而改变 rate。

**通俗解释**

若一大片像素都是绿色，不必逐个发送“绿色”，可以说“绿色重复 N 次”——这是空间冗余。下一帧只有球移动时，不必再发送整张背景，只发送变化的部分——这是时间冗余。画面平静时 VBR 花得少；动作、烟花、镜头切换多时 VBR 需要更多 bits。CBR 则像始终按固定速度出货。

**工作原理 / 使用方法**

编码器选择压缩方法和 bit rate。课件示例：MPEG-1 约 1.5 Mbps、MPEG-2 约 3-6 Mbps、MPEG-4 常用于 Internet，约 64 Kbps-12 Mbps。服务端通常保存同一视频的多种 encoded versions，为自适应 streaming 准备。

**使用场景 / 为什么使用**

compression 降低带宽和存储需求。CBR 容易规划资源；VBR 更能把 bits 用在复杂画面上，但瞬时速率会变化，需要 buffering/adaptation 来吸收波动。

**具体例子**

新闻主播静止背景的帧可以只发送很少变化；足球比赛快速移动和切镜头时，VBR 会选择更高 rate 维持画质。

### 3.2 DASH（Dynamic, Adaptive Streaming over HTTP）

**标准规范定义**

DASH 是 Dynamic, Adaptive Streaming over HTTP。server 将视频分成多个 chunks，每个 chunk 保存为多个 encoding rates；manifest file 提供不同 chunks/versions 的 URLs。client 周期性测量 server-to-client bandwidth，逐块请求并选择当前可持续的最高 coding rate。

**通俗解释**

它不是开播前就选定“全程 1080p”。播放器不断看当前网络情况：网络好就取高质量 chunk；网络差就改取低质量 chunk。切换发生在 chunk 边界，观众尽量少看到卡顿。

**工作原理 / 使用方法**

client 读取 manifest -> 测量可用带宽 -> 一次请求一个 chunk -> 依据测量选择最高可持续 rate -> 再次测量并可在之后的 chunks 改变 rate。client 还决定何时请求，避免 buffer starvation（播到没内容）或 overflow；并可选择靠近自己或带宽更好的 URL/server。Streaming video 可概括为：**encoding + DASH + playout buffering**。

**使用场景 / 为什么使用**

用户可能在有线、Wi-Fi、移动网络间切换，带宽差异大。DASH 用普通 HTTP 交付，且在画质与连续播放之间动态平衡。

**具体例子**

地铁中带宽下降时，播放器把下一段从 4 Mbps 版本改为 800 Kbps；出站 Wi-Fi 恢复后，再提高后续 chunk 的质量。

### 3.3 CDN（Content Distribution Network）

**标准规范定义**

CDN 是把内容副本存储并提供于多个地理分布 CDN nodes 的 application-level distribution infrastructure。

**通俗解释**

单一 mega-server 会成为 single point of failure、出口拥塞点，并让远方用户走很长的网络路径；向大量用户重复发送同一视频也无法扩展。CDN 把副本放到用户附近，让用户从合适的副本拿内容。

**工作原理 / 使用方法**

两种课件部署思路：**enter deep**，把大量 CDN servers 放进许多 access networks，离用户很近；**bring home**，把较少数量但更大的 clusters 放在接近 access networks 的 POPs。订阅者请求内容后，可被导向 nearby copy；若路径拥塞，也可以选不同 copy。

**使用场景 / 为什么使用**

适合数百万视频、数十万并发用户的流媒体和大型网站。OTT（over the top）把 Internet host-to-host communication 当服务使用，仍需面对拥塞、选择哪个 node、用户遇拥塞时如何观看、以及内容放置在哪些 nodes 等问题。

**具体例子**

Netflix 将 `Mad Men` 的副本放到多个 CDN nodes；澳洲用户不必每次跨洋访问美国 origin，而从合适的本地/近端 node HTTP streaming。

### 3.4 CDN + DNS 与 Netflix case

**标准规范定义**

CDN provider 的 authoritative DNS 可将每个 CDN object query 映射到合适的 CDN server；这种映射常结合 CNAME、请求者位置或网络路径状态完成。

**通俗解释**

DNS 在这里不只是“域名换 IP”，还是调度入口。用户觉得自己在访问 `netcinema.com`，但该站 DNS 可以返回指向 `KingCDN.com` 的 CNAME；再由 KingCDN 的 authoritative DNS 选择更适合该用户的 CDN node。

**工作原理 / 使用方法**

Bob 从网页取得 `http://netcinema.com/6Y7B23V` -> local DNS 解析 -> netcinema authoritative DNS 返回 KingCDN 的 CNAME -> KingCDN authoritative DNS 选择 CDN server -> Bob 向该 server 用 HTTP 请求视频并获得 stream。

Netflix 案例：视频的 multiple versions 预先上传 CDN servers；Bob 处理 Netflix account/browsing，取得某内容的 manifest file；选择 DASH server/合适 CDN server；streaming 开始。这里 DASH 决定版本，CDN/DNS 帮助决定从哪里取。

**使用场景 / 为什么使用**

它把“内容本身怎么自适应（DASH）”与“内容从哪个地点交付（CDN + DNS）”分开处理，因此能在全球用户、不同网络条件下扩展。

**具体例子**

同一位用户的播放可能从 Sydney CDN node 取高码率 chunk；路径拥塞后 DNS/CDN 或播放器改选其他适合 node，而 DASH 同时把下一 chunk 降码率。

---

## 4. MCP（Model Context Protocol）

### 4.1 MCP、Host/Client/Server 与 RPC

**标准规范定义**

MCP 是开放的 application-layer protocol，用于连接 AI hosts 与 external context/tools，并标准化 messages 与 discovery。它不定义 LLM、UI 或 backend logic。MCP architecture 包括 host、MCP clients 和 MCP servers；host 包含 UI、LLM、orchestration、consent policy，并在 servers 之间实施边界。

RPC（Remote Procedure Call）是让一个 process 调用另一 process 中 procedure 的通信模式。RPC request 含 method、parameters、id；response 返回 result 或 error，并以相同 id 关联。

**通俗解释**

MCP 像 AI 与“文件、天气、数据库、工具”之间统一的插座标准；模型本身不是 MCP 的一部分。host 负责决定能连什么、用户是否同意、模型如何编排；一个 host 可以为不同 server 建多个 client。RPC 则让 `get_weather("Sydney")` 看起来像函数调用，但实际会跨进程/网络，因此可能延迟或没有响应。

**工作原理 / 使用方法**

host 中的 MCP client 与 local-files server、remote-weather server 等分别连接。不要误以为 one call = one packet：网络中的一次 RPC 是 messages 的语义，底层可能被传输协议分段、延迟或失败。

**使用场景 / 为什么使用**

统一 discovery 与 tool/context access 能减少每个 AI application 对每个外部系统各写一套私有接口的成本；host policy 也能集中执行 consent 与权限边界。

**具体例子**

AI host 通过一个 MCP client 读本地文件，通过另一个 client 调 remote weather server；weather server 不替 host 决定用户是否同意调用。

### 4.2 JSON 与 JSON-RPC

**标准规范定义**

JSON 是以文本表示 structured data 的语法：object 用 `{}` 包围，成员为 `"name": value`，name 与 string value 使用双引号；value 可为 numbers、true、false、null、arrays 或 objects。JSON-RPC 定义 request/response rules，而 JSON 仅定义文本语法。

**通俗解释**

JSON 像一张有字段名的文本表单；JSON-RPC 规定怎样把表单说成“调用方法”、怎样带回“结果/错误”。`id` 就是凭证号码：success 或可读的 error 都重复 request 的 id；notification 则没有 id，也不需要 JSON-RPC response。

**工作原理 / 使用方法**

典型请求：

```json
{"jsonrpc":"2.0","id":7,"method":"get_weather","params":{"city":"Sydney"}}
```

response 用同一 `id: 7` 返回 `result` 或 `error`。例如订阅通知 `notifications/tools/list_changed` 没有 id。

**使用场景 / 为什么使用**

结构化、可机器处理且易读的 messages 适合 client-server tool calls；id 在多个请求、延迟与错误存在时仍能正确配对。

**具体例子**

`tools/list` 的 request 是 id 7；成功 reply 也带 id 7。若 params 无效，error reply 仍带 id 7 与 error code/message。

### 4.3 MCP state、primitives 与 bindings

**标准规范定义**

课件中的 current MCP 是 stateless：每个 request 提供 protocol version、相关 client capabilities、client information 和 method-specific parameters。`server/discover` 由 server 必须支持，client 可先调用，用于返回 versions、capabilities 和通常的 self-reported identity。MCP 的三种 primitives 为 prompts、resources、tools。

**通俗解释**

stateless 表示 server 不应只依赖“你上一句话说过什么”；每次请求要带足够的协商资料。三种 primitive 可记成：prompts 是可重用的提问模板，resources 是只读资料，tools 是能执行动作的函数。host policy 决定 model 是否能选择/调用 tool，不是 tool 自己越权决定。

**工作原理 / 使用方法**

- **Prompts**：user-controlled reusable message templates；`prompts/list`、`prompts/get`。
- **Resources**：以 URI 标识的 read-only contextual data；`resources/list`、`resources/read`。
- **Tools**：executable functions；`tools/list`、`tools/call`。

两种标准 binding 具有相同 methods/semantics：

- **stdio（local）**：host launch subprocess；client/server messages 是 newline-delimited UTF-8 JSON-RPC，经 stdin/stdout；logs 写 stderr；可用 `notifications/cancelled` 取消。
- **Streamable HTTP（typically remote）**：一个 MCP endpoint；每个 client message 是 HTTP POST；request 收 JSON 或 request-scoped SSE（Server-Sent Events）；关闭 SSE response stream 可取消。

**使用场景 / 为什么使用**

stdio 适合本机随 host 启动的 server，例如 local files；Streamable HTTP 适合远程服务，例如 weather。统一 binding 语义让同一个 tools/list / tools/call 逻辑不因部署地点而改变。

**具体例子**

HTTP MCP flow：client `server/discover`（id 1）取得 versions/capabilities -> `tools/list`（id 2）得到 `get_weather` 与 JSON Schema -> host 内 LLM 选择工具 -> `tools/call get_weather`（id 3）-> server 以 id 3 返回结果。每个 client request 使用独立 HTTP POST；这绝不意味着“一次 MCP request 恰好是一个 IP packet”。

---

## 5. Socket 与 UDP/TCP Socket Programming

### 5.1 Socket 的概念

**标准规范定义**

Socket 是 application process 与 end-to-end transport protocol 之间的 interface（“door”）。application developer 控制 application 与 socket 的使用，OS 控制 transport、network、link、physical layers。

**通俗解释**

两个应用不能直接把数据塞进网络；它们把数据交给 socket，OS 再用 TCP 或 UDP 运送。socket 不是整个网络，而是程序通向 transport service 的门。

**工作原理 / 使用方法**

client 与 server 创建 sockets；server 在约定 port 等待，client 指定 server IP 与 port。具体的 send/receive、connection、reliability 行为取决于选 TCP 或 UDP。

**使用场景 / 为什么使用**

socket programming 是构建 client/server applications 的基础。它把应用逻辑与 OS 提供的传输能力分开。

**具体例子**

天气 client 向 server 的 port 发送请求；server 从 socket 收到 bytes、处理后经 socket 回应。

### 5.2 UDP socket programming

**标准规范定义**

UDP 是 connectionless transport：发送前没有 handshake。sender 为每个 packet 显式附带 destination IP address 和 port number；receiver 从收到的 packet 取出 sender IP 与 port。UDP 提供不可靠的、以 segments 为单位的 bytes transfer，数据可能丢失或乱序。

**通俗解释**

UDP 像寄明信片：每封都写收件地址，不先建立专属通道；寄出后不保证一定到、也不保证先寄的先到。优点是简单、开销小、可立即发送。

**工作原理 / 使用方法**

UDP client pseudocode：create socket -> loop（send UDP segment 到已知 server IP/port；receive response）-> close。UDP server：create socket -> bind 到特定 port -> loop（receive client X segment；从 message 取得 client IP/port；send reply 给 client X）-> close。

**使用场景 / 为什么使用**

适用于应用可以接受/自行处理丢失、乱序，且希望避免 connection setup 的情形；若应用需要可靠性，必须在 UDP 之上自行实现 timeout、sequence number、retransmission 等逻辑。

**具体例子**

Ping-style UDP client 给 server 发含 sequence number 的 datagram，等待一段时间；没收到 reply 就记录 timeout，而不是假设 OS 会自动重传。

### 5.3 TCP socket programming

**标准规范定义**

TCP 是 connection-oriented transport。client 创建 socket 并指定 server IP/port，TCP 建立 connection；从 application viewpoint，TCP 在 client 与 server 间提供 reliable、in-order byte-stream transfer。server 对每个被接受的 client connection 创建新的 connection socket。

**通俗解释**

TCP 像先接通电话再持续说话：先建立关系，再按顺序可靠地传输 bytes。server 有一个 welcoming socket 专门接客；每接到一个 client，创建一个 connection socket 专门和该 client 通话，因此能同时服务多个 clients。source port numbers 帮助区分 clients。

**工作原理 / 使用方法**

TCP client：create `ConnectionSocket` -> active connect（指定 server IP/port）-> 在 `ConnectionSocket` read/write -> close。TCP server：create `WelcomingSocket` -> bind 特定 port -> listen/register willingness -> loop（accept new connection 得到 `ConnectionSocket`；read/write；close connection socket）-> 最后 close welcoming socket。

**使用场景 / 为什么使用**

适合需要完整、有序 byte stream 的应用，例如多数 Web、登录、文件传输与 request/response service。相较 UDP，TCP 增加 connection setup 与 transport-layer reliability 的机制。

**具体例子**

Web client 连到 server 的 443 port；server 的 welcoming socket `accept()` 后为该 client 创建独立 connection socket，HTTP bytes 可可靠、有序地在此连接中读写。

---

## Week 3 总结链路

```text
用户输入 hostname
  -> DNS hierarchy / local DNS / cache 解析名字
  -> CNAME + CDN authoritative DNS 选择适合的 content node
  -> CDN 用 HTTP 交付视频
  -> DASH 逐 chunk 依带宽选择 encoding rate

AI host 要访问外部 context/tools
  -> MCP client/server
  -> JSON-RPC messages over stdio or Streamable HTTP

应用要直接通信
  -> socket
  -> 需要可靠有序 stream 时用 TCP；可接受不可靠 datagram 时用 UDP
```


---

# Week 4 - Transport Layer Part 1

> **课程：** COMP3331 / COMP9331 Computer Networks and Applications  
> **Reading Guide：** Chapter 3, Sections 3.1-3.5.2  
> **Major Concepts：** Multiplexing-demultiplexing、Checksum、Reliable data transfer

## 本讲路线与提醒

本讲从 transport-layer services 出发，依次讨论 multiplexing/demultiplexing、connectionless transport (UDP)、reliable data transfer 的基本原理，最后开始介绍 connection-oriented transport (TCP)。后续才会深入 flow control、congestion control 和 TCP 的连接管理。

> **老师明确说明：** 教材会用 finite state machines（FSM）描述 rdt 的 sender/receiver；**本课不使用 FSM，考试也不会考 FSM 题目**。理解事件、状态变化与动作的逻辑即可。

---

## 1. Transport-layer services

### 1.1 端到端的逻辑进程通信

**① Definition（定义）**  
Transport layer protocols provide **logical communication between application processes running on different hosts**。Internet 可供应用使用的两种 transport protocol 是 **TCP** 和 **UDP**。

**② 通俗解释**  
network layer 的工作是把资料大致从一台主机送到另一台主机：可把它看成给 transport layer 的 `sendtohost(data, host)` 服务。它通常采用 best-effort delivery，不承诺可靠送达、固定路径，也不替应用协调发送速率。transport layer 在两端主机（通常在 OS kernel）运行，在这个不完美的基础上，为**两个应用进程**建立像直接沟通一样的抽象。

不要把「logical」误解成一条真实的专线：数据仍经过很多 router 和 network link；只是两端的应用感觉自己在端到端交换资料。

**③ How it works（发送端与接收端）**

```text
发送进程的 application message
    -> transport：决定 header fields、加 transport header、形成 segment
    -> IP / network layer
    -> 网络（best effort）
    -> 接收端 IP
    -> transport：检查 header、取出 message、按 socket demultiplex
    -> 正确的接收进程
```

- Sender：将 application message 切分/封装为 segments，交给 network layer。
- Receiver：从 IP 收到 segment，检查 header，重组或抽取 application message，经 socket 向上交付。

**④ 为什么需要**  
一台 host 可同时运行 browser、DNS、游戏、server 等很多 process。IP 只到 host；transport layer 必须进一步把资料送到正确 process，并按协议提供可靠性、顺序、流量/拥塞控制等能力。

### 1.2 TCP 与 UDP

| Protocol | PPT 所列服务 | 直观理解 |
|---|---|---|
| **TCP** | reliable, in-order delivery；congestion control；flow control；connection setup | 先建立 connection，再提供可靠、有序的 byte stream |
| **UDP** | unreliable, unordered delivery；no-frills extension of best-effort IP | 不握手，每个 datagram 独立处理，尽快交给网络 |

两者都**不提供** delay guarantee 或 bandwidth guarantee；例如「使用 TCP」不等于一定低延迟或一定有多少带宽。

---

## 2. Multiplexing and demultiplexing

### 2.1 核心概念

**① Definition（定义）**  
**Multiplexing** at a sender handles data from multiple sockets and adds a transport header. **Demultiplexing** at a receiver uses header information to deliver received segments to the correct socket。

**② 通俗解释**  
网络是共享资源，它不认识 browser、DNS 或 socket。发送端要把多个应用的资料合流到 IP；接收端则要根据「信封」上的地址和 port，把到达同一台 host 的资料分给正确应用。port 就是 host 内的进程入口编号。

**③ How it works**

```text
P1 socket --\
P2 socket ----> sender multiplexing -> [IP + TCP/UDP header + payload] -> IP
P3 socket --/

IP datagram 到达 host
    -> receiver reads source/destination IP and ports
    -> demultiplexing
    -> matching socket -> correct process
```

一个 TCP/UDP segment 都有 source port 与 destination port；IP datagram 还带 source/destination IP address。header 的值就是 demux 的依据。

**④ 使用场景**  
浏览器和 DNS client 可以同时使用网络而不混线；一台 web server 也能同时为大量 clients 服务。

### 2.2 UDP connectionless demultiplexing

**① Definition（定义）**  
UDP socket 的接收 demultiplexing 使用 **destination IP address 与 destination port number**；实际课堂总结为 destination IP/port（port 是关键 socket 标识）。具有相同 destination port、但 source IP/port 不同的 UDP datagrams，会被导向**同一个**接收 socket。

**③ How it works**  
创建 socket 时，应用指定本地 port，或让 OS 随机选择可用 port：

```java
DatagramSocket serverSocket = new DatagramSocket(6428);
```

发送 UDP datagram 时，应用必须指定 destination IP address 与 destination port。server 收到后只看目的端这一侧来选 socket；它不会因为来自不同 client 而自动各建一个 connected socket。

**⑤ 具体例子与 Quiz**  
若 100 个 client 同时以 UDP 向 server 的 port 6428 通信，每个 client 有一个 socket，server 只需一个绑定 6428 的 UDP socket。因此答案是 **1, 1（server, each client）**。

### 2.3 TCP connection-oriented demultiplexing 与 TCP sockets

**① Definition（定义）**  
A TCP socket is identified by a **4-tuple**：

```text
(source IP address, source port number, destination IP address, destination port number)
```

receiver 用四个值将 segment 导向对应 socket。

**② 通俗解释**  
同一个 server 的 port 80 可以同时服务很多 clients。目的 port 都是 80 并不冲突，因为每个 client 的 source IP/port 不同，形成不同 4-tuple，因而映射到不同 connection socket。

**③ How it works：welcoming socket 与 connection socket**

```text
server process
  welcoming socket (port X)
       <- TCP handshake from client 1 -> connection socket 1 (port X)
       <- TCP handshake from client 2 -> connection socket 2 (port X)
```

server 的 welcoming socket 等待 connection request；每个已建立 TCP connection 有自己的 socket，所有这些 server-side sockets 可使用相同的 server port X，但 4-tuple 不同。

**⑤ Quiz 答案**

- 100 个 client 同时连 traditional HTTP/TCP web server：active sockets 是 **101, 1**（server 有 1 welcoming socket + 100 connection sockets；每个 client 1 个）。
- server 的 TCP sockets 是否有同一个 server-side port number？**Yes**。它们依然由完整 4-tuple 区分。

> **补充理解：port scanning。** PPT 提醒 server 在 open ports 等待请求；攻击者可能用 Nmap、Superscan 等扫描 open/closed/unreachable ports，再针对已知服务漏洞攻击。小于 1024 的 ports 保留给 well-known apps；例子有 MS SQL server UDP 1434、NFS TCP/UDP 2049，以及 Slammer worm 利用 SQL Server buffer overflow。此处重点是理解「开放 port 会暴露服务」，不是扫描技巧。

---

## 3. Connectionless transport: UDP

### 3.1 UDP 的特性与适用情形

**① Definition（定义）**  
UDP (User Datagram Protocol, RFC 768) is a connectionless, best-effort, bare-bones Internet transport protocol. UDP sender 和 receiver 之间没有 handshaking；each UDP segment is handled independently。

**② 通俗解释**  
UDP 很像把一张张明信片直接投入邮筒：不先建 connection、没有顺序保证、可能丢失。它的价值不是「更可靠」，而是简单、没有 setup RTT、没有连接状态，而且没有 congestion control，所以应用可按自己希望的速度发送（即使网络拥塞，仍可运作）。

**③ Header 与动作**

```text
0                 15 16                31
+-------------------+-------------------+
| source port        | destination port  |
+-------------------+-------------------+
| length             | checksum          |
+-------------------+-------------------+
| application data (payload, variable)  |
+---------------------------------------+
```

- `length`：整个 UDP segment（header + data）的 bytes 数。
- Sender：收 application message -> 决定 UDP header fields -> 创建 UDP segment -> 交给 IP。
- Receiver：从 IP 收 segment -> 检查 UDP checksum -> demultiplex 到 socket -> 交付应用。

**④ 使用场景**  
PPT 列出 streaming multimedia（容忍 loss、对 rate 敏感）、DNS、SNMP、HTTP/3；也常见于 latency-sensitive/time-critical 的 DNS、DHCP、SNMP、RIP routing updates、voice/video chat 与 FPS gaming。对这些应用，迟到的旧资料有时比丢掉资料更没用。

若 application 仍要 reliability，例如 HTTP/3，必须在 **application layer** 增加所需 reliability 与 congestion control，而不是 UDP 本身提供。

### 3.2 Internet checksum

**① Definition（定义）**  
UDP checksum is used to detect errors such as flipped bits in a transmitted segment. Sender 将 UDP segment 内容（UDP header fields、data，以及相关 IP addresses）视作一串 16-bit integers，作 **one's-complement sum**，并将 checksum 写入 UDP checksum field；receiver 重算并比对。

**③ How it works：one's complement 与 wraparound**

1. 将内容按 16-bit words 切分。
2. 逐个做 one’s-complement addition。
3. 若最高位有 carry-out，将该 carry **wrap around** 加回低 16 bits。
4. 对最终 sum 逐位取反，得到 checksum。
5. receiver 以同样方法计算：不相等则检测到 error；相等表示未检测到 error。

**④ 局限**  
checksum 是**弱保护**，不是「相等就绝对没有错」。某些不同 bit flips 的变化可使和不变，因而 checksum 不变；所以 equal 只表示没有侦测到错误。

**Pseudo-header（PPT 的实践说明）**  
实际 UDP checksum 是 IP pseudo-header（从 IP header 取出的部分信息）+ UDP header + data 的 one’s-complement sum 的 16-bit one’s complement；若长度为奇数 bytes，末尾补 zero 使其为 two octets 的倍数。虽然 checksum field 本身在 header 内，计算时会按定义处理该字段；本质仍是让 receiver 能验证完整结果。TCP checksum 的计算方式相似。

---

## 4. Principles of reliable data transfer (rdt)

### 4.1 Reliable service abstraction 与接口

**① Definition（定义）**  
Reliable data transfer service abstraction：application 看见的是 sending process 与 receiving process 之间的 reliable channel；实际上 transport 的 sender-side 与 receiver-side rdt protocol 在一个可能 unreliable 的 network channel 之上实现此服务。

**② 通俗解释**  
上层只想「资料一定正确按序到达」，但下层可能 corrupt、lose 或 reorder packets。rdt 就是两端 transport protocol 合作，把坏网络包装成可靠通道。复杂度强烈取决于 channel 可能发生哪些问题。

sender/receiver 并不知道对方当前状态，例如 sender 不会天然知道 packet 有没有收到；除非对方用 message 告诉它。因此可靠性必须依赖 feedback。

**③ rdt interfaces**

```text
Application -> rdt_send(data) -> sender rdt -> udt_send(packet) -> unreliable channel
unreliable channel -> rdt_rcv(packet) -> receiver rdt -> deliver_data(data) -> Application
```

- `rdt_send(data)`：上层 application 调用，交付要送到对方上层的数据。
- `udt_send(packet)`：rdt 调用，把 packet 投入 unreliable channel。
- `rdt_rcv(packet)`：packet 到 receiver 时被调用。
- `deliver_data(data)`：rdt 向上层交付正确资料。

课堂为了循序建立 protocol，先考虑**单向 data transfer**；但 ACK 等 control information 仍会反向流动。

### 4.2 rdt1.0：底层完全可靠

**① Definition（定义）**  
rdt1.0 assumes a perfectly reliable underlying channel: no bit errors and no packet loss。

**② 通俗解释 / How it works**  
没有错误也不丢失，sender 送 data，receiver 收到并 deliver；没有任何额外工作。这是后面每增加一种网络问题时的基线。

### 4.3 rdt2.0：只有 bit errors

**① Definition（定义）**  
rdt2.0 assumes packets may have bit errors. It uses checksum for error detection, receiver feedback via ACK/NAK, retransmission, and stop-and-wait。

**③ How it works**

```text
sender sends one packet -> waits
receiver: checksum OK  -> ACK -> sender sends next packet
receiver: checksum bad -> NAK -> sender retransmits current packet
```

- **ACK (acknowledgement)**：receiver 明确说 packet 收得正确。
- **NAK (negative acknowledgement)**：receiver 说 packet 有错误。
- **stop-and-wait**：一次只送一个 packet，必须等 receiver response 后才能继续。

### 4.4 rdt2.0 的 fatal flaw 与 rdt2.1

**致命问题**  
如果 ACK 或 NAK 本身 corrupted，sender 不知道 receiver 到底收到什么。直接重传可能造成 receiver 已经交付过的 data 再交付一次（duplicate）。

**① rdt2.1 Definition（定义）**  
rdt2.1 extends rdt2.0 with **sequence numbers**、checksums for ACK/NAK、duplicate detection。两个 sequence numbers（0 和 1）已足够。

**② 为什么 0/1 足够**  
stop-and-wait 同时最多只有一个未确认 packet。receiver 只需知道「下一次期待 0 还是 1」；sender 每成功送一个便交替编号。若 control message 损坏，sender 重传当前编号；receiver 若看到不是期待编号的重复 packet，会 discard 而不 deliver up，并回复 ACK。

```text
send data(0) -> ACK(0) -> send data(1) -> ACK(1)
                         ^ ACK/NAK 损坏时重传 data(1)
receiver 收到重复 data(1) -> discard, re-ACK(1)
```

注意：receiver 不知道自己的上一份 ACK/NAK 有没有成功到 sender，因此也需要借 sequence number 判断重复。

### 4.5 rdt2.2：NAK-free

**① Definition（定义）**  
rdt2.2 has the same functionality as rdt2.1 but uses only ACKs. Instead of NAK, receiver ACKs the last correctly received packet and explicitly includes the acknowledged sequence number。

**③ How it works**  
若 sender 正期待 `ACK(1)`，却收到重复 `ACK(0)`，这等价于「当前 packet 没有被正确接收」的信号，sender retransmits current packet。PPT 指出 TCP 采用这种 **NAK-free** 思路。

### 4.6 rdt3.0：errors 加 loss

**① Definition（定义）**  
rdt3.0 handles a channel that can corrupt or lose data packets and ACKs, using checksums, sequence numbers, ACKs, retransmission and a countdown timer.

**② 通俗解释**  
若 packet 或 ACK 根本丢了，sender 会永远等不到回复；光有 checksum/ACK/sequence number 不够。它必须等待「合理时间」，没有 ACK 就假定 loss 并重传。

**③ How it works**

```text
sender sends pkt n and starts timer
  -> ACK(n) before timeout: stop timer; send next packet
  -> timeout: retransmit pkt n; restart timer

receiver gets new pkt n: deliver once; send ACK(n)
receiver gets duplicate pkt n: do not deliver again; send ACK(n) again
```

- **packet loss**：timeout 后重传；随后依序恢复。
- **ACK loss**：sender timeout 后重传；receiver 识别 duplicate，重发 ACK，不重复交付 data。
- **delayed ACK / premature timeout**：ACK 可能只是迟到，sender 已重传。sequence number 让 receiver 安全丢弃重复 packet；迟到/重复 ACK 可被忽略。PPT 说明 rdt3.0 **不因 duplicate ACK 而重传**，它依 timeout 重传。

### 4.7 RDT Quiz 要点

1. 仅有 packet corruption、没有 loss/reordering，要可靠传输的最少机制是 **checksums, ACKs, NAKs, sequence numbers**（答案 e）。ACK/NAK 也可能 corrupt，故须用 sequence number 安全重传。  
2. 若 packets（包括 ACK/NAK）可能 loss，rdt2.1/2.2 会**get stuck**（答案 b）：它们没有 timeout。  
3. 若要同时处理 corruption 与 loss，最少是 **checksums, ACKs, timeouts, sequence numbers**（答案 d）。NAK 非必要，因为可采用 rdt2.2 的 duplicate ACK 方法。

---

## 5. Stop-and-wait 的性能与 pipelining

### 5.1 Stop-and-wait utilization

**① Definition（定义）**  
Sender utilization (U_{sender}) 是 sender 忙于将 packet transmission 到 channel 的时间比例。对 stop-and-wait：

```text
U_sender = (L / R) / (RTT + L / R)
```

其中 `L` 是 packet bits，`R` 是 link rate。

**② 通俗解释 / 例子**  
PPT 例子：1 Gbps link、15 ms propagation delay、8000-bit packet。

```text
L/R = 8000 / 10^9 = 8 microseconds
U_sender = 0.008 / (30 + 0.008) ≈ 0.00027
```

也就是说 sender 绝大部分时间在等 ACK，协议反而限制了很快的基础设施；rdt3.0 虽可靠，性能很差。

### 5.2 Pipelining

**① Definition（定义）**  
Pipelining allows the sender to have multiple in-flight, not-yet-acknowledged packets.

**② 通俗解释**  
不要寄出一封信后才写下一封；连续送多个 packet，再逐个收 ACK。这样 link 在 RTT 期间仍有资料在飞。三-packet pipeline 会使 utilization 约提高三倍。

**③ 需要的新机制**  
sequence number range 要扩大，sender 和/或 receiver 要 buffering。两种代表性 sliding-window protocol 是 **Go-Back-N (GBN)** 与 **Selective Repeat (SR)**。

---

## 6. Go-Back-N (GBN)

**① Definition（定义）**  
GBN sender maintains a window of up to `N` consecutive transmitted but unACKed packets, with k-bit sequence numbers. It uses **cumulative ACKs**: `ACK(n)` acknowledges all packets up to and including sequence number `n`。

**③ Sender 行为**

- 收到 `ACK(n)`：window 前移，使其从 `n+1` 开始。
- 只对 oldest in-flight packet 维护一个 timer。
- `timeout(n)`：重传 packet `n` 及 window 内所有序号更高的 packets，即「go back」并重送。

**④ Receiver 行为**  
receiver 只需记住 `rcv_base`（最高的 in-order packet）。它总 ACK 到目前为止最高的正确、按序 packet，因而可能产生 duplicate ACK。若收到 out-of-order packet，可选择 discard 或 buffer（implementation decision），但会 re-ACK 最近的 highest in-order sequence number。

**⑤ 具体例子**  
N=4，packet 2 丢失而 packet 3、4、5 到达：receiver 因尚在等 2，不能向上按序 deliver 3/4/5，于是反复发 `ACK(1)`。sender 对 packet 2 timeout 后，重传 2、3、4、5；这简单但会重传其实已到达的 packets。

---

## 7. Selective Repeat (SR)

**① Definition（定义）**  
Selective Repeat individually acknowledges correctly received packets, buffers out-of-order packets for eventual in-order delivery, and retransmits only individually timed-out unACKed packets.

**② 通俗解释**  
与 GBN「一个丢了，后面全重送」相比，SR 会记住后面已经抵达的 packets，只补丢的那一个，因此通常更有效，但 sender/receiver 的状态和实现复杂得多。

**③ Sender/receiver 行为**

```text
Sender:
  if next seq in send window: send packet
  timeout(n): resend packet n; restart n's timer
  ACK(n): mark n received; if n is sendbase, advance past contiguous ACKed packets

Receiver:
  n in receive window: ACK(n); out-of-order packets buffer
  n is in-order: deliver it and all now-contiguous buffered packets; advance rcvbase
  n in previous window: re-ACK(n)
  otherwise: ignore
```

sender 为每个 unACKed packet 维护 timer；receiver 分别 ACK 每个正确 packet，而不是 cumulative ACK。

### 7.1 SR 的 sequence-number-space 约束

**① Definition（定义）**  
To avoid ambiguity after sequence-number wraparound, **sender window size must be at most one half of the sequence number space**：

```text
W_sender <= (sequence number space) / 2
```

**② 为什么需要**  
若 sequence numbers 为 0,1,2,3（空间大小 4），window size 却是 3，旧的 retransmitted `pkt0` 可能在 receiver window 已绕回且正接受新 `pkt0` 时到达。receiver 无法观察 sender 的历史，看到的行为相同，不能判断它是旧 duplicate 还是新 data，可能错误 deliver。窗口至多占序号空间一半才能避免 sender/receiver window 的这种歧义重叠。

---

## 8. GBN vs SR 与 reliability recap

| 维度 | GBN | SR |
|---|---|---|
| ACK | cumulative ACK | individual/selective ACK |
| receiver 的 out-of-order packet | 可 discard 或 buffer；仍 ACK highest in-order | buffer，并分别 ACK |
| timer | oldest outstanding packet 一个 | 每个 unACKed packet 一个 |
| timeout 后 | 重传 n 及所有更高的未确认 packet | 仅重传 timeout 的 n |
| 代价 | 简单但丢一个会浪费重传 | 效率高但状态、buffer、序号约束更复杂 |

**Quiz：**

- 「GBN maintains a separate timer for each outstanding packet」不正确（答案 c）；SR 才是每个 outstanding packet 一个 timer。
- receiver 已正确收到至 24，随后收到 27、28：GBN 仍期待 25，因此两次回复 `ACK(24), ACK(24)`；SR 会分别回复 `ACK(27), ACK(28)`。答案 **b**。

**可靠传输解法总览**

- **Checksums**：detect errors。
- **Timers**：detect loss。
- **Acknowledgments**：可 cumulative 或 selective。
- **Sequence numbers**：识别 duplicates，支持 windows。
- **Sliding windows**：提高效率。

可靠协议正是综合这些机制，决定何时 retransmit、何时 acknowledge。之后 TCP 会回答：怎样追踪 outstanding pipelined segments、pipeline 多少、怎样选 sequence numbers、connection setup/teardown 长什么样、timeout 怎样选择。

---

## 9. TCP overview 与 TCP segment structure

### 9.1 TCP overview

**① Definition（定义）**  
TCP is a connection-oriented, point-to-point, full-duplex, reliable in-order byte-stream transport protocol. Relevant RFCs listed in PPT include 793, 1122, 2018, 5681, 7323.

**② 关键性质**

- **connection-oriented**：data exchange 前 handshaking、交换 control messages，初始化双方 state。
- **reliable, in-order byte stream**：可靠且按序交付的是 bytes；**没有 message boundaries**。
- **full duplex**：同一 connection 可同时双向 data flow。
- **point-to-point**：一个 sender 对一个 receiver。
- **pipelining**：TCP congestion control 和 flow control 一起设置 window size。
- **flow controlled**：sender 不会 overwhelm receiver。
- **MSS (maximum segment size)**：maximum segment size。

TCP 从前面的 rdt 思想吸收 cumulative ACK、pipelining、sequence number、timer 等机制，并在真实 Internet 中结合 flow/congestion control。

### 9.2 TCP segment structure

```text
+--------------------+--------------------+
| source port         | destination port   |
+--------------------+--------------------+
| sequence number                          |
+------------------------------------------+
| acknowledgement number                   |
+----+---+----------------+----------------+
|hlen|...| flags          | receive window |
+-------------------------+----------------+
| checksum                | urgent pointer |
+-------------------------+----------------+
| options (variable)                       |
+------------------------------------------+
| application data (variable length)       |
+------------------------------------------+
```

- **sequence number**：计算的是 byte stream 中的 bytes，不是 segment number。
- **acknowledgement number**：ACK 表示的下一个 expected byte 的 sequence number；`A` bit 表示这是 ACK。
- **receive window**：flow control；receiver 愿意接收的 bytes 数。
- **header length (`hlen`)**：TCP header 的长度；`options` 可变长。
- **checksum**：Internet checksum。
- **RST, SYN, FIN**：connection management。
- **C, E**：congestion notification；其他 flags 包括 `U, A, P, R, S, F`。
- **application data**：application 写入 TCP socket 的可变长度资料。

---

## Week 4 Part 1 总结链路

```text
多个 application sockets
  -> multiplexing：加 TCP/UDP header
  -> IP best-effort network
  -> demultiplexing：按 UDP destination IP/port 或 TCP 4-tuple 找 socket

若用 UDP：无连接、低开销、checksum 只做 error detection

若要可靠传输：checksum + ACK + sequence number + timer + retransmission
  -> stop-and-wait 正确但低利用率
  -> pipelining / sliding window
       -> GBN：cumulative ACK，丢包后回退重传
       -> SR：selective ACK，只重传丢失 packet，但窗口需 <= 序号空间一半

TCP：把这些可靠传输思想用于 connection-oriented、可靠有序 byte stream，
并加入 flow control 与 congestion control（后续内容）。
```

