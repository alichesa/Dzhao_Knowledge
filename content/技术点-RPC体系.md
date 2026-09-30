<div class="hero">
  <span class="hero-eyebrow">主线三 · 配套基础补课</span>
  <h1>技术点 · RPC 体系</h1>
  <p class="hero-sub">你自认最大的短板，最先补：RPC 全景、Thrift 深入、gRPC、Protobuf、HCS 生态、网络、OS 与 C++ 现代特性。</p>
</div>

> 工作中天天用 Thrift，却说不清 RPC 体系——先补这块，成本最低、见效最快。每个技术点固定五字段。

### C1.1 RPC 全景

| 字段 | 内容 |
|---|---|
| 是什么 | IDL → 代码生成 → 序列化 → 传输 → 服务发现 → 负载均衡 → 超时重试 的完整链路。 |
| 为什么学 | 你用 Thrift 写接口但停在"调用方"视角；补完全景后，再看 CSI 的 gRPC、HCS OpenAPI 就不再是黑盒。gRPC 是云原生/CSI 生态入口[57]。 |
| 怎么学 | gRPC 官方文档先建现代 RPC 全景[57]，再回头看 Thrift 教程对照[58]。 |
| 可验证产出 | 画一张 RPC 调用分层图（IDL/序列化/传输/服务发现），标出你现职每个环节用的是什么。 |
| 对应岗位要求 | K8s/CSI 岗要求熟悉容器存储接口，CSI 本身即 gRPC 服务[60]。 |

### C1.2 Thrift 深入

| 字段 | 内容 |
|---|---|
| 是什么 | Thrift IDL 语法、TBinary/TCompact 协议、TTransport/TProcessor 模型、多语言绑定。 |
| 为什么学 | 直接关联你当前工作（公开层面）：你写 Thrift 接口，但没读过它的协议层。 |
| 怎么学 | Apache Thrift 官方 Tutorial[58] → 阿里云开发者社区《Thrift 与 gRPC 深度对比》[59]。 |
| 可验证产出 | 用 Thrift 写一个 echo 服务（见学习路线 Phase 0），抓包看一次 TBinary 协议体长什么样。 |
| 对应岗位要求 | 你现职 Agent 插件后端即 Thrift 通信（个人履历，非外部 JD）；备份研发岗要求 Linux C++ 服务端与网络传输[50]。 |

### C1.3 gRPC 深入

| 字段 | 内容 |
|---|---|
| 是什么 | HTTP/2 传输、Protobuf 序列化、流式 RPC、拦截器、服务端/客户端流。 |
| 为什么学 | 字节 K8s/CSI 存储岗要求熟悉 PV/CSI[60]，CSI 的存储面就是 gRPC；学会 gRPC 等于拿到云原生存储的入场券。 |
| 怎么学 | gRPC 官方 Quickstart[57] → 微软《Compare gRPC services with HTTP APIs》看序列化/延迟/流式对比[61]。 |
| 可验证产出 | 用 gRPC 写一个 echo 服务（见学习路线 Phase 0），实现一个服务端流式 RPC；与 Thrift echo 做协议体对比笔记。 |
| 对应岗位要求 | "熟悉 Kubernetes 架构和生态，熟悉 PV/CSI 等云原生容器存储技术"[60]。 |

### C1.4 Protobuf 编解码

| 字段 | 内容 |
|---|---|
| 是什么 | varint/zigzag 编码原理、字段 tag、与 Thrift 序列化的差异。 |
| 为什么学 | 这是 RPC 序列化性能认知的底层；gRPC/CSI 生态都建立在 Protobuf 上[62]。 |
| 怎么学 | Protobuf 官方文档[62]；自己手写一个 varint 编解码小函数验证。 |
| 可验证产出 | 写一个 50 行的 varint 编解码 demo 并测一组数字编码结果；笔记说明为什么 Protobuf 比 JSON 小。 |
| 对应岗位要求 | 云原生存储岗普遍要求 gRPC/Protobuf 生态能力[60]。 |

### C1.5 序列化性能对比

| 字段 | 内容 |
|---|---|
| 是什么 | JSON / Protobuf / FlatBuffers / Cap'n Proto 的选型差异与官方 benchmark。 |
| 为什么学 | 存储数据面追求低延迟，序列化省一次拷贝都是收益；这是选型常识。 |
| 怎么学 | 在 C1.4 基础上跑官方 benchmark，或自己写 micro-benchmark 对比 JSON vs Protobuf 编解码吞吐。 |
| 可验证产出 | 一份 micro-benchmark 表格（数据量×三种格式的编码/解码耗时与字节数），≥100 行代码。 |
| 对应岗位要求 | "追求极致性能"类存储/中间件岗高频要求（常识性判断，无单一 JD 逐字对应）。 |

### C1.6 HCS 生态公开资料

| 字段 | 内容 |
|---|---|
| 是什么 | 华为云 Stack（HCS）是什么：混合云基础设施与产品组合定位，与公有云的差异。 |
| 为什么学 | 你自认"不懂 HCS 应用"——只用华为云官网公开产品页补认知，不碰任何内部实现。 |
| 怎么学 | 华为云 Stack 官方产品介绍页[63]。 |
| 可验证产出 | 写一页笔记：HCS 作为混合云形态，其 OpenAPI 体系与公有云的异同（仅基于公开页）。 |
| 对应岗位要求 | 你现职用 HCS OpenAPI（个人履历）；公开产品页即学习边界[63]。 |

### C1.7 网络补课

| 字段 | 内容 |
|---|---|
| 是什么 | TCP 深挖、HTTP/2、epoll/io_uring、连接池与超时重试。 |
| 为什么学 | RPC 可靠性与存储数据面都建在网络之上；美团岗要求精通网络编程、多线程高并发[56]。 |
| 怎么学 | 结合 C1.3 的 HTTP/2、B1.10 的 io_uring；用公开教材补 TCP 状态机与 epoll 水平触发/边缘触发。 |
| 可验证产出 | 写一个带连接池与超时重试的 TCP 客户端小 demo；笔记讲清 epoll LT vs ET 的区别。 |
| 对应岗位要求 | "精通网络编程，多线程、高并发编程"[56]；"深入理解计算机体系结构、网络协议（TCP/IP）"[66]。 |

### C1.8 OS 补课

| 字段 | 内容 |
|---|---|
| 是什么 | 进程线程、锁与内存模型、文件系统与页缓存、IO 栈分层。 |
| 为什么学 | 你自评"epoll、零拷贝、锁、brk/mmap 停留在认知层"；这是数据面理解的根基，也是腾讯/商汤 JD 的硬要求[65][66]。 |
| 怎么学 | 结合 B1.6 块设备与 B1.10 fio/perf 边做边补；用 DDIA 第 7 章（事务）与操作系统教材交叉。 |
| 可验证产出 | 用 mmap 与 read 写同一文件做一次吞吐对比并记录；笔记讲清页缓存如何影响 fio 结果。 |
| 对应岗位要求 | "深入理解操作系统和网络基本原理"[65]；"深入理解计算机体系结构、操作系统原理、Linux I/O 栈"[66]。 |

### C1.9 C++ 现代特性

| 字段 | 内容 |
|---|---|
| 是什么 | C++17/20 常用特性、CMake、内存模型与 atomics。 |
| 为什么学 | 你现有 C++ 实战级但现代特性需补；快手推荐存储岗明确要求"精通 C++ 内存模型……掌握现代 C++ 特性（C++17/20）"[64]；EET-China 报告称 C++17 成生产主流、C++20 快速增长（公开行业报告口径）。 |
| 怎么学 | 用一个真实小项目（如 Phase 0/1 的 demo）强制用 C++17/20 重写一遍；学 CMake 现代 target 写法。 |
| 可验证产出 | 把 Phase 0 的 echo 服务用 C++17（structured binding/optional/variant）重写；写一篇 CMake target-based 笔记。NVIDIA 推理引擎岗同样要求 C++11/14/17/20[68]，现代 C++ 是 AI Infra 与存储底座的共同语言。 |
| 对应岗位要求 | "精通 C++ 内存模型及性能调优，掌握现代 C++ 特性（C++17/20）"[64]。 |

---
**本页参考来源**（编号与全量 83 条来源一致）：
50. https://m.zhipin.com/job_detail/ac372a785d96f1b00nR80ti7GVZX.html
56. https://zhaopin.meituan.com/m/position/detail?highlightType=social&jobUnionId=4135206862
57. https://grpc.io/docs/
58. https://thrift.apache.org/tutorial/
59. https://developer.aliyun.com/article/1686465
60. https://jobs.bytedance.com/experienced/m/position/detail/7450383628510759186
61. https://learn.microsoft.com/aspnet/core/grpc/comparison
62. https://protobuf.dev/
63. https://www.huaweicloud.com/product/huaweicloudstack.html
64. https://m.zhipin.com/job_detail/620b21fc007745190nF409-1EVJQ.html
65. https://hr.tencent.com/jobdesc.html?postId=1803020828879626240
66. https://sensetime.jobs.feishu.cn/exp/m/position/detail/7661947342195181874
68. https://jobs.nvidia.com/careers/job/893393297070

相关页面：[[学习路线]] · [[技术点-AI存储]] · [[技术点-分布式存储]] · [[开源项目地图]]
