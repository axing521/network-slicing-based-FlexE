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

### 中国移动SPN技术白皮书





