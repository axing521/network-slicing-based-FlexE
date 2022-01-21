输出成果：

1）网络切片编排的数学模型；

2）最小化网络切片资源分配的启发式算法的设计与实现；

3）仿真环境的设计与搭建；

4）基于评价指标的算法仿真结果和性能分析。

---

本课题旨在基于**FlexE网络切片开通流程、技术特点**，并结合**典型租户业务需求**，对基于FlexE的传输网络**切片编排问题进行分析和建模**，在此基础上**设计相应的启发式算法求解切片编排问题**，形成智能化的资源分配方案，最后**设计和搭建实验环境，对所提出的算法进行验证和评估**。

---

网络切片的要求：

1. 分片严格隔离

2. 可平滑扩容调整

---

![image-20211205173413596](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211205173413596.png)

![image-20211205173554011](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211205173554011.png)

![image-20211205173756789](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211205173756789.png)

![image-20211205174018061](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211205174018061.png)

![image-20211205174649674](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211205174649674.png)

SPN管控5.流量管理看看，以后写论文可能要用

![image-20211223142801865](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20211223142801865.png)

==日历和数据一起通信==

网络切片资源分配是调度资源以找到最佳关联的技术，该关联将提高可用资源的利用率并满足用户的所有要求，其中系统中的各种资源可以根据时变的系统条件，用户数量和异构性进行协调，优先级，以及每个编排分配任务的最大可容忍延迟。

FlexE可以基于时隙调度将一个物理[以太网](https://www.zhihu.com/search?q=以太网&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A243311659})端口划分为多个以太网弹性硬管道，实现同一分片内业务统计复用，分片之间业务互不影响。同时，通过网络云化引擎支持对每个分片的灵活创建、带宽按需调整、SLA（服务等级承诺）按需保证和故障快速定位，一网多用，最大化回传网络价值。

![image-20220107175332228](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20220107175332228.png)

MDSC = FlexE控制器，提供给网管系统北向接口

![image-20220119134824574](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20220119134824574.png)

![image-20220119135838213](C:\Users\28274\AppData\Roaming\Typora\typora-user-images\image-20220119135838213.png)

