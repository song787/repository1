

## 1. 网络

#### IPV4跟IPV6比较

1. IPv4：



2. IPv6：

地址长度是128位，每16位是一组，中间用冒号‘：’隔开；连续的0可以用两个冒号来折叠，但只能用一次；

亮点：

- IPv6可以自动设置，即使没有DHCP服务也可以实现IP地址的自动分配；
- IPv6 包头包首部长度采用固定的值 `40` 字节，去掉了包头校验和，简化了首部结构，减轻了路由器负荷，大大**提高了传输的性能**。
- IPv6 有应对伪造 IP 地址的网络安全功能以及防止线路窃听的功能，大大**提升了安全性**。

#### 网桥工作在哪一层

- 物理层：双绞线、中继器、集线器
- 数据链路层：网桥、交换机（根据MAC地址寻址）、网卡
- 网络层：路由器（根据IP地址寻址）
- 应用层：网关；

**网桥**工作在数据链路层的介质访问控制(MAC)子层上,用于在多个使用同一种通信协议的网段中传送数据包的设备

#### 网关的作用是什么

网关（Gateway）又称网间连接器、协议转换器。网关在传输层上以实现网络互连，是最复杂的网络互连设备，仅用于两个高层协议不同的网络互连。网关的结构也和路由器类似，不同的是互连层。网关既可以用于广域互连，也可以用于局域网互连。网关是一种充当转换重任的计算机系统或设备。在使用不同的通信协议、数据格式或语言，甚至体系结构完全不同的两种系统之间，网关是一个翻译器。与网桥只是简单地传达信息不同，网关对收到的信息要重新打包，以适应目的系统的需求。同时，网关也可以提供过滤和安全功能。大多数网关运行在OSI 7层协议的顶层–应用层。

那么网关到底是什么呢？网关实质上是一个网络通向其他网络的IP地址。比如有网络A和网络B，网络A的IP地址范围为“192.168.1.1~192.168.1.254”，子网掩码为255.255.255.0；网络B的IP地址范围为“192.168.2.1~192.168.2.254”，子网掩码为255.255.255.0。在没有路由器的情况下，两个网络之间是不能进行TCP/IP通信的，即使是两个网络连接在同一台交换机（或集线器）上，TCP/IP协议也会根据子网掩码（255.255.255.0）判定两个网络中的主机处在不同的网络里。而要实现这两个网路之间的通信，则必须通过网关。如果网络A中的主机发现数据包的目的主机不在本地网络中，就把数据包转发给它自己的网关，再由网关转发给网络B的网关，网络B的网关再转发给网络B的某个主机。

默认网关的意思是一台主机如果找不到可用的网关，就把数据包发给默认指定的网关，由这个网关来处理数据包。

#### 子网掩码详细作用？

主要是用来区分网络地址与主机地址；如果网络地址相同，则证明是在同一个网段，如果不同则需要借助路由的转发；

是由于IPv4的地址资源紧缺，做不到每个网络设备都能获得一个网络地址。可变长度的网络地址分配方式相比以前主类网络划分方式更加灵活，在有限的网址资源的情况下，提高网络地址的利用率，减少网络地址的浪费。而灵活的代价就是：网络地址可以改变长度，没有规律可循了，只能靠子网掩码来划分了，所以这就是子网掩码的用途：**区分网络地址和主机地址。**

在没有子网掩码之前，网络地址按照主类网络的方式进行区分，但是这种划分方式不够灵活，也很浪费地址资源，并且随着通信设备的普及，有上网需求的通信设备不断增加，并且online和offline之间的切换频繁，数量是随着时间变化而变化的。因此子网掩码的出现，在**划分地址资源**方面做出了改进。

#### OSI七层协议

分别是-物理层、数据链路层、网络层、传输层、会话层、表示层、应用层；

- 应用层：为应用程序提供服务；
  - FTP(21端口)：文件传输协议
  - SSH(22端口)：远程登陆
  - TELNET(23端口)：远程登录
  - SMTP(25端口)：发送邮件
  - POP3(110端口)：接收邮件
  - HTTP(80端口)：超文本传输协议
  - DNS(53端口)：运行在UDP上，域名解析服务
- 表示层：数据格式转化、数据加密；
- 会话层：建立、管理和维护会话；
- 传输层：建立、管理和维护端到端的连接；
- 网络层：IP选址与路由选择；
- 数据链路层：提供介质访问与链路管理；
- 物理层：通过物理介质传输比特流；

#### Get 与 Post区别？

- **GET**用于获取资源，**POST**用于传输实体主体；

- **参数方面：**GET 和 POST 的请求都能使用额外的参数，但是 GET 的参数是以查询字符串出现在 URL 中，而 POST 的参数存储在实体主体中。不能因为 POST 参数存储在实体主体中就认为它的安全性更高，因为照样可以通过一些抓包工具查看。因为 URL 只支持 ASCII 码，因此 GET 的参数中如果存在中文等字符就需要先进行编码。例如 `中文` 会转换为 `%E4%B8%AD%E6%96%87`，而`空格`会转换为 `%20`。POST 参数支持标准字符集。

- **安全方面：**安全的 HTTP 方法不会改变服务器状态，也就是说它只是可读的。GET 方法是安全的，而 POST 却不是，因为 POST 的目的是传送实体主体内容，这个内容可能是用户上传的表单数据，上传成功之后，服务器可能把这个数据存储到数据库中，因此状态也就发生了改变。

  安全的方法除了 GET 之外还有：HEAD、OPTIONS。

  不安全的方法除了 POST 之外还有 PUT、DELETE。

- **幂等性：**幂等性：同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。所有的安全的方法都是幂等的，在正确实现的情况下：GET、HEAD、PUT、DELETE等是幂等的，POST是非幂等的；

- **可缓存：**如果要对响应进行缓存，需要满足以下条件：

  - 请求报文的 HTTP 方法本身是可缓存的，包括 GET 和 HEAD，但是 PUT 和 DELETE 不可缓存，POST 在多数情况下不可缓存的。
  - 响应报文的状态码是可缓存的，包括：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, and 501。
  - 响应报文的 Cache-Control 首部字段没有指定不进行缓存。

- 为了阐述 POST 和 GET 的另一个区别，需要先了解 XMLHttpRequest：

  > XMLHttpRequest 是一个 API，它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL 来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX 中被大量使用。

  - 在使用 XMLHttpRequest 的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。
  - 而 GET 方法 Header 和 Data 会一起发送。

#### Http与Https区别？

1. 端口不同：HTTP使用的是80端口，HTTPS使用443端口；
2. HTTP（超文本传输协议）信息是明文传输，HTTPS运行在SSL(Secure Socket Layer)之上，添加了加密和认证机制，更加安全；
3. HTTPS由于加密解密会带来更大的CPU和内存开销；
4. HTTPS通信需要证书，一般需要向证书颁发机构（CA）购买

#### HTTPS加密过程？

- 加密：
  - 对称密钥加密：对称密钥加密（Symmetric-Key Encryption），加密和解密使用同一密钥。所以运算速度会非常快，但是无法安全的将密钥传输给通信方；
  - 非对称密钥加密：非对称密钥加密，又称公开密钥加密（Public-Key Encryption），加密和解密使用不同的密钥。公开密钥所有人都可以获得，通信发送方获得接收方的公开密钥之后，就可以使用公开密钥进行加密，接收方收到通信内容后使用私有密钥解密。
  - 区别：对称加密速度更快，通常用于大量数据的加密；非对称加密安全性更高（不需要传送私钥）
- HTTPS 采用混合的加密机制：
  - 使用非对称密钥加密方式，传输对称密钥加密方式所需要的 Secret Key，从而保证安全性;
  - 获取到 Secret Key 后，再使用对称密钥加密方式进行通信，从而保证效率。（下图中的 Session Key 就是 Secret Key）
- 数字签名：
  - 非对称密钥除了用来加密，还可以用来进行签名。因为私有密钥无法被其他人获取，因此通信发送方使用其私有密钥进行签名，通信接收方使用发送方的公开密钥对签名进行解密，就能判断这个签名是否正确。

#### Https的连接过程 *

1. 客户端向服务器发送请求，同时发送客户端支持的一套加密规则（包括对称加密、非对称加密、摘要算法）；
2. 服务器从中选出一组加密算法与HASH算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，**加密公钥**（用于非对称加密），以及证书的颁发机构等信息（证书中的私钥只能用于服务器端进行解密）；
3. 客户端验证服务器的合法性，包括：证书是否过期，CA 是否可靠，发行者证书的公钥能否正确解开服务器证书的“发行者的数字签名”，服务器证书上的域名是否和服务器的实际域名相匹配；
4. 如果证书受信任，或者用户接收了不受信任的证书，浏览器会生成一个**随机密钥**（用于对称算法），并用服务器提供的公钥加密（采用非对称算法对密钥加密）；使用Hash算法对握手消息进行**摘要**计算，并对摘要使用之前产生的密钥加密（对称算法）；将加密后的随机密钥和摘要一起发送给服务器；
5. 服务器使用自己的私钥解密，得到对称加密的密钥，用这个密钥解密出Hash摘要值，并验证握手消息是否一致；如果一致，服务器使用对称加密的密钥加密握手消息发给浏览器；
6. 浏览器解密并验证摘要，若一致，则握手结束。之后的数据传送都使用对称加密的密钥进行加密

总结：非对称加密算法用于在握手过程中加密生成的密码；对称加密算法用于对真正传输的数据进行加密；HASH算法用于验证数据的完整性。

#### HTTP的状态码

| 状态码 | 类别                             | 含义                       |
| ------ | -------------------------------- | -------------------------- |
| 1XX    | Informational（信息性状态码）    | 接收的请求正在处理         |
| 2XX    | Success（成功状态码）            | 请求正常处理完毕           |
| 3XX    | Redirection（重定向状态码）      | 需要进行附加操作以完成请求 |
| 4XX    | Client Error（客户端错误状态码） | 服务器无法处理请求         |
| 5XX    | Server Error（服务器错误状态码） | 服务器处理请求出错         |

#### TCP\UDP的区别

1. TCP是面向连接的，UDP是无连接的；
2. TCP是可靠的，UDP不可靠；
3. TCP只支持点对点通信，UDP支持一对一、一对多、多对一、多对多；
4. TCP是面向字节流的，UDP是面向报文的；
5. TCP有拥塞控制机制，UDP没有。网络出现的拥塞不会使源主机的发送速率降低，这对某些实时应用是很重要的，比如媒体通信，游戏；
6. TCP首部开销（20字节）比UDP首部开销（8字节）要大；
7. UDP 的主机不需要维持复杂的连接状态表；

#### TCP为什么可靠

1. 数据包校验：目的是检测数据在传输过程中的任何变化，若校验出包有错，则丢弃报文段并且不给出响应，这时TCP发送数据端超时后会重发数据；
2. 对失序数据包重排序：既然TCP报文段作为IP数据报来传输，而IP数据报的到达可能会失序，因此TCP报文段的到达也可能会失序。TCP将对失序数据进行重新排序，然后才交给应用层；
3. 丢弃重复数据：对于重复数据，能够丢弃重复数据；
4. 应答机制：当TCP收到发自TCP连接另一端的数据，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒；
5. 超时重发：当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段；
6. 流量控制：TCP连接的每一方都有固定大小的缓冲空间。TCP的接收端只允许另一端发送接收端缓冲区所能接纳的数据，这可以防止较快主机致使较慢主机的缓冲区溢出，这就是流量控制。TCP使用的流量控制协议是可变大小的滑动窗口协议。

#### TCP三次握手

![三次握手.png-12.4kB](http://static.zybuluo.com/Rico123/c7m5fo6qdua0q7me88jm9w10/%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B.png)

- 第一次握手：Client将SYN置1，随机产生一个初始序列号seq发送给Server，进入SYN_SENT状态；
- 第二次握手：Server收到Client的SYN=1之后，知道客户端请求建立连接，将自己的SYN置1，ACK置1，产生一个acknowledge number=sequence number+1，并随机产生一个自己的初始序列号，发送给客户端；进入SYN_RCVD状态；
- 第三次握手：客户端检查acknowledge number是否为序列号+1，ACK是否为1，检查正确之后将自己的ACK置为1，产生一个acknowledge number=服务器发的序列号+1，发送给服务器；进入ESTABLISHED状态；服务器检查ACK为1和acknowledge number为序列号+1之后，也进入ESTABLISHED状态；完成三次握手，连接建立。

>TCP连接可以两次握手吗？

不可以，两方面的原因：

1. 可能会出现已经失效的连接请求报文段又发送到了服务端，比如说client发送的一个连接请求报文段在传输过程中并没有丢失，而是在某个网络节点拥塞并滞留了一段时间， 导致报文段延误到连接释放之后才到达服务器，这样服务器收到了一个原本是失效的报文段，但是服务器不知道，会误认为是client发送的新的连接请求，所以就会向client发送确认报文段建立连接，如果是两次握手，那么现在就成功建立了连接，但实际client端并没有发送新的建立连接的请求，也就不会理睬服务器的确认报文段，不会向服务器发送数据。而服务器端以为已经建立好了连接，就会一直等待client发送数据，导致服务器资源的浪费；三次握手就可以解决这样的问题，在client收到服务器确认报文段时，不会向服务器的确认发送确认，服务器收不到确认，连接也就不能建立；
2. 两次握手无法保证client正确的收到服务器发送的确认报文，也无法保证client与服务器端之间成功交换初始序列号；

> 可以采用四次握手吗？

- 可以，但是三次握手可以搞定的事情，用四次握手才搞定，就会降低传输的效率；多出来的一次是将第二次服务器到客户端的确认报文拆分为两次传输，第一次服务器只发送ACK与acknowledge number的确认报文段，而服务器端的SYN与初始序列号在第三次握手的时候发送；

> 第三次握手中，如果客户端的ACK报文段没有成功发送到服务器端，会发生什么？

- 服务器端：由于没有收到ACK确认，会重复发送第二次握手的报文段，（默认是重发5次，然后会自动关闭连接，进入closed状态），在客户端收到重发的报文段后会重新传ACK给服务器端；
- 客户端：1. 在Server进行超时重发的过程中，如果Client向服务器发送数据，数据头部的ACK是为1的，所以服务器收到数据之后会读取 ACK number，进入 establish 状态；2. 在Server进入CLOSED状态之后，如果Client向服务器发送数据，服务器会以RST包应答。

> 建立连接后，如果客户端出现了故障会怎么样？

- 服务器每收到一次客户端的请求后都会重新复位一个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75秒钟发送一次。若一连发送10个探测报文仍然没反应，服务器就认为客户端出了故障，接着就关闭连接。

> 什么是初始序列号？

- TCP连接的一方，随机选择一个32位的序列号（Sequence Number）作为发送数据的初始序列号（Initial Sequence Number，ISN），以该序列号为原点，对要传送的数据进行编号. 三次握手时，把这个初始序列号传送给另一方B，以便在传输数据时，B可以确认什么样的数据编号是合法的；同时在进行数据传输时，A还可以确认B收到的每一个字节。

#### TCP四次挥手

- 第一次挥手：Client将FIN置为1，发送一个序列号seq给Server；进入FIN_WAIT_1状态；
- 第二次挥手：Server收到FIN之后，发送一个ACK=1，acknowledge number=收到的序列号+1；进入CLOSE_WAIT状态。此时客户端已经没有要发送的数据了，但仍可以接受服务器发来的数据。
- 第三次挥手：Server将FIN置1，发送一个序列号给Client；进入LAST_ACK状态；
- 第四次挥手：Client收到服务器的FIN后，进入TIME_WAIT状态；接着将ACK置1，发送一个acknowledge number=序列号+1给服务器；服务器收到后，确认acknowledge number后，变为CLOSED状态，不再向客户端发送数据。客户端等待2*MSL（报文段最长寿命）时间后，也进入CLOSED状态。完成四次挥手。

> 为什么不能把服务器发送的ACK和FIN合并起来，变成三次挥手（CLOSE_WAIT状态意义是什么）？

- 因为服务器收到客户端断开连接的请求时，可能还有一些数据没有发完，这时先回复ACK，表示接收到了断开连接的请求。等到数据发完之后再发FIN，断开服务器到客户端的数据传送。

> 如果第二次挥手时服务器的ACK没有送达客户端，会怎样？

- 客户端没有收到ACK确认，会重新发送FIN请求。

> 客户端TIME_WAIT状态的意义是什么？

- 第四次挥手时，客户端发送给服务器的ACK有可能丢失，TIME_WAIT状态就是用来重发可能丢失的ACK报文。如果Server没有收到ACK，就会重发FIN，如果Client在2*MSL的时间内收到了FIN，就会重新发送ACK并再次等待2MSL，防止Server没有收到ACK而不断重发FIN。
- MSL(Maximum Segment Lifetime)，指一个片段在网络中最大的存活时间，2MSL就是一个发送和一个回复所需的最大时间。如果直到2MSL，Client都没有再次收到FIN，那么Client推断ACK已经被成功接收，则结束TCP连接。



#### TCP滑动窗口机制

窗口是缓存的一部分，用来暂时存放字节流。发送方和接收方各有一个窗口，接收方通过 TCP 报文段中的窗口字段告诉发送方自己的窗口大小，发送方根据这个值和其它信息设置自己的窗口大小。

使用滑动窗口协议实现流量控制。防止发送方发送速率太快，接收方缓存区不够导致溢出。接收方会维护一个接收窗口 receiver window（窗口大小单位是字节），接受窗口的大小是根据自己的资源情况动态调整的，在返回ACK时将接受窗口大小放在TCP报文中的窗口字段告知发送方。发送窗口的大小不能超过接受窗口的大小，只有当发送方发送并收到确认之后，才能将发送窗口右移。

发送窗口的上限为接受窗口和拥塞窗口中的较小值。接受窗口表明了接收方的接收能力，拥塞窗口表明了网络的传送能力。

发送窗口内的字节都允许被发送，接收窗口内的字节都允许被接收。如果发送窗口左部的字节已经发送并且收到了确认，那么就将发送窗口向右滑动一定距离，直到左部第一个字节不是已发送并且已确认的状态；接收窗口的滑动类似，接收窗口左部字节已经发送确认并交付主机，就向右滑动接收窗口。

接收窗口只会对窗口内最后一个按序到达的字节进行确认，例如接收窗口已经收到的字节为 {31, 34, 35}，其中 {31} 按序到达，而 {34, 35} 就不是，因此只对字节 31 进行确认。发送方得到一个字节的确认之后，就知道这个字节之前的所有字节都已经被接收。

> 接收窗口为0会怎么样？零窗口

- 如果接收方没有能力接收数据，就会将接收窗口设置为0，这时发送方必须暂停发送数据，但是会启动一个持续计时器(persistence timer)，到期后发送一个大小为1字节的探测数据包，以查看接收窗口状态。如果接收方能够接收数据，就会在返回的报文中更新接收窗口大小，恢复数据传送。

#### TCP拥塞控制机制

如果网络出现拥塞，分组将会丢失，此时发送方会继续重传，从而导致网络拥塞程度更高。因此当出现拥塞时，应当控制发送方的速率。这一点和流量控制很像，但是出发点不同。流量控制是为了让接收方能来得及接收，而拥塞控制是为了降低整个网络的拥塞程度。

TCP 主要通过四个算法来进行拥塞控制：`慢开始、拥塞避免、快重传、快恢复`。

发送方需要维护一个叫做拥塞窗口（cwnd）的状态变量，注意拥塞窗口与发送方窗口的区别：拥塞窗口只是一个状态变量，实际决定发送方能发送多少数据的是发送方窗口。

为了便于讨论，做如下假设：

- 接收方有足够大的接收缓存，因此不会发生流量控制；
- 虽然 TCP 的窗口基于字节，但是这里设窗口的大小单位为报文段。

1. 慢开始与拥塞避免

发送的最初执行慢开始，令 cwnd = 1，发送方只能发送 1 个报文段；当收到确认后，将 cwnd 加倍，因此之后发送方能够发送的报文段数量为：2、4、8 ...

注意到慢开始每个轮次都将 cwnd 加倍，这样会让 cwnd 增长速度非常快，从而使得发送方发送的速度增长速度过快，网络拥塞的可能性也就更高。设置一个慢开始门限 ssthresh，当 cwnd >= ssthresh 时，进入拥塞避免，每个轮次只将 cwnd 加 1。

如果出现了超时，则令 ssthresh = cwnd / 2，然后重新执行慢开始。

2. 快重传与快恢复

在接收方，要求每次接收到报文段都应该对最后一个已收到的有序报文段进行确认。例如已经接收到 M1 和 M2，此时收到 M4，应当发送对 M2 的确认。

在发送方，如果收到三个重复确认，那么可以知道下一个报文段丢失，此时执行快重传，立即重传下一个报文段。例如收到三个 M2，则 M3 丢失，立即重传 M3。

在这种情况下，只是丢失个别报文段，而不是网络拥塞。因此执行快恢复，令 ssthresh = cwnd / 2 ，cwnd = ssthresh，注意到此时直接进入拥塞避免。

慢开始和快恢复的快慢指的是 cwnd 的设定值，而不是 cwnd 的增长速率。慢开始 cwnd 设定为 1，而快恢复 cwnd 设定为 ssthresh。

#### TCP和UDP分别对应的常见应用层协议

1). TCP对应的应用层协议

- FTP：定义了文件传输协议，使用21端口。常说某某计算机开了FTP服务便是启动了文件传输服务。下载文件，上传主页，都要用到FTP服务。
- Telnet：它是一种用于远程登陆的端口，用户可以以自己的身份远程连接到计算机上，通过这种端口可以提供一种基于DOS模式下的通信服务。如以前的BBS是-纯字符界面的，支持BBS的服务器将23端口打开，对外提供服务。
- SMTP：定义了简单邮件传送协议，现在很多邮件服务器都用的是这个协议，用于发送邮件。如常见的免费邮件服务中用的就是这个邮件服务端口，所以在电子邮件设置-中常看到有这么SMTP端口设置这个栏，服务器开放的是25号端口。
- POP3：它是和SMTP对应，POP3用于接收邮件。通常情况下，POP3协议所用的是110端口。也是说，只要你有相应的使用POP3协议的程序（例如Fo-xmail或Outlook），就可以不以Web方式登陆进邮箱界面，直接用邮件程序就可以收到邮件（如是163邮箱就没有必要先进入网易网站，再进入自己的邮-箱来收信）。
- HTTP：从Web服务器传输超文本到本地浏览器的传送协议。

2). UDP对应的应用层协议

- DNS：用于域名解析服务，将域名地址转换为IP地址。DNS用的是53号端口。
- SNMP：简单网络管理协议，使用161号端口，是用来管理网络设备的。由于网络设备很多，无连接的服务就体现出其优势。
- TFTP(Trival File Transfer Protocal)：简单文件传输协议，该协议在熟知端口69上使用UDP服务。

#### URL请求到渲染的过程

浏览器解析URL->生成发送给Web服务器的请求信息（HTTP请求信息）->查询服务器域名对应的IP地址（DNS）-> 发起client到server的连接请求（TCP三次握手） -> 连接建立之后由client向server发送HTTP请求信息 -> 服务器处理请求信息，并将结果返回给浏览器 -> 浏览器完成解析并渲染

1. 浏览器解析URL，先要通过网站的域名查询域名对应的IP地址；
2. 生成DNS查询报文，并放入目的地址为DNS服务器IP地址的IP数据报中，IP数据报放入以太网帧中，如果这时还不知道网关的MAC地址，可能还需要ARP协议的帮助来获取网关的MAC地址；
3. 收到来自DNS服务器的应答报文之后，解析出域名地址对应的IP地址；
4. 有了HTTP服务器的IP地址之后，主机通过TCP三次握手来与HTTP服务器建立连接；
5. 主机生成TCP套接字，并且生成HTTP GET报文，发送给HTTP服务器；
6. HTTP服务器从TCP套接字读取GET报文，生成一个HTTP响应报文，返回给浏览器；
7. 浏览器收到HTTP响应报文后，解析内容后进行渲染，显示出相应的Web页面；

#### DNS协议

DNS域名解析协议就是将域名地址转换为IP地址；

域名层级之间靠句点`"."`来分割，越靠右边的域名层级越高；

- 域名层级关系：
  - 根DNS服务器
  - 顶级域DNS服务器
  - 权威DNS服务器
- 工作流程：
  - 客户端发送DNS请求，发送给本地DNS服务器；
  - 本地域名服务器：如果能够在缓存表中找到域名的IP地址，则直接返回IP地址；如果没有，本地DNS会发送信息到根域名服务器；
  - 根DNS服务器：根据域名给出顶级域名服务器地址，并发送给本地域名服务器；
  - 本地域名服务器收到地址后发送信息给顶级域名服务器；
  - 顶级域名服务器将权威域名服务器地址发送给本地域名服务器；
  - 本地域名服务器发送信息给权威域名服务器；
  - 权威域名服务器将IP地址发送给本地域名服务器；
  - 本地域名服务器将IP地址返回给客户端；

#### ARP协议

ARP 地址解析协议，作用是在以太网环境中，数据的传输所依懒的是`MAC地址`而非`IP地址`，而将已知`IP地址`转换为`MAC地址`的工作是由`ARP协议`来完成的。每个主机都会有一个`ARP高速缓存`，里面有所在的局域网上的各主机和路由器的IP地址到硬件地址的`映射表`。

数据包在确定源IP地址与目的IP地址之后，会通过主机的路由表来确定数据包的下一跳的IP地址，但是数据链路层需要的是下一跳的MAC地址，因此借助ARP协议可以完成IP地址到MAC地址的转换，具体过程如下：

- ARP协议借助ARP请求与ARP响应两种包来确定MAC地址；
- 主机通过广播发送ARP请求包，包中含有想知道的MAC地址对应的IP地址；
- 同链路中设备收到ARP请求包后将会解析ARP请求包中的目标IP地址，如果匹配将会返回含有自己MAC地址的ARP响应包给主机；
- 操作系统会建立一个所在局域网上各设备的IP地址到MAC地址的映射表，并会定期清理映射表中的内容；

> RARP协议：ARP协议的逆协议，作用是根据MAC地址转换为IP地址；
>
> 如果是小型嵌入式设备接入网络，则通常需要一台RARP服务器来协助其完成IP设置，服务器中注册该设备的MAC地址与IP地址，然后将该设备接入网络，随后就会发送一条MAC地址到IP地址的RARP请求包，服务器则会将查询到的IP地址通过相应包发送给设备，协助其完成IP地址设置；

#### ICMP协议

ICMP(internet control message protocol)网际控制报文协议，目的是提高IP数据报交付成功的机会。ICMP属于IP层协议，允许主机或路由器报告差错情况和提供有关异常情况的报告，测试网络层有没有故障。

功能：确认IP包是否成功送达目标地址、报告发送过程中IP包被废弃的原因、改善网络设置等；

1. ICMP报文格式

> 首先，ICMP报文是封装在IP包里面的，作为IP包的数据部分存在，是TCP/IP协议中IP协议的助手；

类型（8位）：ICMP查询报文（用于诊断）、ICMP差错报告报文（通知出错原因）

```c++
/*
查询报文类型：<类型号-内容>   0-回送应答、8-回送请求

类型号（8位）：0、8;
代码（8位）：0;
校验和（16位）：
标识符（16位）：用以区分是哪个进程发送的ICMP包，比如使用进程PID作为标识符;
序号（16位）：序号从0开始，每发送一次新的回送请求就会加1，可以用来确认网络包是否有丢失;
选项数据：具体情况而定，ping会存放发送请求的时间，来计算往返时间；
```

```c++
/*
差错报文类型：<类型号-内容>   3-目标不可达、4-原点抑制、5-重定向或改变路由、11-超时

类型号（8位）：3、4、5、11

//类型号 3 目标不可达消息
IP路由器无法将IP数据包发送给目标地址时，返回发送端主机一个目标不可达的ICMP消息
 在ICMP包头的代码字段中记录具体不可达原因；
 0-网络不可达；
 1-主机不可达；
 2-协议不可达；
 3-端口不可达；
 4-需要进行分片但设置了不分片；
//类型号 4 原点抑制消息
ICMP原点抑制是为了缓解低速广域线路下，路由器可能会遇到网络拥堵的问题；
 具体来说就是当路由器向低速线路发送数据时，其发送队列的缓存变为零而无法发送出去时，可以向IP包的源地址发送一个ICMP原点抑制消息；收到该包的主机会增大IP包的传输间隔，减少网络拥堵的状况；
//类型号 5 重定向消息
如果路由器发现发送端使用了非最优路径发送数据，它会返回一个ICMP重定向消息给主机；
 消息中包含最合适的路由信息和源数据；
//类型号 11 超时消息
如果IP包中的TTL值在当前路由器中减为0，则该IP包被丢弃，并且发送一个ICMP超时消息给发送端主机；
```

2. ping的过程：

1. 源主机先构建一个ICMP回送请求消息数据包，类型号为8，并且还有序号，每发一个请求数据包，序号自动加1，并且在报文数据部分插入发送时间，以方便计算往返时间RTT；
2. 由ICMP协议将数据包与目的IP地址交给IP层，IP层构建一个协议字段为1的IP数据包；
3. 从本地ARP映射表中查询或者利用ARP协议查询目的MAC地址，通过数据链路层构建一个数据帧并发送出去；

4. 目的端收到数据帧后，查验目的MAC地址，匹配则接收，否则丢弃；
5. 提取数据帧中的IP数据包，IP层检查后将有用的信息提取交给ICMP协议；
6. 目的端主机会构建一个ICMP回送响应消息数据包，类型为0，序号为请求数据包中的序号，发送给源端主机；

- 源端如果在规定时间内没有收到ICMP应答包，则说明目标主机不可达，收到应答包后，会用当前时刻减去数据包发送时刻，得到数据包的延迟；

3. traceroute的作用：

1. 通过故意设置特殊的TTL，来追踪去往目的地时沿途经过的路由器；
2. 利用traceroute可以知道发出的UDP包是否到达了目的主机；
3. traceroute可以通过故意设置不分片，来确定路径的MTU；

#### IGMP协议



#### DHCP协议

DHCP协议用于动态获取IP地址；具体步骤为：（本部分来自小林coding）

- 客户端首先发起 **DHCP 发现报文（DHCP DISCOVER）** 的 IP 数据报，由于客户端没有 IP 地址，也不知道 DHCP 服务器的地址，所以使用的是 UDP **广播**通信，其使用的广播目的地址是 `255.255.255.255`（端口 67） 并且使用` 0.0.0.0`（端口 68） 作为源 IP 地址。DHCP 客户端将该 IP 数据报传递给链路层，链路层然后将帧广播到所有的网络中设备。
- DHCP 服务器收到 DHCP 发现报文时，用 **DHCP 提供报文（DHCP OFFER）** 向客户端做出响应。该报文仍然使用 IP 广播地址 `255.255.255.255`，该报文信息携带服务器提供可租约的 IP 地址、子网掩码、默认网关、DNS 服务器以及 **IP 地址租用期**。
- 客户端收到一个或多个服务器的 DHCP 提供报文后，从中选择一个服务器，并向选中的服务器发送 **DHCP 请求报文（DHCP REQUEST）**进行响应，回显配置的参数。
- 最后，服务端用 **DHCP ACK 报文**对 DHCP 请求报文进行响应，应答所要求的参数。

一旦客户端收到 DHCP ACK 后，交互便完成了，并且客户端能够在租用期内使用 DHCP 服务器分配的 IP 地址。

如果租约的 DHCP IP 地址快期后，客户端会向服务器发送 DHCP 请求报文：

- 服务器如果同意继续租用，则用 DHCP ACK 报文进行应答，客户端就会延长租期。
- 服务器如果不同意继续租用，则用 DHCP NACK 报文，客户端就要停止使用租约的 IP 地址。

所以在DHCP 交互中**全程都是使用 UDP 广播通信**。

> 对于客户端与DHCP服务器不在同一局域网内的情况，需要用到DHCP中继代理：
>
> - DHCP 客户端会向 DHCP 中继代理发送 DHCP 请求包，而 DHCP 中继代理在收到这个广播包以后，再以**单播**的形式发给 DHCP 服务器。
> - 服务器端收到该包以后再向 DHCP 中继代理返回应答，并由 DHCP 中继代理将此包转发给 DHCP 客户端 。
>
> 因此，DHCP 服务器即使不在同一个链路上也可以实现统一分配和管理IP地址。

#### 客户端不断进行请求链接会怎样？DDos攻击

1)、DDos 攻击

- 客户端向服务端发送请求链接数据包
- 服务端向客户端发送确认数据包
- 客户端不向服务端发送确认数据包，服务器一直等待来自客户端的确认

2)、DDos 预防 **( 没有彻底根治的办法，除非不使用TCP )**

- 限制同时打开SYN半链接的数目
- 缩短SYN半链接的Time out 时间
- 关闭不必要的服务

------

## 2. 操作系统

#### 进程与线程的区别是啥

- 进程：进程是系统进行资源分配的基本单位，进程的基本信息与状态由进程控制块PCB来描述；

  - 程序 = 算法 + 数据结构（描述进程的数据结构：进程控制块PCB） 

- 线程：线程是CPU调度的基本单位，是进程当中的一条执行流程，同时线程依赖于进程存在，一个进程至少有一个线程存在；`线程=进程-共享资源`

  - 线程能够减少并发执行的时间和空间开销：
    - 线程的创建时间比进程短；
    - 线程的终止时间比进程短
    - 同一进程内的线程切换时间比进程短
    - 由于同一进程的各线程之间共享内存和文件资源，可直接进行不通过内核的哦通信；

- 区别：
  - 进程是系统资源分配的基本单位，因此线程只拥有一点再运行中必不可少的资源，如程序计数器、寄存器、栈等，其余的资源需要访问隶属进程的资源；

  - 线程是CPU调度的最小单位，因此同一个进程中的线程切换只需要保存和设置少量的寄存器内容，而不属于同一个进程的线程之间的切换，涉及到进程切换，需要当前执行进程CPU环境的保存及新调度进程CPU环境的设置，相对来说进程切换的开销远大于线程切换的开销；`线程有自己的寄存器和堆栈`

  - 通信方面，线程之间的通信可以通过同一进程中的共享数据通信，而进程之间的通信需要借助IPC；

  - 稳定性方面，多线程程序如果有一个线程对共享数据造成破坏或崩溃，会影响整个程序，导致崩溃，但是在多进程程序中，进程间是相互独立的，一个进程出错或崩溃不会导致整个程序崩溃，所以在稳定性和健壮性方面，多进程更好；`进程更安全，但是慢一点，线程快，性能好，但是不够安全`

#### 线程的实现方式？有什么区别？*

  C++ 静态局部变量 能保证线程安全 

  问了别的方式std::call_once或者静态成员变量，但是一般都会用静态局部变量 

  C++没有java的volatile能阻止指令重排，不能用DCL 

#### 进程通信方法

1. 管道pipe
   - 管道是通过调用 pipe 函数创建的，fd[0] 用于读，fd[1] 用于写。
   - 只支持半双工通信（单向交替传输）；
   - 只能在父子进程或者兄弟进程中使用。
2. 命名管道FIFO
   - 去除了管道只能在父子进程中使用的限制。
   - FIFO常用于客户-服务器（C/S）应用程序中，FIFO用作汇聚点，在客户进程和服务器进程之间传递数据。
3. 消息队列
   - 消息队列是按照FIFO来管理消息的；
   - 消息队列可以独立于读写进程存在，从而避免了 FIFO 中同步管道的打开和关闭时可能产生的困难；
   - 避免了 FIFO 的同步阻塞问题，不需要进程自己提供同步方法；
   - 读进程可以根据消息类型有选择地接收消息，而不像 FIFO 那样只能默认地接收。
4. 信号量
   - 信号量是一个整型变量sem，包含两个原子操作P( )、V( )，也就是对信号量的-1和+1操作；
   - 当一个线程访问临界区，信号量为正，则可以进行P操作，如果信号量等于0，则线程睡眠，等待信号量大于0；如果一个线程完成了对临界区的访问，会对信号量执行V操作，信号量+1，同时会唤醒睡眠的线程；
   - 信号量是被保护的变量，在初始化完成后，唯一改变一个信号量的值的办法就是通过P、V操作；
   - P操作能够产生阻塞，V操作不会阻塞；
   - 信号量允许同一时刻多个线程访问同一资源，最大线程数量不超过信号量的最大资源计数；
   - 如果信号量的最大资源计数为1，也就是只能取0和1，那么信号量就成了互斥量；
5. 共享内存
   - 也称为直接通信模式；
   - 每个进程都有私有地址空间，在每个地址空间内，明确的设置了`共享内存段(多个进程可以将同一个文件映射到它们的地址空间，也就是映射到共享内存段，从而实现共享内存)`；允许多个进程共享一个给定的存储区。因为数据不需要在进程之间复制，所以这是最快的一种 IPC，并且一个进程写，另一个进程立即可见，没有系统调用干预，没有数据的复制；
   - 该方法能够快速、方便的共享数据，但是必须同步数据访问，以免产生冲突（常利用`信号量`来实现同步）；
6. 套接字
   - 与其它通信机制不同的是，它可用于不同机器间的进程通信。

#### 进程同步方法

> 什么是同步：同步就是数据保持一致，无论是进程还是线程，都是实现了代码执行流程的分支，多个分支同时进行。多个分支互不干扰，但是又有些数据需要共享，让这些数据对所有分支保持一致即为同步。 
>
> 什么是通信：通信就是数据传输，数据存在两块不同的内存区域。通过某种方式互相传递。

- 进程的同步是目的，而进程间通信是实现进程同步的手段;

1. 信号量semaphore :信号量（semaphore）的数据结构为一个值和一个指针，指针指向等待该信号量的下一个进程。信号量的值与相应资源的使用情况有关。当它的值大于0时，表示当前可用资源的数量；当它的值小于0时，其绝对值表示等待使用该资源的进程个数。注意，信号量的值仅能由PV操作来改变。	

   - PV操作是典型的同步机制之一。用一个信号量与一个消息联系起来，当信号量的值为0时，表示期望的消息尚未产生；当信号量的值非0时，表示期望的消息已经存在。用PV操作实现进程同步时，调用P操作测试消息是否到达，调用V操作发送消息。

   -   使用PV操作实现进程同步时应该注意的是：

       （1）分析进程间的制约关系，确定信号量种类。在保持进程间有正确的同步关系情况下，哪个进程先执行，哪些进程后执行，彼此间通过什么资源（信号量）进行协调，从而明确要设置哪些信号量。
       （2）信号量的初值与相应资源的数量有关，也与P、V操作在程序代码中出现的位置有关。
       （3）同一信号量的P、V操作要成对出现，但它们分别在不同的进程代码中。

2. 管程:集中式同步进程，其基本思想是将共享变量和对它们的操作集中在一个模块中，操作系统或并发程序就由这样的模块构成。这样模块之间联系清晰，便于维护和修改，易于保证正确性。

   - 从语言的角度看，管程主要有以下特性： 

     （1）模块化。管程是一个基本程序单位，可以单独编译; 

     （2）抽象数据类型。管程是中不仅有数据，而且有对数据的操作; 

     （3）信息掩蔽。管程外可以调用管程内部定义的一些函数，但函数的具体实现外部不可见; 

   - 为了保证共享变量的数据一致性，管程应互斥使用。 管程通常是用于管理资源的，因此管程中有`进程等待队列`和相应的`等待和唤醒操作`。在管程入口有一个等待队列，称为`入口等待队列`。当一个已进入管程的进程等待时，就释放管程的互斥使用权；当已进入管程的一个进程唤醒另一个进程时，两者必须有一个退出或停止使用管程。在管程内部，由于执行唤醒操作，可能存在多个等待进程（等待使用管程），称为`紧急等待队列`，它的优先级高于入口等待队列。

   - 因此，一个进程进入管程之前要先申请，一般由管程提供一个`enter`过程；离开时释放使用权，如果紧急等待队列不空，则唤醒第一个等待者，一般也由管程提供外部过程`leave`。

   - 管程内部有自己的等待机制。管程可以说明一种特殊的条件型变量：var c:condition；实际上是一个指针，指向一个等待该条件的PCB队列。对条件型变量可执行`wait`和`signal`操作：（联系P和V； take和give） 

   - `wait(c)`:若紧急等待队列不空，唤醒第一个等待者，否则释放管程使用权。执行本操作的进程进入C队列尾部； 

   - `signal(c)`:若C队列为空，继续原进程，否则唤醒队列第一个等待者，自己进入紧急等待队列尾部。 

#### 线程通信方法

线程里数据是共享的，即同一变量占用同一个内存地址，所以用全局变量就可以轻松实现数据交流。

#### 线程同步方法

> 为什么需要线程同步：线程有时候会和其他线程共享一些资源，比如内存、数据库等。当多个线程同时读写同一份共享资源的时候，可能会发生冲突。因此需要线程的同步，多个线程按顺序访问资源。
>
> 从大的方面讲，线程的同步可分`用户模式的线程同步`和`内核对象的线程同步`两大类。
>
> - 用户模式中线程的同步方法主要有`原子访问`和`临界区`等方法。其特点是同步速度特别快，适合于对线程运行速度有严格要求的场合。
> - 内核对象的线程同步则主要由`事件`、`等待定时器`、`互斥量`、`信号量`等内核对象构成。由于这种同步机制使用了内核对象，使用时必须将线程从用户模式切换到内核模式，而这种转换一般要耗费近千个CPU周期，因此同步速度较慢，但在适用性上却要远优于用户模式的线程同步方式。

1. 互斥量Mutex：互斥量是内核对象，只有拥有互斥对象的线程才有访问互斥资源的权限。因为互斥对象只有一个，所以可以保证互斥资源不会被多个线程同时访问；当前拥有互斥对象的线程处理完任务后必须将互斥对象交出，以便其他线程访问该资源；互斥对象和临界区对象非常相似，只是其允许在进程间使用，而临界区只限制与同一进程的各个线程之间使用，但是更节省资源，更有效率。

2. 信号量Semaphore：
   - 信号量是一个整型变量sem，包含两个原子操作P( )、V( )，也就是对信号量的-1和+1操作；
   - 当一个线程访问临界区，信号量为正，则可以进行P操作，如果信号量等于0，则线程睡眠，等待信号量大于0；如果一个线程完成了对临界区的访问，会对信号量执行V操作，信号量+1，同时会唤醒睡眠的线程；
   - 信号量是被保护的变量，在初始化完成后，唯一改变一个信号量的值的办法就是通过P、V操作；
   - P操作能够产生阻塞，V操作不会阻塞；
   - 信号量允许同一时刻多个线程访问同一资源，最大线程数量不超过信号量的最大资源计数；
   - 如果信号量的最大资源计数为1，也就是只能取0和1，那么信号量就成了互斥量；
   
3. 事件Event（信号）：
   - 允许一个线程在处理完一个任务后，主动唤醒另外一个线程执行任务。事件分为手动重置事件和自动重置事件。手动重置事件被设置为激发状态后，会唤醒所有等待的线程，而且一直保持为激发状态，直到程序重新把它设置为未激发状态。自动重置事件被设置为激发状态后，会唤醒一个等待中的线程，然后自动恢复为未激发状态。
   
   - 事件对象状态：有信号状态、无信号状态；
   
   - 事件对象类型：人工事件(手动设置)、自动事件(自动恢复)；
   
   - 自动事件对象，在被至少一个线程释放后自动返回到无信号状态；
   
   - 人工事件对象，获得信号后，释放可利用线程，但直到调用成员函数ReSet()才将其设置为无信号状态。在创建Cevent对象时，默认创建的是自动事件。
   
   - 使用”事件”机制应注意以下事项：
   
     （1）如果跨进程访问事件，必须对事件命名，在对事件命名的时候，要注意不要与系统命名空间中的其它全局命名对象冲突；
   
     （2）事件是否要自动恢复；
   
     （3）事件的初始状态设置。
   
     ```c++
     #include "stdafx.h"
     #include<windows.h>
     #include<iostream>
     using namespace std;
     
     int number = 1;	//定义全局变量
     HANDLE hEvent;	//定义事件句柄
     
     unsigned long __stdcall ThreadProc1(void* lp) {
     	while (number < 100) {
     		WaitForSingleObject(hEvent, INFINITE);	//等待对象为有信号状态
     		cout << "thread 1 :"<<number << endl;
     		++number;
     		_sleep(100);
     		SetEvent(hEvent);
     	}
     	return 0;
     }
     
     unsigned long __stdcall ThreadProc2(void* lp) {
     	while (number < 100) {
     		WaitForSingleObject(hEvent, INFINITE);	//等待对象为有信号状态
     		cout << "thread 2 :"<<number << endl;
     		++number;
     		_sleep(100);
     		SetEvent(hEvent);
     	}
     	return 0;
     }
     
     int main() {
     	hEvent = CreateEvent(NULL, FALSE, TRUE, "event");
     	CreateThread(NULL, 0, ThreadProc1, NULL, 0, NULL);
     	CreateThread(NULL, 0, ThreadProc2, NULL, 0, NULL);
     	Sleep(10*1000);
     	system("pause");
         return 0;
     }
     ```
   
     
   
4. 临界区Critical Section：

   - 一段对临界资源访问的代码，称为临界区；临界区只允许`同时一个线程`对其访问，如果同时有其他线程试图访问临界区，会被挂起，直到临界区中的线程退出临界区；`临界区是指线程中的一段需要访问共享资源并且当另一个线程处于相应代码区域时便不会被执行的代码区域.`

   - 临界区在使用时以`CRITICAL_SECTION`结构对象保护共享资源，并分别用`EnterCriticalSection()`和`LeaveCriticalSection()`函数去标识和释放一个临界区。所用到的`CRITICAL_SECTION`结构对象必须经过`InitializeCriticalSection()`的初始化后才能使用，而且必须确保所有线程中的任何试图访问此共享资源的代码都处在此临界区的保护之下。否则临界区将不会起到应有的作用，共享资源依然有被破坏的可能

     ```c++
     #include "stdafx.h"
     #include<windows.h>
     #include<iostream>
     using namespace std;
     
     int number = 1;	//定义全局变量
     CRITICAL_SECTION Critical;		//定义临界区句柄
     
     unsigned long __stdcall ThreadProc1(void* lp) {
     	while (number < 100) {
     		EnterCriticalSection(&Critical);
     		cout << "thread 1 :"<<number << endl;
     		++number;
     		_sleep(100);
     		LeaveCriticalSection(&Critical);
     	}
     	return 0;
     }
     
     unsigned long __stdcall ThreadProc2(void* lp) {
     	while (number < 100) {
     		EnterCriticalSection(&Critical);
     		cout << "thread 2 :"<<number << endl;
     		++number;
     		_sleep(100);
     		LeaveCriticalSection(&Critical);
     	}
     	return 0;
     }
     
     int main() {
     	InitializeCriticalSection(&Critical);	//初始化临界区对象
     	CreateThread(NULL, 0, ThreadProc1, NULL, 0, NULL);
     	CreateThread(NULL, 0, ThreadProc2, NULL, 0, NULL);
     	Sleep(10*1000);
     	system("pause");
         return 0;
     }
     ```

5. 条件变量：

#### 生产者-消费者问题

1. 一个生产者、一个消费者、共用一个缓冲区；
2. 一个生产者、一个消费者、共用n个环形缓冲区；
3. 一组生产者，一组消费者，公用n个环形缓冲区；

#### 操作系统调度方法（进程调度方法）

1. 先来先服务FCFS
   - 如果进程在执行中阻塞，那么队列中的下一个会得到CPU；
   - 该调度方法非常的简单，但是平均等待时间波动较大，花费时间少的任务可能排在花费时间长的任务后面；
2. 最短作业优先SJF
   - 按照预测的完成时间来将任务入队；（非抢占）
   - 该调度算法拥有最优平均等待时间，但是可能导致饥饿，比如连续的短任务可能会使长任务饥饿，另外就是需要预知未来，需要预估下一个CPU突发的持续时间；
3. 最短剩余时间优先SRT
   - 按剩余运行时间的顺序进行调度。(最短作业优先的抢占式版本)。吞吐量高，开销可能较大，提供好的响应时间；可能导致饥饿问题，对长进程不利。
4. 最高响应比优先HRRN
   - 在SJF的基础上，考虑到了饥饿现象（不可抢占、关注进程等待了多长时间、防止无限期推迟）；
   - R = (w + s) / s，选择R值最高的进程进入CPU，w是等待时间、s是执行时间；
   - 不可抢占、同样需要执行时间s，所以难以获得精确的时间，只能预估；
5. 时间片轮转
   - 将所有就绪进程按 FCFS 的原则排成一个队列，用完时间片的进程排到队列最后。抢占式（时间片用完时），开销小，无饥饿问题，为短进程提供好的响应时间；
   - 若时间片小，进程切换频繁，吞吐量低；若时间片太长，实时性得不到保证，极限情况下退化为FCFS。
6. 优先级调度算法
   - 为每个进程分配一个优先级，按优先级进行调度。为了防止低优先级的进程永远等不到调度，可以随着时间的推移增加等待进程的优先级。
7. 多级反馈队列调度算法MFQ
   - 一个进程需要执行 100 个时间片，如果采用时间片轮转调度算法，那么需要交换 100 次。多级队列是为这种需要连续执行多个时间片的进程考虑，它设置了多个队列，每个队列时间片大小都不同，例如 1,2,4,8,..。进程在第一个队列没执行完，就会被移到下一个队列。这种方式下，之前的进程只需要交换 7 次。每个队列优先权也不同，最上面的优先权最高。因此只有上一个队列没有进程在排队，才能调度当前队列上的进程。可以将这种调度算法看成是时间片轮转调度算法和优先级调度算法的结合。

#### 基于优先级调度的方法存在什么问题

优先级调度可能会产生饥饿的问题，解决方法是偶尔提升一下优先级

#### 程序跟进程比较，是否一一对应



#### 进程线程地址空间



#### 虚拟内存 常驻内存 共享内存？实际内存怎么计算？（常驻-共享）



#### 既然多优先级队列+时间片轮转调度这么好，为什么还会出现死机情况?



#### 系统调用的过程



#### 产生死锁的原因 







## 3. Linux

#### 常用的linux指令讲一下





## 4. 设计模式

#### 手写单例模式-线程安全



## 5. 数据库



#### Redis的5大结构



#### Redis 的 zset 的底层数据结构



#### Redis 实现分布式锁



#### 数据库索引



#### B与B+的区别，B+树高度低带来的好处是啥







## 6. C11

#### 左值右值的区别



#### 完美转发





#### C++ 4种cast 区别和主要使用场景





## 7. 网络编程

#### 多进程和多线程



#### 阻塞IO、非阻塞IO、异步IO



#### 线程池



#### epoll模型的工作原理



## 8. C++

#### malloc和new

  malloc分配内存 

  new是操作符重载 分配内存+构造函数 

#### 构造函数和析构函数能不能是虚函数



#### 模板与继承的对比，模板的缺点，模板是如何编译的

模板默认内联，且没有虚表指针，调用开销小 ；

编译慢、编译产物更占用内存 ；

 编译期碰到了就生成需要的代码，每个.o都会有用到的定义，所以链接的时候要去掉重定义，因此编的慢；

#### 智能指针-STL三种（细、手写智能指针）





#### 浅拷贝与深拷贝



#### 一个类A，里面有两个整型变量，一个成员函数，两个虚函数，问这个类占多大内存



#### 空悬指针与野指针？怎么避免空悬指针？



#### NULL与nullptr差别



#### 虚函数以及虚函数表  虚函数表原理



#### .cpp与.c编译时的区别



#### 数组和链表的区别



#### 重载与重写



#### C++ 的内存管理是怎么实现的



#### 指针和引用



#### strlen和sizeof



#### static关键字



#### 时间复杂度，如果一个算法的时间复杂度是O(1)代表的含义是什么



#### 头文件重复的包含和重复的引用



#### struct与union有什么区别？ 



#### struct内存对齐原则有哪些？



#### inline关键字有什么作用？ 



#### 内联函数与宏定义的区别？ 



#### 什么是内存泄漏？如何避免？

<u>为什么会存在内存泄漏问题？</u>

通常来说，一个线程的栈内存是有限的，通常来说是 8M 左右（取决于运行的环境）。栈上的内存通常是由编译器来自动管理的。当在栈上分配一个新的变量时，或进入一个函数时，栈的指针会下移，相当于在栈上分配了一块内存。我们把一个变量分配在栈上，也就是利用了栈上的内存空间。当这个变量的生命周期结束时，栈的指针会上移，相同于回收了内存。

由于栈上的内存的分配和回收都是由编译器控制的，所以在栈上是不会发生内存泄露的，只会发生栈溢出（Stack Overflow），也就是分配的空间超过了规定的栈大小。

而堆上的内存是由程序直接控制的，程序可以通过 malloc/free 或 new/delete 来分配和回收内存，如果程序中通过 malloc/new 分配了一块内存，但忘记使用 free/delete 来回收内存，就发生了内存泄露。

<u>避免方法：</u>

- 尽量使用智能指针，而不是手动地去管理内存
- 使用 std::string 来替代 char*。使用 std::string 无需关心内存管理，它已经很好地在内部实现了内存管理。
- 无论如何都不要使用一个裸指针，除非逼不得已（比如要用它来指向一个旧的库）
- 在 C++ 中最好的避免内存泄漏的方式是尽量少的使用 new 和 delete 函数，最好是0使用。当你确实需要动态内存的时候，构造一个 RAII 类来在析构的时候自动地 delete 所有动态申请的内存。 RAII 类在构造函数中申请内存，而在析构函数释放内存，所以这可以保证在 RAII 对象离开作用域的时候，自动地释放内存。
- 在你每次确实需要申请动态内存的时候，先写 new 和 delete 语句。然后再在中间添加你的功能模块！这样做确保你不会忘记释放内存！

#### strcpy和memcpy的区别？



#### 为什么引入抽象基类和纯虚函数？



#### 为什么传指针比传引用安全？



#### 虚函数和纯虚函数有什么区别



#### 构造函数与析构函数的调用顺序是怎样的



#### 多态与底层



#### HashMap的底层实现；HashMap是否是线程安全的





## 9. 其他

#### 软件工程常用开发模型？ 软件开发流程 



#### Utf-8几个字节，汉字呢？



#### Memory load



#### move，forward



#### 动态链接库原理？静态链接库跟动态链接库异同比较？动态链接库实际存了哪些东西? 怎么引用动态链接库，什么是动态库、静态库？



#### gdb常用命令有什么



#### 哈希冲突有哪些解决方法



#### LRU如何实现的



## 10 手撕算法

leetcode 92 反转链表从m到n

LFU leetcode 460

场景类算法题有依赖关系的进程启动管理

二分法求浮点数平方根，不得递归，精度要求0.001 
  反转链表 

多线程打印ABCD 

手写大小端转换函数 

手写socket断点续传文件

手撕智能指针

斐波那契数列 

设计一个数据结构 list:  rpush rpop lpush lpop index 五种方法的时间复杂度均为 O(1)

二叉树的最大路径和、二叉树最大和的路径；

判断链表是否有环

判断两个链表是否相交

DFS、BFS

建立一个双向链表；

整数转化成字符串；

单链表只遍历一次，要找到链表的中间位置要怎么做；

层次遍历二叉树的过程；

迭代二叉树的深度；

0-n-1中缺失的数字，两种方法；

二叉搜索树后序遍历；

K个一组反转链表；

IP地址字符串转换为32位整数；

两个有序数组，其中一个有足够空位，不使用额外空间排序到含空位数组中；

求二叉树两个节点的最小距离；

对大规模数据进行去重；

找到两个链表的首个公共节点；

寻找无序整数数组中第一个缺失的正数；

对给出一个数组中的每个元素求因数个数；