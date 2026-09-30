<div class="hero">
  <span class="hero-eyebrow">主线一 · AI 存储 / 存算协同</span>
  <h1>技术点 · AI 存储</h1>
  <p class="hero-sub">3-5 年差异化出口：KV Cache、GDS、RDMA、NVMe-oF、CXL、Checkpoint、数据管线与行业全景。</p>
</div>

> 每个技术点固定五字段：是什么 / 为什么学 / 怎么学 / 可验证产出 / 对应岗位要求。产出全部可跑、能量化、能写进 GitHub。

### A1.1 AI 工作负载的存储特征（训练 vs 推理）

| 字段 | 内容 |
|---|---|
| 是什么 | 训练是大带宽顺序读 checkpoint、写模型；推理是高并发小 IO、反复读权重、反复读写 KV Cache。三大存储对象：Checkpoint、KV Cache、训练数据加载。 |
| 为什么学 | 这是"懂负载"的第一课。字节 AI 存储岗明确把 KV Cache/Checkpoint/RL 列为工作对象，且"纯 AI 侧或纯存储侧均可"[8]——你补的就是这层共同语言。 |
| 怎么学 | 先读 vLLM PagedAttention 论文建立 KV Cache 直觉[9]；再读 DeepSeek 3FS 的 Design Notes 看一个面向 AI 负载的 FS 怎么设计[29]。 |
| 可验证产出 | 写一篇 1500 字笔记《训练 vs 推理的 IO 模式对比表》，列出带宽/IOPS/延迟目标三大行，每个数字标注来源；自己用 fio 各跑一次顺序读与随机读并贴图。 |
| 对应岗位要求 | "负责 AI 存储加速系统（KV Cache/Checkpoint/RL）的设计与开发，纯 AI 侧……和纯存储侧……均可"[8]。 |

### A1.2 KV Cache 原理与存储特性

| 字段 | 内容 |
|---|---|
| 是什么 | 自回归推理把已算过的 Attention Key/Value 缓存在显存里避免重算；PagedAttention 把 KV Cache 切成固定块非连续管理，消除显存碎片[9]。为什么要外置：显存容量与 TTFT 相互矛盾，KV Cache 越大能跑的并发越长，但显存装不下。 |
| 为什么学 | 这是 AI 存储岗最核心的特化对象。DeepSeek 公开招"支撑推理的 KVCache 存储系统（毫秒级延迟、上亿 IOPS）"[12]；腾讯大模型专用存储做训推一体分层存储[15]。你读研的显存/算子经验在这里直接可用。 |
| 怎么学 | PagedAttention 论文[9] → vLLM 官方 Prefix Caching 文档看公共前缀复用[10] → Paged Attention 设计文档[11] → 跑通 vLLM 后读它的 block manager。 |
| 可验证产出 | 本地用 vLLM 跑一个 7B 模型，量化出"每千 token KV Cache 占多少 GB 显存"，写出公式（层数×头数×维度×2×精度），并实测对照；笔记≥1 篇。 |
| 对应岗位要求 | "设计支撑大模型推理的 KVCache 存储系统（毫秒级延迟、上亿级 IOPS）"[12]；"熟悉 Attention/KV Cache/TP-PP-DP 并行/量化/投机采样"[67]。 |

### A1.3 GPU Direct Storage（GDS）

| 字段 | 内容 |
|---|---|
| 是什么 | 让 GPU 经 NVMe SSD/网络存储直接读写，绕过 CPU 主存一次拷贝；与 GPUDirect RDMA 的区别：GDS 省的是"存储↔GPU"的 CPU 拷贝，GDR 省的是"网卡↔GPU"的主机拷贝。 |
| 为什么学 | Radian Arc 的 AI 存储岗 JD 直接点名要调优 GPU Direct Storage / RDMA / NVMe-oF[13]；这是把"AI 侧"和"存储侧"粘起来的关键桥梁。 |
| 怎么学 | 从 NVIDIA GPUDirect Storage 官方文档中心（cuFile 编程模型）入手[14]；无 GPU 环境先读文档与架构图，不硬跑 demo。 |
| 可验证产出 | 画一张"传统读路径 vs GDS 读路径"对比图，标清几次内存拷贝、几次系统调用；写一篇 cuFile API 列表笔记（≥10 个 API）。 |
| 对应岗位要求 | "调优 RDMA / NVMe-oF / GPU Direct Storage"[13]。 |

### A1.4 RDMA（IB / RoCE）

| 字段 | 内容 |
|---|---|
| 是什么 | 内核旁路、零拷贝、QP/MR/verbs；InfiniBand 与 RoCE（以太网上跑 IB 协议）的区别；用户态库 libibverbs。 |
| 为什么学 | 腾讯大模型专用存储岗点名"CXL/RDMA 优化"[15]；AI 存储要把 KV Cache/数据在 GPU 节点间搬来搬去，TCP 延迟是瓶颈。 |
| 怎么学 | rdma-core 官方仓库入门示例与 man 页[16] → 内核侧 InfiniBand/RDMA API 文档理解 verbs 在核内对应物[17]。无硬件先读代码与示例。 |
| 可验证产出 | 写一篇笔记讲清 QP/MR/CQ 三个对象与一次 verbs Send/Recv 的数据流；能白板画出 RoCE vs IB 的协议栈分层。 |
| 对应岗位要求 | "结合 CXL、RDMA 做性能优化"[15]；海外 JD 点名 RDMA 调优[13]。 |

### A1.5 NVMe-oF

| 字段 | 内容 |
|---|---|
| 是什么 | 把本地 NVMe 块设备通过网络（TCP/RDMA/FC）共享出去，SSD 从"服务器内部件"变成"网络存储资源"。 |
| 为什么学 | 阿里云 DPU 方向岗做"IO 协议栈开发与卸载"[18]，字节高级存储专家点名 SPDK/VirtIO/NVMe[19]；NVMe-oF 是全闪分布式存储的物理底座。 |
| 怎么学 | 读 NVM Express 官方规范库中的 NVMe over Fabrics 现行规范[20]；先建立"Transport 层"概念，不抠寄存器级细节。 |
| 可验证产出 | 笔记一张表：NVMe over TCP / RDMA / FC 三者在延迟、部署成本、生态成熟度上的对比（≥3 行×4 列）。 |
| 对应岗位要求 | "精通块存储原理……iSCSI/NVMe-oF"[48]；"了解 SCSI 协议、了解 SPDK、NVMe 等"[18]。 |

### A1.6 CXL 内存池化

| 字段 | 内容 |
|---|---|
| 是什么 | CXL 2.0/3.0 提供一致性内存互连，把 DRAM 池化、在主机间共享内存；与 KV Cache 外置的关系：用 CXL 内存当"显存延伸"装 KV Cache，绕开 NIC 一跳到 GPU。 |
| 为什么学 | 这是当前最前沿、也最贴你"存储×AI"交叉点的方向。腾讯大模型存储岗点名 CXL[15]；学术侧 TraCT（arXiv 2512.18194）用 CXL 共享内存做机架级 KV Cache[23]，Beluga（arXiv 2511.20172，SIGMOD'26）用 CXL Switch 管理 KVCache、TTFT 降 89.6%[24]，CXL-SpecKV（arXiv 2512.11920）用 FPGA 做分离式 KV Cache[25]。Astera Labs 白皮书称 KV Cache 卸载到 CXL 后同 TTFT 下 batch size 可提升 30%[26]。 |
| 怎么学 | CXL 联盟官网先建整体图景[21] → 官方资源库白皮书[22] → 三篇论文选 TraCT 精读[23]，其余两篇摘要读[24][25]。 |
| 可验证产出 | 读 TraCT 论文后写一页"它把 KV Cache 放在哪、网络跳数省了几次、TTFT 收益多少"的精读笔记；能讲清 CXL 内存池与本地 SSD 当 KV 后端的延迟量级差异。 |
| 对应岗位要求 | "训推一体化分层存储架构……结合 CXL、RDMA 做性能优化"[15]。 |

### A1.7 Checkpoint 存储

| 字段 | 内容 |
|---|---|
| 是什么 | 训练断点的周期性持久化；写放大、异步 checkpoint、断点续训；训练中途挂掉要从最近 checkpoint 恢复。 |
| 为什么学 | 字节 AI 存储岗把 Checkpoint 列为工作对象之一[8]；NVIDIA DGXC 岗明确做 checkpointing、数据加载、caching[27]。 |
| 怎么学 | 结合 A1.1 的训练 IO 特征，读 NVIDIA DGXC JD 描述反推存储侧要做什么[27]；不碰内部训练框架。 |
| 可验证产出 | 写一篇笔记《一次 checkpoint 写请求的写放大来源》（同步刷盘 vs 异步、临时文件、元数据），≥800 字。 |
| 对应岗位要求 | "数据加载、checkpointing、caching、POSIX/对象存储集成"[27]。 |

### A1.8 训练数据管线（小文件高并发加载）

| 字段 | 内容 |
|---|---|
| 是什么 | 训练集海量小文件（图片/token shard）的高并发读取、缓存预取、数据集版本化；GPU 等数据会空转。 |
| 为什么学 | 3FS 就是为"本地 SSD + 网络"架构解决 AI 训练/推理数据加载而生的开源样本[28][29]；这是 3FS/WEKA/VAST 公开解法对应的岗位内容。 |
| 怎么学 | 读 DeepSeek 3FS 仓库与 Design Notes[28][29]；对照 WEKA 白皮书讲用户态绕过内核、SPDK/DPDK、NVMe 优化与 KV Cache 卸载[31]。 |
| 可验证产出 | 画一张 3FS 的 cluster manager/metadata/storage/client 四组件关系图；写笔记说明它为什么用本地 SSD 做缓存层。 |
| 对应岗位要求 | 字节"纯存储侧（对 KV Cache 等训推加速技术有基本了解）"[8]；3FS 即此类岗位的公开开源对照[28]。 |

### A1.9 AI 存储架构案例（行业全景）

| 字段 | 内容 |
|---|---|
| 是什么 | NVIDIA DGXC、VAST DASE、WEKA、华为 OceanStor A800（AI 分布式文件存储，公开页称单框千万 IOPS、训练集加载效率业界数倍）与 M900（面向推理的 PB 级 KV Cache 池化，公开新闻稿）[32][33]、DeepSeek 3FS[28]。 |
| 为什么学 | 建立行业全景，面试谈资与方向验证；OceanStor A800/M900 仅看公开产品页与新闻稿，不涉及内部实现。 |
| 怎么学 | VAST 白皮书[30] → WEKA 白皮书[31] → 华为 OceanStor A800 公开产品页[32] 与 M900 公开新闻稿[33]。 |
| 可验证产出 | 做一张"主流 AI 存储架构对比表"（厂商/架构特点/主打场景/是否公开开源），≥5 行；能口述 3 家厂商的差异化定位。 |
| 对应岗位要求 | "为 AI 负载设计高性能可扩展存储系统与客户端库"[27]；AI 存储行业格局公开梳理[30][31]。 |

---
**本页参考来源**（编号与全量 83 条来源一致）：
8. https://www.liepin.com/job/1984783145.shtml
9. https://arxiv.org/abs/2309.06180
10. https://docs.vllm.ai/en/stable/features/automatic_prefix_caching.html
11. https://docs.vllm.ai/en/stable/design/paged_attention/
12. https://www.51shuobo.com/ids_other/job_277559544
13. https://www.builtinsf.com/job/staff-storage-platform-engineer-ai-storage-radian-arc/10331801
14. https://docs.nvidia.com/gpudirect-storage/index.html
15. https://hr.tencent.com/m/jobdesc.html?postId=2035012525405401088
16. https://github.com/linux-rdma/rdma-core
17. https://docs.kernel.org/driver-api/infiniband.html
18. https://m.yupao.com/zhaogong/338643829.html
19. https://jobs.bytedance.com/experienced/m/position/detail/7036966671353137416
20. https://nvmexpress.org/SPECIFICATIONS/
21. https://computeexpresslink.org/
22. https://computeexpresslink.org/resource-library/
23. https://arxiv.org/abs/2512.18194
24. https://arxiv.org/abs/2511.20172
25. https://arxiv.org/abs/2512.11920
26. https://www.asteralabs.com/resources/blog/breaking-through-the-memory-wall-how-cxl-transforms-rag-and-kv-cache-performance/
27. https://www.builtinchicago.org/job/senior-storage-software-engineer-dgxc-data-services/10071443
28. https://github.com/deepseek-ai/3FS
29. https://github.com/deepseek-ai/3FS/blob/main/docs/design_notes.md
30. https://www.vastdata.com/whitepaper
31. https://www.weka.io/wp-content/uploads/files/resources/2024/05/the-ai-native-weka-data-platform-powers-ai-workloads.pdf
32. https://e.huawei.com/cn/products/storage/ai-storage/oceanstor-a800
33. https://www.huawei.com/cn/news/2026/9/hc-context-memory-storage
48. https://m.liepin.com/job/1985044587.shtml
67. https://hr.tencent.com/m/jobdesc.html?postId=2037101976877170688

相关页面：[[学习路线]] · [[技术点-分布式存储]] · [[技术点-RPC体系]] · [[开源项目地图]]
