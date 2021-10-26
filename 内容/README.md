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
* background
  * 大家都用光纤了，底层接口/模块/速率固定，调整成本高，所以FlexE来灵活配置带宽。
* advantage
  * 接口速率可变（不再受制于阶梯型速率体系
  * 接口带宽可以按需灵活满足，不受制于光传输网络能力
* how make it？
  * ![image-20211026221640465](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKwXDVRARjGI44uwiaFygGNkMxhwMCjs103m79h4p4ibCnpfwwMKwahSKYQh0SZS6Kiax3ew0ROAicQk0g/0?wx_fmt=png)
  * ![image-20211026221721093](https://mmbiz.qpic.cn/mmbiz_png/rxKvItX9KKwXDVRARjGI44uwiaFygGNkMLxnLmDn4QjkwfGc2QvibgEDslLib8ibuJTfsI2N25hiaA81RhavRKCNsZQ/0?wx_fmt=png)
  * 

