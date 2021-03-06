# `ACBash`的毕设🌟

## 题目：==基于FlexE的传送网切片编排方法的设计与实现==

##执行人：[ACBash](https://github.com/axing521)

##指导人：[王颖老师](https://teacher.bupt.edu.cn/wangying3/zh_CN/index.htm)

---

> 2021/10/26🎶

* keyword🔑
  * FlexE
  * 传送网切片编排
* synopsis📕
  * `网络切片`是一种`按需组网`的方式，让运营商在统一的基础设施上切出多个虚拟的端到端网络，通过引入`智能编排`功能，可以在满足差异化的`多租户需求`的同时，提高基础设施网络的`资源利用率`。
  * 柔性/灵活以太网【FlexE】是由OIF【国际标准组织光互联网论坛】主导的一种新技术。通过在`MAC`层与`PCS`层中新增`FlexShim`层，实现网络灵活性/多速率/刚性接口等特性。其捆绑/通道化/子速率等功能，可以与IP/Ethernet技术良好对接，提供了基于以太网物理接口的切片隔离机制，在承载移动业务/数据中心互联等方面有广阔的应用前景。
  * 本课题旨在基于`FlexE`网络切片开通流程/技术特点，并结合**典型租户业务需求**，对基于FlexE的传输**网络切片编排问题进行分析和建模**，在此基础上设计相应的启发式算法求解切片编排问题，形成智能化的资源分配方案，最后设计和搭建实验环境，对所提出的算法进行验证和评估。
* mission🚀🚀
  * ❌学习FlexE，网络切片的相关知识	
  * ❌学习和分析基于FlexE的切片编排流程，建立基于FlexE的传送网切片编排问题模型
  * ❌设计一种启发式算法，用于解决在满足差异化租户需求的情况下，最小化资源分配的问题
  * ❌编程实现上述算法，搭建仿真环境，设计评价指标，对设计实现的切片编排算法进行比较和评估
* output📚
  - [ ] 网络切片编排的数学模型
  - [ ] 最小化网络切片资源分配的启发式算法的设计与实现
  - [ ] 仿真环境的设计与搭建
  - [ ] 基于评价指标的算法仿真结果和性能分析

---

### FlexE技术白皮书

* problem
  * client/group架构
  * why 5G？
  * 开销帧标记位可以CRC校验，why？
  * PCS编码？
* background
  * 大家都用光纤了，底层接口/模块/速率固定，调整成本高，所以FlexE来灵活配置带宽。
* advantage
  * 接口速率可变（不再受制于阶梯型速率体系
  * 接口带宽可以按需灵活满足，不受制于光传输网络能力
* how make it？
  * ![image-20211026221640465](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKwXDVRARjGI44uwiaFygGNkMxhwMCjs103m79h4p4ibCnpfwwMKwahSKYQh0SZS6Kiax3ew0ROAicQk0g/0?wx_fmt=png)
  * ![image-20211026221721093](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKwXDVRARjGI44uwiaFygGNkMLxnLmDn4QjkwfGc2QvibgEDslLib8ibuJTfsI2N25hiaA81RhavRKCNsZQ/0?wx_fmt=png)

> 2021/10/27🛠

* function

  * 捆绑：捆绑多个物理接口，使多个PHY一起工作，以支持更高速率。

    ![003](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhshVE31hWCwOibH4sww1WVmm52JgB7Go7ooFXloRbBUDQdqAcj0X1tElA/0?wx_fmt=png)

  * 通道化：多个低速率数据流共享一个或多个PHY。

    ![003](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsianibdOHqYqQyeGk0ZhWXAXChFqnpOWxY6WIDIusKnibSRwyWvpd1q9ug/0?wx_fmt=png)
    
  * 子速率：一个低速率的数据流共享一个或多个PHY。
  
    ![010](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsjKgvkPJucVG71KOQ6CP44oIMx6CNnWgBMdCF0DWgYxUE4PMTFDEqyA/0?wx_fmt=png)

* how work it?

  * FlexE帧结构：

    64/66B数据块 => slot => 20blocks => Calendar | 形成颗粒度5G的Slot数据承载通道。

    ![011](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsDofOiabPS1IoPF8VKjQh3K9rgn93FoJKklyic7z43pGIJEhzjumvKqTQ/0?wx_fmt=png)

  * 映射关系：FlexE-Client  <=> FlexE-Group-Slot

  * FlexE-Overhead-Slot：FlexE开销时隙，实际为按照64B/66B编码形成的数据块。

  * FlexE-Overhead-Frame：FlexE开销帧，由8个开销时隙组成。

  * FlexE-Overhead-MultiFrame：FlexE开销复帧，由32个开销帧组成。其中，前16个开销帧标记位为**“0”**，后16个标记位为**“1”**。（可以CRC校验，why？）

    ![012](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhs3lC34GuDRRFGSGdAsMOM5b0ibbj77iaLwZkFMviag83Q910I1q7HcyUbA/0?wx_fmt=png)

  * FlexE开销时隙中包含控制字符与“O Code”字符等信息。

    这些信息可以确定第一个FlexE开销帧，从而在通信的两端间简历一个管理信息通道，实现对接的两个接口之间配置信息的预先协商，握手等。

  * Client/Slot映射机制

    * FlexE-Client数据流在发送端的FlexE-shim/group数据通道中映射到Slot，然后等这些Slot映射信息，位置等内容传送到接收端后，接收端可以从数据通道中根据发送端的Slot映射等信息恢复该FlexE-Client的数据流。（就是说，告诉你哥们，我这边是走这几个管子的，你瞧瞧）

    * FlexE-Mux：FlexE-Client 的数据 => FlexE-Group-Slot

    * 将不同带宽的 FlexE Client 数据插入到 Calendar 中。只要 Calendar 中有足够的 Slot，分配给特定 FlexE Client 的 Slot 并不都需要位于 FlexE Group 的同一个 PHY 上，这样可以同时使用多个 PHY 并行发送 FlexE Client 的数据流，提高了发送效率。

      ![013](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsrEic9WPnZKba26r2oJCUkq6CMz2F7AWs6E3LtvbQeeeO67VcS4QX4TQ/0?wx_fmt=png)

    * FlexE-Demux：FlexE-Group-Slot => FlexE-Client 的数据

    * FlexE 将从多个 PHY接收到的 Slot 数据重新拼装，恢复为 2 个 FlexE Client 数据。

      ![014](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhseQyx04QPVPFo3HicbfcKdxlWzibu5CPqwgm7Sae9wicNiaYpedic3llJdtg/0?wx_fmt=png)

  * Slot/Calendar更改机制

    * 允许映射关系实时变化，以实现带宽动态调整。

      ![015](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsJjzCcJnCxibrRTaRsr40MsEA9YfTiauicxYoJr8nlnChicZvwr3Hyo4YDA/0?wx_fmt=png)

* instance

  * 带宽按需分配，通道隔离？，低延时保障？

  * 与`SDN`技术结合，支持基于业务体验的未来网络架构，能够支撑未来的高带宽视频，VR，5G等业务发展。

  * 标准对于FlexE在**光传输网络**中的映射定义了3种模式：

    * FlexE Unaware Transport
    * FlexE termination in the Transport
    * FlexE Aware Transport

  * FlexE Unaware Transport

    ![016](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhsVziayWeAI7NowXcgVot8v9EKLQjecuQnUu7qKZv893DPgR8nMgNV12w/0?wx_fmt=png)

    Flexe Shim 将 Flexe Client 的数据和 Flexe Group 里捆绑的 PHY 进行映射转换，每个 PHY 的数据都独立的在传输网络进行传输，传输网络透明承载 FlexE 数据。

    传输网络不区分是否是 FlexE 数据，传输网络按照 PCS 编码进行透明传输，在不升级传统传输网络的情况实现了带宽的捆绑。

  * FlexE termination in the Transport

    ![017](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhs3Evy5rJDL9AMR8bEMaNec7BmaicXGmUiazicZmagPNFk0wB7udqCEH3vA/0?wx_fmt=png)

    ​                                                类路由器？

    由于两个 FlexE Shim 的距离不超过以太网的最大传输距离（40km），所以无法在 PHY 之间远距离传输数据。FlexE termination in the Transport 模式下，FlexE 流量在传输网络中不通过 FlexE Group里捆绑的 PHY 进行传输，FlexE 流量在进入传输网络前被边缘设备被终结。如图 12 所示，在该模式下，在光传输网络的边缘设备上部署 FlexE 接口并恢复出 FlexE Client 数据流，再进一步映射到光传输网络中进行承载传输，解决长距离传输问题。

  * FlexE Aware Transport

    ![018](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzmKQCBUnbRmuNc2b00KPhspPqlOsprwtudELib3g1IomG5hZRj2uu4JfpDz09aYfJ4DBw3jjVQy9Q/0?wx_fmt=png)

    FlexE Aware Transport 模式主要利用 FlexE 的子速率特性，在 PHY 上低速传递数据流量，适配光传输网络的波长。如图 13 所示，该模式下，光传输网络的接入设备对 FlexE 接口的 PHY 中无效的数据进行丢弃，并在出方向的设备上对丢弃的 Slot 恢复填充，从而光传输网络可以传输小于 FlexE
    接口 PHY 带宽的数据。(减小网络带宽压力是吧？)

---

> 2021/10/30🏀

### 中国移动SPN技术白皮书

#### 1.需求和愿景

##### 1.1 驱动力

###### 1.1.1 无线网接入业务

​	3GPP定义的3种典型的5G应用场景将会给传输网络带来以下挑战：

* 流量将增长1000倍

* 连接数将增加100倍

* 10Gbps的峰值数据速率

* 10Mbps以上的用户体验速率

* 低延迟和更高的可靠性

  * 与4G相比，5G从终端设备到核心网的延迟应该降低5 - 10倍。对于涉及人身和财产安全的服务，端到端可靠性应提高到99.999%。

* 提高频谱效率

  * 根据国际电联的说法，outdoor中IMT-A的平均频谱效率的最小值应该是2bps /Hz到3bps /Hz。为了解决业务量爆炸性增长带来的频谱资源短缺问题，5G的平均频谱效率应比4G高5 ~ 10倍

    ![222](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxkiaNA9wzuNKNbOlXJk0gEE58lictpKB59CZPaIfIF4wS8iaV36xR0opJYlPMicfWyiaicM3bYyHKoE5QQ/0?wx_fmt=png)

###### 1.1.2 居民宽带接入业务

​	人们对更好的体验的渴望是无止境的，这将进一步推动住宅宽带的质量。为了追求更好的体验，视频业务将对传输速率和延迟有更严格的要求。此外，运营商在服务发放和故障处理上花费了大量时间，导致用户体验下降。可以引入基于云的战略，以提高管理效率。

###### 1.1.3 企业网络接入业务

​	对于政府和企业客户，运营商提供不同节点间的宽带接入和线路租赁服务。信息技术产业的快速发展将在不久的将来耗尽现有企业专用线的带宽，推动对高速专用线的持续需求。针对不同的场景，私线主要有三种类型:IDC互联私线、高价值政企私线和中小企业私线。云服务有利于提高专用线路的管理能力。同时，云MEC将被进一步分配到网络边缘，这将给运输网络带来巨大的挑战。简单的带宽租赁不能满足大型企业的需求。**与传统的专线业务相比，虚拟网工作租赁在未来可能是一种很有前景的业务方式。**

###### 1.1.4 数据中心网络互联业务

​	基于dc的网络结构将改变传输网的传统业务模式。传输网络应支持更灵活的流量和多样化的可靠性。指出了SDN技术是DCI服务的关键部分。控制器应实现DCs之间的广域网级互连，协调器应实现DCs之间的资源调度。SDN技术还支持元素分离的转发和控制以及集中路由计算。

#####1.2 新传输网络的关键需求

​	数字社会的到来将对通信基础网络产生深远的影响，现有通信网络的流量模式将发生巨大的变化。连接密度和交通密度将以前所未有的速度增长，地理网络覆盖范围将大大扩大。不同类型的用户对网络提出了个性化的需求，具体行业的实时交互要求非常高。因此，下一代传输网络架构必须满足以下要求:

* 高带宽
  * 传输网络需要提供低成本和高带宽的能力。网络吞吐量应大于1gbit /s /用户，支持视频、全息、虚拟现实等应用的持续发展。
* 低延迟
  * 它应该支持优于1ms的端到端延迟，以满足交互体验和工业控制的严格要求。
* 灵活可变的连接
  * 连接总数增加了十几倍，在10000节点以上的庞大网络中，新网络应能满足Full-Mesh数据连接。
* 网络的开放性
  * 支持标准的南向接口和北向接口。通过这种方式，他们有潜力利用SDN控制平面的ver的饱和性和开放性来实现他们在虚拟或物理部署或两者中生成的业务控制。
* 网络分片
  * 多样化的业务带来多样化的网络需求。未来，传输网络应具备**动态的网络分片能力**，以满足多样化的业务需求。网络应同时具备**硬隔离**和**软隔离**的能力。
* 高可靠性
  * 它应该为AR、工业控制和远程医疗等新服务提供超高可靠性连接。
* 智能化运维管理
  * **服务模型从根本上改变了。网络分片和全网格流量需要更智能的网络运维系统来降低运营成本。**

##### 1.3 SPN 愿景

​	Slicing packet network（切片分组网络）

​	切片分组网络(SPN)的重点是构建一个高效、简化且高带宽的传输网络，以支持城域网络上的不同业务。

* 新架构
  * 新的技术架构提供了一个低成本和简化的传输网络。网络的带宽提高了100倍，每一位的成本降低了10 ~ 100倍。
* 新服务
  * SPN的重点是支持运输网络上的新服务。延迟减少10 ~ 100倍，服务连接数增加100倍
* 新操作
  * 全新运维平台提供**敏捷的服务部署和运维能力**。运营成本低了10倍

​	SPN定位为基于以太网生态系统的下一代融合传输网络，实现高带宽、低延迟、高效率的综合业务传输。SPN主要承载以下业务:无线业务、企业业务、住宅业务、云互联业务。

####2.原则和架构

##### 2.1 SPN 设计原则

​	SPN利用高效的以太网生态系统，提供低成本、高带宽的传输网络服务。通过多层网络技术的高效融合，实现了**灵活的软隔离切片和硬管道切片**，提供了从L0到L3的多层业务传输能力。SDN集中管控，创新实现开放、敏捷、高效的网络运营。SPN原则如下:

* 包友好
  * 利用以太网技术(IEEE 802.3以太网、OIF **FlexE**和创新切片以太网)，SPN共享IP/以太网生态系统(光模块、协议和芯片组)，并支持主流包客户端
* 多层网络技术融合
  * 通过IP、以太网、光技术的高效转换，可以实现从L0到L3的分层组网，可以建设各种类型的管道。以太网分组调度用于实现分组业务的灵活连接。采用创新的分片以太网数据单元流调度，为业务提供硬管道隔离和带宽保障，并提供低延迟的服务传输网络通道。Optical-layer波长梳理，支持平滑扩容和大粒度业务梳理。
* 高效的软硬切片
  * 提供了高可靠的硬切片和弹性可伸缩的软切片功能。这种能力分离一个物理网络的后期资源，以运行多个虚拟网络，并为多种类型的服务提供差异化的基于sla的传输网络服务。
* SDN集中管理和控制
  * SDN有助于实现开放、敏捷、高效的网络运维。服务提供运行和运维实现自动化。SPN可以实时监控网络状态，触发网络自优化。基于sdn的融合管控架构还提供了简化的网络协议、开放的网络、跨网络域、跨技术的业务协调等功能。
* 运营商级的可靠性
  * 支持网络级分层OAM和保护功能。OAM用来监控逻辑层/网络连接/网络服务=>实现全方位网络可靠性，支持高可靠性业务传输。
* 高精度同步
  * 实现了基于带内时钟和时间同步传输，具有高可靠性、高精度、高效率。
* 灵活的服务调度
  * SPN采用灵活的隧道、寻址和转发技术，对P2P、P2MP和MP2MP业务进行灵活调度。采用分片以太网寻址和转发的方式实现了网络第一层业务调度。通过MAC和MPLS寻址和转发ing实现二层业务调度。通过IP寻址和转发实现三层业务调度。

![020](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxkiaNA9wzuNKNbOlXJk0gEE9hO991NfR8dShhJvypcdsJ6BBWYP4bicUg2XbdVfJ2V6cV3lJhK2q1w/0?wx_fmt=png)

##### 2.2 技术架构🚀🚀

​	SPN基于ITU-T网络模型，以以太网为基础技术，支持IP、以太网和CBR业务的综合传输。SPN技术架构由分片包层(SPL)、分片通道层(SCL)/切片传输层(STL)/时钟同步模块/管理控制功能模块组成。

![021](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxkiaNA9wzuNKNbOlXJk0gEEtuyGvRAYKCoSQTt2gpeEia9NyhU1NxcMXtrXCVKFTRwfxLUM50FicZiaw/0?wx_fmt=png)

* SPL
  * SPL通过IP、以太网和CBR业务的寻址、转发和传输通道的封装，提供L2VPN、L3VPN、CBR透明传输等多种业务。SPL通过IP、MPLS、802.1q和物理端口等多种寻址机制实现业务映射，并提供业务识别、流量工程和QoS (quality of service)保障。对于报文业务，SPL提供SR-TP (segment routing-transport profile)隧道，并提供面向连接和无连接的trans端口通道。基于源路由的段路由技术允许入口使用一组段(MPLS标签)来标识每个tunnel转发路径。与传统的隧道技术不同，中转节点不需要维护分段路由隧道的路径状态信息，允许灵活的路径调整和网络pro的可移植性。SR- tp隧道技术增强了SR隧道的运维能力，支持双向隧道和端到端服务级OAM检测
* **SCL**
  * 为网络服务和片提供端到端信道化组通道。SCL采用创新的切片以太网(SE)实现以太网物理接口和**FlexE**绑定组的时值处理，提供基于端到端以太网的虚拟网络连接，并在L1上建立低延迟、硬隔离的切片通道，实现多业务传输。SCL利用OAM和保护功能对以太网channels进行切片，实现端到端性能监控和故障恢复
* STL
  * 采用IEEE 802.3以太网物理层技术和OIF FlexE technology实现高效的高带宽传输。以太网物理层由50GE、100GE、200GE、400GE等新型高速以太网接口组成。通过以太网产业链，实现低成本、高带宽的网络建设，支持跳距可达80公里的主流组网应用。对于需要更高带宽可扩展性和更长的传输距离的应用，SPN采用以太网+DWDM组合nation实现10t规模和数百公里长距离的网络。

> 2021/10/31😅

####3 切片以太网

##### 3.1 切片以太网

###### 3.1.1 切片以太网需求

​	未来的多种业务(如5G eMBB、URLLC和mMTC)支持提高了未来传输网络的端到端网络分片需求。不同行业的不同业务需求不同，可以通过不同的QOS保证网络技术(如分组/时分复用)来支持。未来的端到端可分片的传输网络应能够**避免在现有网络中增加新业务带来的负面影响，从而降低运营成本**。

​	网络切片可以将一个物理网络中的相关业务功能和网络资源组织在一起，形成多个完全独立、自治、独立的逻辑网络切片，每个切片可以满足特定的用户/业务需求。

​	高带宽、海量连接、低延迟的服务对传输网络提出了更高的要求。有效隔离传输网的工作资源，满足各种业务的差异化SLA需求，是对防未来的传输网提出的新的挑战。当前的切片/隔离机制可分为软切片/隔离和硬切片/隔离两种。在具有统计复用能力的分组传输网络中，基于分组(如MPLS)的VPN技术被认为是一种有效的软分片/隔离解决方案。但是基于报文的VPN技术作为一种软分片/隔离机制，不能解决不同业务的带宽抢占问题，不能保证业务的SLA。特别是在考虑提高带宽利用率的情况下，很多数据中心网络采用了jumbo packet技术，这就延长了片之间的报文调度时间，影响了其他片的SLA能力。IP/以太网友好的未来多业务传输网络需要增强硬切片/隔离能力。Ethernet以其简单、高效、低成本成为主流技术，其硬切片/隔离机制应基于以太网。

###### 3.1.2 切片以太网技术

​	在工业以太网中有一些基于硬切片/隔离的努力。例如，FlexE和OIF之前开发的MLG提供了基于以太网物理接口的硬切片/isola机制。然而，FlexE作为一个inter面级技术，在这个阶段没有端到端组网考虑，以满足运营商网络的需求。在SPN体系结构中，提出了一种创新的新网络层——分片以太网(SE)，以提供基于以太网的分片/隔离能力。分片以太网防止L2/L3包存储和table查找，提供端到端硬隔离管道的第1层网络能力，节点低延迟。切片以太网(SE)有以下特点:

* 切片以太网交叉连接(SE-XC):
  * 支持守护延时极低节点，业务硬隔离。提供端到端SPN分片以太网通道，支持端到端以太网L1组网。	
* 随需应变的E2E OAM
  * 根据需求和配置，采用按需分片以太网OAM& p消息es替代以太网IPG空闲块，对端到端分片Ethernet信道提供足够强大的OAM和保护功能。可在1ms内完成端到端保护切换，并可根据需要进行系统误码检测。
* CBR服务的透明映射
  * 采用特定的转码和数据速率调整机制，将各种服务透明地映射到slice以太网通道中，通过该通道可以透明地传输以太网和/或非以太网CBR服务。

![022](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922yDHibTedqIIicUB76uChUic16g7oHW5HVoTYGg0icdX9xBvIcic2DrzBCCA/0?wx_fmt=png)

​	分段以太网L1组网技术是对L2以太网L2.5 MPLS和IP L3组网的有效改进。SPN通过引入slice Ethernet，具备基于以太网的多层组网能力，满足fies差异化多业务传输需求。端到端SPN分片Ethernet信道用于高值专线。通过二层或三层单包调度和/或支持包统计复用的软隧道实现高效的带宽利用。对于低延迟分组业务，可以将第二层和第三层分组调度限制在边缘PE节点上，在网络中具有的P节点上，第1层的切片以太网交叉连接(Slicing Ethernet cross-connecting, SE-XC)可以以低延迟快速转发同一SPN切片以太网通道的分组。

![023](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922Hka7abcI8gJaYsaAvER4mqMxMYCJ5X7mbMCGg7zBibsCyVFQRW7icTNw/0?wx_fmt=png)

##### 3.2 高效的高带宽技术

######3.2.1 高效高带宽的需求

​	随着5G移动、高清视频、数据中心互联、物联网业务的快速发展，传输网络带宽将快速增长。5G移动新无线电(NR)采用更宽的频谱和更高的频谱效率，实现了每个小区10gbit /s的峰值带宽体验，比4G移动网络提高了100倍。随着4K、8K和VR视频业务的发展，单个用户的带宽需求将增加到100mbit /s到1gbit /s。因此，传输网络迫切需要新的高效技术来适应业务带宽的发展需求。在ad的情况下，单个用户的平均每用户收益(ARPU)无法大幅提高。因此，带宽的增长不应大幅增加网络建设成本。

​	以太网接口是通信领域中应用最广泛的接口技术。这也是最具成本效益的界面。经过近四十年的发展，以太网接口已经形成了成熟的产业链，在电信和IT网络中得到了广泛的应用。近年来，由于Internet的发展，高速以太网接口得到了迅速的发展，以满足日益增长的业务带宽需求。

###### 3.2.2 高效高带宽技术

​	在需求和技术的驱动下，同时发展以太网接口物理层、低成本单通道技术和高性能多通道技术，以达到最佳的成本效益。50GbE、200GbE、400GbE速率、四电平脉冲幅mod (PAM4)和25G光器件逐渐成为下一代以太网接口的主流。

![024](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922r4AiaMHdde1BeOP3RW5rniaAibS2KhxibS6W7v6ibI3agbZE1TotRxzl1LQ/0?wx_fmt=png)

​	基于25G光器件和前向error correction (FEC)、PAM4等关键技术，速率提高了一倍，实现了Lane 50gbit /s的数据速率，降低了比特成本。在单线50GE的基础上，采用多线模式开发低成本高速的以太网接口，如200GE、400GE等。基于pam4的高速以太网技术标准，包括50GE、200GE和400GE标准，在802.3bs (200GE和400GE)和802.3cd (50GE)中已经开发出来。该标准将于2018年发布，得到了全行业的广泛认可。

​	50GE、200GE、400GE接口涉及以下关键技术:

* FEC:采用成熟的KP4 FEC技术，用于长途运输任务。
* PAM4:在波特率不变的情况下，使用PAM4使数据速率翻倍，有效降低光接口成本。

​	**FlexE于2014年提出，预计将应用于DCI, DCI的主要需求是将MAC层数据速率与PHY层数据速率解耦。FlexE支持MAC层和PHY层之间的灵活映射，支持多个PHY层的绑定，并实现各种高带宽接口。在传输网络领域，FlexE主要用于提供可变的数据速率，与网络上的可用带宽值匹配，支持多波长应用，并节省光纤。**

​	在SPN组网中，FlexE将多个光接口绑定在一起，通过低成本的低速率光模块提供高速率以太网接口。例如，4个200GE以太网接口绑定，单个端口提供800gbit /s的容量。此外，还与柔性粘接结合使用波分复用，SPN系统可以支持单光纤与太比特级容量。

##### 3.3 确定性低延迟技术

​	与LTE时代相比，确定的超低延迟要求是5G服务器恶习的一个重要变化。eMBB业务端到端加密时延为10ms, URLLC业务端到端加密时延为1ms，高于LTE业务

​	传统报文设备转发业务报文时，报文在出接口上排队，延迟时间长，约为数十μs。在网络拥塞的情况下，时延会变长，甚至达到毫秒级，无法满足5G时代低时延服务的需求。5G URLLC业务要求在单个节点上实现低转发延迟μs和低延迟jitter。

###### 3.3.1 包低延迟

​	报文低时延转发技术改进了低时延报文快速转发的转发机制，不需要进行队列处理。当出接口转发其他优先级较低的报文时，低时延的业务会抢占资源，实时被rap无响应转发。

![025](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922qTjtncicS9Uvt6dSibv589MyRWSS2icwfT7cXnic3W0ibk80WF4P8Vc6KLA/0?wx_fmt=png)

###### 3.3.2 切片以太网低延迟技术

​	传统的报文设备一跳一跳地转发客户业务报文。在每个节点上，对数据包进行PHY层处理、分组组装、地址和标签查找转发、发送前队列调度ted。这些过程会导致不可预防的随机高节点延迟，最高可达几十微秒。即使采用包快速转发机制，在一定程度上降低了节点时延，但端到端时延变化(jitter/PDV)仍不能满足对时延敏感的5G业务需求。因此，针对确定性低延迟的进一步延迟cy优化解决方案需要考虑ered。

​	基于切片以太网的确定性低延迟解决方案引入了SE-XC技术，以取代传统的存储转发机制。用户报文在网络的中间节点上不需要解析，业务流转发过程几乎是实时完成的。比萨饼盒设备的节点延迟可以优化到微秒级。

​	例如，PE将用户业务报文映射到SPN通道。中间节点ate执行SE-XC操作后，业务报文直接转发到的出接口，无需排队等待。因此可以实现极低的转发延迟

![026](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922ibCyuZPQYzQXQGSKy0F62ibPYtZYUcP08btZNqf1KnibqwAxibicIkTErCw/0?wx_fmt=png)

​	如图所示为SPN通道切换过程。FlexE A组和B组表示设备上的两个逻辑接口。每组可以有m条物理链路。假设配置了三个FlexE客户端服务。

* 蓝色的Client1映射到时间段1、4、7，并切换到右侧的时间段5、8、10。
* 红色的Client2映射到12、16、18分时段，并切换到左侧的9、11、12分时段
* 黄色的Client3为直通服务，映射到左侧的13、15、17、19，切换到右侧的1、3、4、7

​	黄色为直通业务，A组接收m个PHY芯片的码块，恢复m × 20的码块序列。A组根据预先配置的时隙表，从13、15、17、19时隙中提取代码块，恢复Client3的代码块流。然后，将码块按右侧的m × 20码块序列插入时隙1、3、4、7，实现SPN信道交换。最后，通过PHY芯片将代码块转发到下一个节点。

​	基于se - xcx的转发技术在时隙层实现业务转发。与L1转发技术类似，SE-XC保证了最小延迟小于1µs，抖动非常小，适合承载URLLC业务。SE-XC能够满足金融高频交易、自动驾驶等服务对延迟和抖动的严格要求

##### 3.4 弹性可靠连接技术

###### 3.4.1 弹性连接需求

​	5G移动回程采用平面IP架构。由于超密集网的工作要求，基站之间东西向的流量激增。此外，在下游部署MEC需要在不同MEC之间建立东西通道。5G网络连接数比4G网络连接数增加了数十倍es。传统的静态MPLS-TP隧道无法满足业务调度灵活、连接普遍的需求。因此，需要一种新的隧道连接技术。

###### 3.4.2 弹性连接技术

​	SR (segment routing)是一种源路由技术，简化了现有的MPLS技术，重用了现有的MPLS转发机制，与当前的MPLS网络兼容，支持从现有MPLS网络平滑演进到SDN网络。SRTP是在SR隧道技术的基础上，增强传输域运维能力的一种新型隧道技术。分为SRTP-TE隧道或SRTP-BE隧道。

* SRTP-TE隧道

  * 传统的SR-TE隧道不需要维护中转节点上的路径状态，提高了隧道路径调整的灵活性和网络可编程性。但是SR-TE只在源节点上维护路径信息，无法实现双向关联。在这种情况下，SR-TE缺乏传统的传输驱动的端到端OAM检测能力和双向隧道。但是SR-TE只在源节点上维护路径信息，无法实现双向关联。在这种情况下，SR-TE缺乏传统的传输驱动的端到端OAM检测能力和双向隧道

  * SRTP-TE隧道通过承载一个路径段来唯一标识一个端到端连接，并通过路径段关联实现双向隧道。SRTP-TE隧道作为SR-TE隧道的子集，既具有SR-TE隧道的灵活性，又具有传统隧道的传输能力。SRTP-TE是现有传输网络中MPLS-TP隧道的理想演化解决方案。

    ![027](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922ye7DYnnKNJOxNs5CATVzLFdgQDZve5P3XHLP4PxB7ZD7mM5GkCNqqA/0?wx_fmt=png)

* SRTP-BE隧道

  * SRTP-BE隧道是IGP泛洪后自动生成的隧道。IGP域中可以生成全mesh隧道连接。SRTP-BE隧道适用于无连接Mesh服务，简化了隧道规划和部署。

##### 3.5 超高精度同步技术

###### 3.5.1 高精度时间同步要求

​	5G将传统BBU划分为CU和DU两个逻辑实体。CU设备处理非实时无线上层协议栈，DU设备处理物理cal层功能和实时需求。CU和DU对时间同步的要求不一样。CU设备不需要精确时间同步，而DU-AAU级有以下同步要求:

* 5G TDD基础服务同步
  * 对于TDD基站，上行和下行信号利用相同的频率。为了防止基站间的信号干扰，必须满足基站间严格的相位同步，以保证所有基站上下行切换时间一致。在5G新无线电(NR)中引入超短帧，加快了上行-下行切换频率，这要求时间同步精度达到微秒级。
* 基站间协调技术的同步
  * 协调技术是一个增强的属性5 g,它有严格的要求的时间错误,如基站协调(CoMP)和基站载波聚合(CA)需要的时间精度±130 ns,而MIMO技术的发展提出了同步交流副牧师的职务要求±65 ns。
* 新的5G服务的同步
  * 对于基于tdoa的基站定位业务，定位精度与基站间的时间误差线性相关。1-ns的时间误差对应约0.3 ~ 0.4 m的定位精度，3-m的位置精度对应约10 ns的定位精度。
  * 考虑到5G业务发展需求，5G的同步精度要求高于4G TD-LTE技术的±1500ns

###### 3.5.2 SPN时间同步网络模型

​	4G网络时间分配链上的时间误差分配如下：

![027](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq9221ia2oatY5KicPWibU8RukJbIl9OzrgacVTiaRVEn4uoyicEVvTs1poVIz1A/0?wx_fmt=png)

​	对于5G SPN，考虑同步需求和未来技术发展，以及难度和成本之间的平衡，建议时间误差分配如下表所示。

![028](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKyGDsCIWdevfpuTBoNqq922cVmNlBNcfaooNjcXkCc5jXVkeGbxzbxjcdOXybuPlkt4LvNSoK8Xibw/0?wx_fmt=png)

200ns的运输网预算可以按如下方式分配:

(1)100纳秒为正常时间同步。每个节点分配±5ns。一个时间链支持从A到的20跳的塔尔。

(2) 100ns用于故障期间的时间延迟。

5G前中程和回程网络需要超高精度时间同步。

###### 3.5.3 SPN的超高精度同步技术

超高精度时间同步需要新的时间源和时间传输任务技术。为了支持新的网络体系结构，需要新的同步接口syn和维护管理技术

* 超高精度时间参考源

  * 高精度时标源需要达到±50 ns以上的同步精度。以下技术可用于提高精度:

    * 新的卫星接收技术
      * 减小GNSS接收时间误差的可能解决方案包括采用GNSS共视法或双频接收技术
    * 高稳定性频率源技术
      * 在没有GNSS的情况下，引入时钟组(如铷原子钟组)而不是单个时钟，提高了稳定性和延迟性能。

  * 时间传输技术

    * 在时间传输过程中，时间误差的来源如下:
      * 时间戳粒度
      * 表面频率误差
      * 层质层不对称
      * 节点内的传输延迟
      * 环联不对称
    * 为了实现超高精度时间同步，可以采用以下技术:
      * 高精度时间戳技术使时间戳gran的精度优于±1 ns。
      * 时钟模块，具有高稳定性和同步精度，pro提供更精确稳定的频
      * PHY层精确延迟测量和补偿。
      * 系统的精确延迟设计和测量。
      * 非对称链接的精确延迟测量和控制。采用单光纤双向传输可以解决这个问题lem。
      * 增强的同步测试协议和算法，具有更高的精确度。

  * 同步接口技术

    * SPN应支持专用的同步接口和新颖的波段接口，具有超高的精度。

      * 专用同步接口，超高精度

        * 专用的同步接口用于时间源输入、时间输出和测量。可以考虑以下接口:

          (a) 1GE光同步接口:用于接入超高精度

          时间源，并将时间信息传递给下游设备。

          (b) 1PPS同步输出接口:用于输出的1PPS信号

          本地节点，便于测量使用同步测试设备

      * 新颖的带内同步接口

        * 由于SPN使用了新的流量接口，因此必须支持新的流量接口

        ​       同步接口技术:

        ​       a) FlexE接口同步:支持超高精度时间同步和联动。

        ​       b) eCPRI接口同步:采用以太网接口同步

        ​       实现eCPRI接口的同步和互通。

  * 智能时钟技术

    * 控制层的智能时钟技术，为超高精度同步网络的部署和运维提供支撑。其核心功能如下:
      * 同步网络自动规划
      * 动态同步状态图形化显示
      * 同步运行状态检测与分析
      * 智能同步故障诊断
      * 同步性能实时监控与分析

> 2021/11/1🤤

##### 3.6 SDN集中管理与控制技术

###### 3.6.1 要求

​	SDN是5G的关键技术之一。可概括为集中式网络工作控制、转发与控制分离、开放网络与程序的可移植性。SDN采用三层架构(业务应用层、网络控制层、转发层)，提高5G网络资源利用率，加速业务部署，实现灵活的业务调度和开放的网络可编程性。

​	SDN控制器作为一种智能网络操作系统，其发展趋势如下:

* E2E管理能力

  通过SDN技术，SPN可以实现从单个网元、网络、业务的垂直管理向覆盖网络、业务部署、评估反馈的闭环管理的转变。操作系统从业务创建、网络监控、故障分析、性能评估到网络故障切换和恢复，形成了一个闭环过程。大大提高了业务部署效率和网络的健壮性。

* 自动化OAM和分析

  为了应对动态变化的服务，智能操作系统必须向自动化管理转变，以实现对网络功能、应用和服务的灵活管理。该功能基于5G网络的大数据分析，自动生成OAM policies。

* 开放操作能力

  智能运营的另一个重要原则是突破不同厂商的技术壁垒，构建统一管控的开放平台。免费和开源的软件架构和向第三方开放的运营商网络能力可以加速新技术的成熟和商业应用，并促进创新。

###### 3.6.2 智能运营关键技术

为了适应SPN架构的变化，新的智能运营系统需要在业务架构、service模型和策略、运营流程、可靠性和安全保障等关键技术上做出调整。下图展示了智能运营的发展趋势与关键技术的对应关系。

![030](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CjbgJo0pU8qB6QR6lpef1LP95p3FvaQVIMSvMTLXxOPqHfCju3tUsRA/0?wx_fmt=png)

* 低耦合服务架构

  低耦合服务体系结构的核心思想是将各个独立的功能模块原子化成独立的原子服务，使pro通过聚合和组合提供不同的服务场景。原子函数根据基本的服务功能分为不同的类型。协调器或控制器根据不同服务场景的需要安排和组合子功能。

* 自动化的管理过程

  利用SDN网工作架构的优势，实现设计、创建、部署、管理和新业务迭代的标准化和自动化，可大大提高网络运行效率。自动化管理包括自动化的业务配置、自动化的环境感知、自动化的软硬件安装和升级、自动化的故障检测、自动化的业务切换和恢复、智能的业务数据分析等

* E2E策略管理

  可以开发策略管理模块来为自动化编排制定、解析、执行和优化策略规则。协调器可以根据设计需求将er策略交付给统一策略库。一旦net工作事件发生，控制器或协调器可以自动执行之前定义的操作，以减少手动干预并提高响应速度。

* 统一开放模型设计

  一套标准的数据模型是端到端服务管理、数据分析和开放操作平台的前提。基于标准规范cations，制定统一拓扑上报、告警与性能上报、业务配置的模型和流程，可显著减少端到端服务器vice差异。此外，数据分析系统可以根据这些固定的模型提取关键信息并进行数据分析。

* 可靠性和安全保证

  协调器和控制器作为操作系统的核心，必须保证智能操作系统的可靠性，以提供及时的端到端管理。通过将网络划分为多个子网，并将每个子网分配给多个控制集群进行管理和控制，集群部署和分布式存储解决方案提高了网络的鲁棒性，防止了单点故障或单点流量拥塞。

###### 3.6.3 智能操作系统的架构

基于spn的智能操作系统架构如下图所示。核心模块是SDN控制器，通过对网络设备的集中控制和管理，提供智能opera。

![031](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CUHjOSApbceXMmPoB6KvZIm4P5485U5N9YCcGWRu9OCMprz1QOlZR0Q/0?wx_fmt=png)

#### 4.SPN

SPN是下一代综合传输网络技术，适用于多种应用场景。基于全新的网络架构架构、多种关键技术和新的运营模式，SPN是5G时代用户在综合传输网络上承载多种业务的良好选择。

#####4.1移动服务运输

SPN实现了前程网、中程网和回程网的统一传输，避免了异构网络的互操作，不仅使整个网络的规划、供应、运维和优化更加简单，而且大大降低了网络的资本支出和运营成本。基于spn的移动传输网络架构如图4-1所示。

![032](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CDHM9ICEYj1MV7ibOcBXia3qoz6HibzrJJ2dJt4H2QCGwHc2D0ofEo9lOQ/0?wx_fmt=png)

###### 4.1.1 前拖网络

在集中式部署的CRAN场景下，aau与du或du /CUs的组网称为前置网络。fronthaul网络要求高带宽(每个AAU大于20 Gbit/s)和低延迟(小于100µs)。

SPN为fronthaul网络提供了以下特性:

* 基于SCL的SPN信道交叉连接技术的超低时延业务转发，满足了5G网络对低时延的需求。
* 完整的OAM和保护机制，使前端网络实时监控，快速故障定位和快速保护切换ver
* 采用低成本的100GE短距离硅光模块，降低网络建设成本
* 业界领先的CPRI通过FlexE技术提供了与传统3G/4G前端接口的兼容性。

###### 4.1.2 Midhaul /回程网络

5G基站可以以不同的方式部署。例如，du和CUs可以一起部署，也可以单独部署。当du和CUs合一部署时，需要由传输网络向核心网络传输业务。当du和CUs分开部署时，由中间网完成du和CUs之间的业务传输，由回程网传送用户vice到核心网。两种部署方法可能同时存在。尽管部署方式不同，但5G基站对传输网络提出了统一的要求，包括:高带宽、低延迟(低于4G网络)、更强的L3路由能力、L3到边、南向、北向、东西向业务的灵活部署和调度。基于spn的城域网由接入层、聚合层和核心层三层组成，实现中程和回程业务的统一传输。

##### 4.2 数据中心互联

未来，数据中心将更广泛地用于承载各种NFV云软件和IT应用，在运营商业务部署中起到举足轻重的作用。同时，不同数据中心之间的业务互操作也将变得更加复杂。基于spn的综合传输网络可以实现数据中心之间的互联。

###### 4.2.1 通过SPN实现边缘数据中心多点互联

当城域网中存在多个数据中心时，需要采用网状组网。SPN可以提供独立的网络切片来承载DCI业务。各数据中心gw之间通过SPN L3 VPN互联，满足数据中心间流量调度的需求ments。

![033](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211101204145677.png)

###### 4.2.2 数据中心通过SPN实现点对点互联

对于城域数据中心之间的点对点互联，SPN可以通过CBR映射和高带宽硬管道提供L1透明传输，保证数据中心业务的低时延。

![034](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6Cwvx26Jibjzu14S12jpmicY6VCxKE6YwAU8AyT56VXHkLWgOwGoVDcpng/0?wx_fmt=png)

##### 4.3 视频无处不在

随着互联网的日益普及，视频业务为电信运营商提供了重要的战略转型机遇。在大视频时代，4K、8K、VR/AR等超高清视频对网络带宽、时延、抖动提出了更高的要求。电信公司可以利用网络广告的优势，提供比互联网OTT公司更高质量和更好的用户体验的视频服务。

为了传输在光线路终端(OLTs)上运行的HSI和IPTV等家庭宽带业务，SPN利用SPN通道将网络划分为多个独立的片。不同片上的服务彼此隔离。根据实时流量状态，SPN可以部署QoS，为不同的业务设置优先关系。这样，视频服务就可以得到相应的precedence。为了防止网络拥塞导致IPTV业务性能下降，可以采用独立的网络分片技术来承载IPTV业务。这样，这些IPTV业务与普通的inter网络接入业务严格隔离，用户可以享受到与其他OTT业务截然不同的高质量服务。服务质量测量(SQM)技术nology用于监控视频流量性能，包括时延、带宽宽度和抖动。通过定期的统计和分析，可以对网络进行相应的变更，保证服务质量。

![035](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CPkQKD7Z7WibdaOq5U90icMKiawfEbicT8Pia15KV166xGia1HhhTgNyXs2WQ/0?wx_fmt=png)

随着大型视频业务的发展，OLT的上行带宽已经从NxGE扩展到Nx10GE。SPN可以通过FlexE和DWDM技术进行扩展，为家庭宽带服务提供带宽保障。

##### 4.4 云专线

SPN是SDN技术与分组传输网工作的完美结合，能够实现快速的网络重构，为专用线路提供合适的网络切片，并提供云连接。SPN还可以通过连接数据中心或部署本地计算节点来满足云应用需求。

###### 4.4.1 云连接

**SPN可以根据端口、波长、FlexE通道等资源划分为网络片。一个物理网络可以划分为多个虚拟网络，每个虚拟网络各自承载各自的专用业务，互不影响。**

SPN还可以根据企业用户的实际需求提供差异化的L2/L3业务。这样可以实现企业分支机构和总部之间灵活的业务调度，为客户提供L2VPN/L3VPN功能

![036](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6COdF0I1ZOwRbpqWwvxvlpBEdXCfwA8YJsXJ9tsxQ5lxF5JSKJqGhCgA/0?wx_fmt=png)

对于隔离要求较高的专线业务，如金融、证券行业的以太网和SDH业务，可通过基于SPN channel的硬管道独立承载。SPN设备的客户端使用CBR映射将物理层的位流直接转发到SPN chan隧道，网络服务的中间节点使用SE-XC进行业务调度。此实现满足硬服务隔离、安全性和超低延迟的要求。

![037](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CJewL9ZNV8CGZr5DIbbojhic88lIYBuvnQ2l4nNpqvficNNgia6CkpUfibw/0?wx_fmt=png)

###### 4.4.2 云应用

在涉及云和网络协同的专线业务场景中，美国er-side CPE支持用户访问和企业虚拟化服务。或接入企业分支机构提供虚拟专用线连接。用户业务通过SPN接入DC用户网关vMSR，实现对Internet和站点的访问。分支交换机通过L2 vlan连接SPN, SPN将业务传输给数据中心叶子节点的TOR交换机。分布式交换机将L2流量导向vMSR

![038](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKxsG6kkPsicSnZLWR3ic3dc6CwKtJic5fFfehCvRQDTibvqII3VpoGFwmkJicmSXmGiazx1zGicl9C0oibbgQ/0?wx_fmt=png)

> SPN白皮书后面有专业简称名词介绍

### 江苏电力信通交流（🚀看源文件

#### 交流议题

\1. FlexE设备形态，和现有路由器设备以及PTN设备之间的演进关系，SHIM层的 

部署位置，当前三种模式（unaware, aware, terminated）的实现现状如何？ 

\2. FlexE网络切片的配置和管理流程？ 

\3. Canlendar时隙是否支持按需即时分配和调整？分配和调整的过程？通常的 

调整间隔是多长时间？calendar时隙分配方案的无缝切换如何保证？ 

\4. 当前设备支持的小颗粒带宽粒度有哪些？FlexE设备是否有时隙数目上限？ 

\5. FlexE设备管控接口和设备网管的北向接口分别支持哪些功能，分别基于什 

么协议？ 

\6. FlexE的硬切片和SR的软切片是如何配合实现的？ 

\7. 当前FlexE设备MPU在处理切片间数据、切片层与管理层数据间的隔离性如 

何？

#### FlexE:增加Shim层将业务逻辑层和物理层隔开

#### FlexE基本概念：FlexE Shim / FlexE时隙

* **Shim**：一个功能层，实现FlexE-Client和FlexE-Group之间的映射/解映射。

  ​			时分复用，绑定多个物理通道。

* **FlexE时隙**：shim中有n * 20个时隙（n是成员的数量，每个成员有20个时隙），一个时隙就是5G的速率，（传输数据的基本单位是66比特的数据块）

#### FlexE基本概念：Calendar结构

![039](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211102221702594.png)

#### FlexE管理通道实现：Shim-to-Shim层|Section层

**Section**层的管理通道信息在相 

邻的段层处终结，只在相邻的物 

理管道之间有效，不会穿透第三 

方网络 

---

**Shim-to-Shim**的管理通道信息*

在相邻的两个shim之间有效。在 

特定场景下，Shim-to-Shim管理 

通道信息会穿透第三方网络

![040](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKz1Z2TmkTDGtyicHQh17RZbQFEcKUBWPicbvaJCpUkd9mQp2wWJWiaN6icaTzP6u6sbEvWK1JICw98oJw/0?wx_fmt=png)

> 2021/11/3🐱‍🏍

#### FlexE帧结构：开销结构

复帧结构：32 * 8 = 256

开销位置：每间隔1023×20个66比特块有一个开销块，连续8个开销块组成一个FlexE帧， 

连续32个FlexE帧组成一个FlexE复帧 

![041](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzoVDXVoAZoiaXNZ07dO4OWa0nj8vzZK6hkxzGUqXt2SMBZWibehlIsnvfETErFISsiaT8MOibC4IG8UQ/0?wx_fmt=png)

![042](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211103212547167.png)

![043](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzoVDXVoAZoiaXNZ07dO4OWa9sz9aXeuKfSqgd1cxibpjoL2Pa5FZDBjXnVNGQSYNZDO7ZlMl1FAibyQ/0?wx_fmt=png)

![044](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzoVDXVoAZoiaXNZ07dO4OWa8cAWosrBz2Lq72pCiajRoT1gVwfiboGytDdPicWrXm6wqsCXFJddtchicw/0?wx_fmt=png)

####Calendar静态配置

Calendar静态配置是指互通的两个MTN接口组对应接收端MTN Client的业务恢复配置选择，完全根据本端控制器下发的配置信息进行MTN Client 提取，而不是根据接收端从MTN接口开销Client Calendar A/B字段中提取的配置信息，Client互通配置的正确性通过控制器保证，Client Calendar提取的配置信息可用于一致性校验。

#### Calendar动态协商

![045](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKzoVDXVoAZoiaXNZ07dO4OWawoKzicBa4dCj5fusCVj7DKqy9uRP7Beh6cbAZq0JppNticgU3ia5L8B4A/0?wx_fmt=png)

#### 组网场景-Unaware

#### 组网场景-Terminating

#### 组网场景-Aware

---

#### SPN设备的端口模式

#### FlexE Channel/G.MTN Channel

> 2021/11/17🤢

`MTN`: 外国针对FlexE的描述

