<div class="hero">
  <span class="hero-eyebrow">Personal Knowledge Base</span>
  <h1>个人知识库</h1>
  <p class="hero-sub">算法研究 → 推理部署 → 系统设计。这里是知识总览，专题能力请前往「知识体系」与「简历」两个板块。</p>
  <div class="hero-points">
    <span class="hero-point">L0–L4 五层知识架构</span>
    <span class="hero-point">量化可溯源的 STAR 简历</span>
    <span class="hero-point">业务知识全览</span>
  </div>
</div>

## 专题板块

<div class="nav-cards">
  <a class="nav-card" href="知识体系">
    <span class="nav-card-title">知识体系</span>
    <span class="nav-card-desc">五层能力架构 · Mermaid 架构图 · 能力交叉点</span>
    <span class="nav-card-tag">Knowledge Architecture</span>
  </a>
  <a class="nav-card" href="简历">
    <span class="nav-card-title">简历</span>
    <span class="nav-card-desc">个人定位 · STAR 项目经历 · 量化成果 · 技能标签</span>
    <span class="nav-card-tag">Resume</span>
  </a>
</div>

## 知识体系速览

> 由 102 篇研究生学习笔记与三段项目经历提炼而成的五层架构，从基础能力到业务应用层层递进。

| 层级 | 定位 | 代表能力 |
|---|---|---|
| L0 基础能力 | 工程师的底层操作系统 | C++ / Python、数据结构、网络、操作系统、数学建模 |
| L1 核心技术 | 专业纵深 | 图像融合（PDBFnet +29.5%）、深度学习训练、相机标定 <0.2px |
| L2 加速与部署 | 从实验室到产品 | TensorRT 220ms、知识蒸馏 1/3 参数、CUDA 提速 55%+ |
| L3 业务应用 | 技术价值落点 | OceanProtect 备份后端、VHA 双活、快照状态机 |
| L4 横向综合 | 贯穿的元能力 | 科研素养、软件评测、高性能计算、学习方法论 |

**走进完整架构：[[知识体系]] →**

## 业务知识

> 学习与业务实践沉淀，原独立 wiki 页面已并入本页总览。

> [!note]- 学习技巧与新人建议
>
> 新人应该多多翻阅的内容（不需要全部掌握，只需要知道遇到什么问题能在哪里找到解答）。
>
> - 陌生业务可通过前辈带教 + 录屏复盘快速掌握，留存操作回放用于复刻练习。学习过程中有疑问即时提出，无需纠结当场弄懂，后续结合工具逐步拆解逻辑、梳理答案。
> - 以 QA 为导向搭建业务认知，从前辈处获取核心业务 Q，围绕问题自主查找 A，快速建立系统化的知识框架。
> - 遇到问题优先独立思考，加深理解、吃透逻辑，同时避免盲目死磕。思路受限、陷入误区时及时调整状态、适度沉淀，重新梳理即可获得新的解题思路。
> - 以任务驱动学习，以工作任务为主线推进学习，同步记录工作中遇到的各类问题，循序渐进拓展业务认知，稳步提升业务能力。

> [!note]- 存储与备份背景知识
>
> **三大存储类型**
>
> - **SAN 块存储**：对外提供裸磁盘 LUN，文件系统由业务服务器安装管理；协议 FC、iSCSI、NVMe-FC、NVMe-RoCE、NVMe-TCP；典型产品 OceanStor Dorado，适合数据库、虚拟机。
> - **NAS 文件存储**：存储设备本身运行文件系统，走 IP 网络；协议 NFS、SMB、HDFS；高级能力配额、WORM、GVS 全局命名空间；典型产品 OceanStor Pacific，适合文档、日志共享。
> - **对象存储**：最小单元 Bucket 桶，基础接口 PUT/GET；适合图片、日志、音视频等非结构化数据；关键技术全局命名空间、纠删码 N+M、多版本、加密、多活容灾。
>
> **通用存储术语**
>
> - Thin Provision 精简分配：虚拟容量映射，物理空间随写入动态分配。
> - QoS：对带宽、IOPS、IO 优先级管控，防止业务互相抢占资源。
> - Dedupe 重复数据删除：只存一份副本，备份场景收益明显。
> - Snapshot 快照：COW 写时复制，只复制映射关系，创建快、占用小，是备份的前置能力。
> - Replication 复制：主 LUN → 从 LUN，用于阵列层灾备。
> - 缓存分层：RAM → SSD/SCM → SSD Tier → HDD Tier，冷热数据分层。
>
> **备份保护基础概念**
>
> - RPO：灾难发生后最多丢失多久的数据；RTO：故障后业务恢复完成需要多久。
> - 容灾优先级：应用层 > 数据库层 > 网络层 > 阵列层，排障、方案设计优先看上层。
> - 备份类型：全量（拷贝全部有效数据）、增量（拷贝上次备份后变化的数据块）、差异（拷贝上次全量后变化的数据块）。
> - 归档：冷数据长期保存，用于合规留存，不用于高频恢复。
>
> **HCS 资源模型**
>
> - 租户：顶层账号，最高权限。
> - O-VDC（组织 VDC）：绑定租户 ID，组织层级，用于组织/部门资源归类。
> - I-VDC（项目 VDC）：实际业务资源池，存放 ECS、磁盘卷。
>
> **OP & Agent 基础概念**
>
> - OP（OceanProtect）本身不存业务数据，只做调度、管理、任务下发、结果汇总；K8s 容器化部署。
> - Agent 客户端代理运行插件 AppPlugin_VAS，真正执行业务逻辑、调用 HCS 接口。
>
> | 项目 | 内置 Agent | 外置 Agent (VHA) |
> |---|---|---|
> | 部署位置 | OP 本机 Pod 内 | 独立外部主机 |
> | 业务执行日志 | 落在 OP 机器 | 落在外置主机；OP 只存 PM 调度日志 |
> | 域名映射 | Agent 侧做域名映射，OP 不用改 hosts | OP 本机 hosts 做域名映射；无 hosts 则走 DNS |
> | 连通要求 | OP 不需要 ping 通客户端 | OP 需要能够 ping 通客户端主机 |
>
> **网络基础概念**
>
> - 管理网：注册、指令下发、控制信令、心跳；备份网：海量备份数据流，大带宽；复制网：复制/容灾同步流量。
> - 五大网段（归档、备份、管理、复制、业务）必须隔离，不能冲突。
> - Bond/Eth-Trunk 网卡链路聚合；LACP/M-LAG 协议；交换机堆叠高可用。
> - NTP UDP 123，节点时间必须对齐；DNS 解析：HCS 证书校验强依赖域名；ping 通 ≠ DNS 解析正常 ≠ 业务通。
>
> **VHA 双活基础**
>
> VHA 是 HCS 的虚拟机双活能力，一台虚拟机同时运行在两套存储，分主端、容端。原有备份流程无 VHA 识别逻辑；改造目标是识别 VHA 实例、区分主/容端、正确拿 LUN、做快照、区分全量/增量备份。

> [!note]- 业务核心流程与 VHA 改造
>
> **整体组网拓扑**
>
> 1. 内置代理组网：`OP(PM+Agent) ↔ HCS/存储`
> 2. 外置代理组网（VHA）：`OP-PM ↔ 网络 ↔ 外置Agent ↔ HCS/存储`
> 3. 管理网：OP-PM 与 Agent 之间注册、指令下发；备份网：Agent 与 HCS 之间做备份数据流。
>
> 关键点：OP 只跟 Agent 通信；Agent 才跟 HCS 存储通信。
>
> **部署与注册流程**
>
> 1. 硬件：网卡规划 4 张网卡，Bond/Eth-Trunk，LACP/M-LAG，交换机堆叠。
> 2. 五大网段隔离；配置 zone 白名单。
> 3. NTP 时间同步（UDP123）；导入 License；版本校验；SSH/NFS/SFTP 可用。
> 4. 客户端主机安装 Agent + AppPlugin_VAS 插件。
> 5. 配置域名映射（hosts/DNS），在 OP 侧执行注册。
>
> **注册失败标准排查步骤（新人排障模板）**
>
> 1. 判断故障大类：代码问题 / 底层网络（VPN、路由、网卡、DNS、防火墙）。
> 2. 分层连通校验：OP-PM ping Agent（管理网）；Agent ping HCS（业务网）；Agent ping HCS（备份网）。
> 3. 判断代理类型：外置检查 OP 本机 hosts，curl 验证域名解析；内置检查 Agent 侧域名映射。
> 4. 分别查看 OP-PM 调度日志、agent-log，定位真实报错。
>
> **日志位置**
>
> 1. OP 端 Nginx 日志：仅看 ping 连通，不能定位业务报错。
> 2. agent-log：注册、建链、任务执行真实报错。
> 3. OP 内部：PM 模块做任务下发；Kafka 作为内部消息队列。
> 4. 内置 Agent 业务日志在 OP 机器；外置 Agent 业务日志在外置主机。
>
> **核心业务流程**
>
> - 流程 1 Agent 插件启动：PluginMain 入口 → 解析参数、初始化环境 → 创建引擎 → 注册扫描/快照/备份处理器 → Thrift 长连接监听等待 OP-PM 任务下发 → 按任务类型进入分支。
> - 流程 2 VDC 资源扫描：入口 `listApplicationResourceV2`；环境初始化刷新 Token → 获取租户、O-VDC/I-VDC 列表 → 循环遍历 VDC 设置上下文 → callApi 调用 HCS 接口多线程采集 → 序列化上报 OP。支持全量扫描、多轮扫描策略。
> - 流程 3 快照创建：入口 `DoCreateSnapshot`；卷互斥锁防同卷并发快照 → 跳过共享卷组装请求 → `CreateSnapshot` 调用存储接口 → `ConfirmSnapshotReady` 轮询阻塞等待快照就绪。
> - 流程 4 全量备份主流程：PRE-INIT 初始化任务上下文 → PRE-Hook 前置校验（ECS、磁盘、快照状态）→ 获取卷信息区分集中式/分布式存储 → 创建任务异步分发子任务 → 子任务 IAM 鉴权 → 创建快照 → LUN 映射 → iSCSI 挂载 → bitmap 拷贝有效块 → 卸载清理。
> - 流程 5 备份状态机：PRE 前置阶段（初始化、hook、创建/激活快照、采集元数据）→ GENERATE 子任务生成（按卷拆分）→ EXEC 执行阶段（上报进度、拷贝脏块）→ POST-JOB 收尾（清理残留快照、写 checkpoint、执行钩子）。
>
> **VHA 双活备份改造**
>
> 改造目标：HCS VHA 双活虚拟机能够正常备份，识别主/容端，获取 LUN，做快照，区分全量/增量。
>
> 1. Step1 VHA 实例识别：输入 VM-ID/VM-Name，调用 `GetVhaInstanceInfo()、IsVhaVolume()`，区分主端/容端。风险点：VHA 接口 Endpoint、IAM-Token 正确性。
> 2. Step2 ID 转换链路：VM-ID → GetMachineMetaData → ShowVolumeDetail(卷ID) → GetVolumeLun → GetVolumeHandler 拿到 LUN。没有 LUN 无法下发快照，备份中断。
> 3. Step3 快照逻辑：判断快照是否存在，存在走增量、不存在新建走全量；VHA 快照 ID 命名 `SNAP-lunId-time`；失败必须回滚删除，不遗留脏快照。
> 4. 代码分支改造点：优先判断 VM 是否 VHA 实例，是则走 OceanStor 快照链路，普通 VM 走原有 cinder 链路；`GetSnapShotByID` 由 VM 维度改 LUN 维度；重写 `checkBackupJobType` 适配全量/增量；增加 CG 一致性组判断、主机/存储故障异常分支。
> 5. 必须覆盖的测试 case：上次备份失败本次增量是否自动转全量；普通非 VHA 兼容性；VM 重名场景；主活容挂/主挂容活/两端存活；快照丢失、网络超时、SSL 异常、DNS 异常。
>
> **开发调试基础**
>
> - 调试工具：curl、Postman。
> - 关键断点：注册（QueryVdcList、callApi）；扫描（setTokenInfo、GetVdcResourceBranch）；快照（CreateSnapshot、ConfirmSnapshotReady）；备份（PRE-INIT、PRE-Hook、GetVolumeHandler、CheckBackupJobType、AsyncExecuteBackupSubJob、bitmap 拷贝）。
> - 插件编译：docker 映射代码目录到 /workspace；编译后 ISO 包替换更新。

> [!note]- QA 清单速查
>
> **Q1：OP 注册客户端，是否需要 OP 能 ping 通客户端主机？**
>
> A：分代理模式。Nginx 日志只记录 ping 连通性，真实注册错误要看 agent-log。外置 Agent：OP 必须能 ping 通客户端主机；内置 Agent：没有这个强制要求。
>
> **Q2：线上注册 HCS 是什么意思？是不是 OP 直连 HCS？**
>
> A：线上注册是华为云侧触发注册，线下注册是本地手动填参数导入。OP 不会直连 HCS，所有接口调用由 Agent 完成。
>
> **Q3：OP 访问 HCS，是谁和谁建立连接？**
>
> A：链路 `OP ↔ Agent ↔ HCS`。OP 只下发任务；业务逻辑、HTTP 调用全部跑在 Agent。
>
> **Q4：OP 是 k8s 部署，客户端是不是只需要配置，不用装软件包？**
>
> A：OP 节点是 k8s 容器，但客户端必须安装 Agent 程序和插件，不是只做配置。
>
> **Q5：某网段访问 HCS 不通，排查哪些点？**
>
> A：路由、网关、防火墙、安全 zone 白名单；检查五大网段是否冲突。
>
> **Q6：eth0 是什么？**
>
> A：物理网卡，一张网卡可以配置多个 IP。
>
> **Q7：Agent 可以 ping 通 HCS 备份网段，为什么备份任务失败？**
>
> A：ping 只是 ICMP 通，不代表路由、防火墙、DNS、证书、业务端口通。常见：VPN 路由、DNS 解析失败、证书域名不匹配。
>
> **Q8：注册失败，看 OP 的 Nginx 日志够不够？**
>
> A：不够。Nginx 只看 ping 连通；注册、建链真实报错优先看 agent-log。OP 只存 PM 调度日志。
>
> **Q9：hosts 和 DNS 的关系？**
>
> A：外置 Agent 场景优先读 OP 本机 hosts，hosts 没有才走 DNS。可用 `curl 域名` 验证解析和连通。
>
> **Q10：VHA 是不是等于外置 Agent？内置 Agent 属于 VHA 吗？**
>
> A：VHA 指虚拟机双活特性，外置 Agent 是代理部署形态，二者概念不等价。内置 Agent 不属于 VHA。
>
> **Q11：快照失败，是全部卷一起回滚吗？**
>
> A：多卷快照场景需要看代码逻辑；开发改造重点保证：快照创建失败要删除已生成的快照，不能遗留脏快照。
>
> **Q12：HCS 证书为什么有时候网络通还报证书错误？**
>
> A：HCS 签发证书绑定域名；三层网络通但访问时域名不匹配，证书校验失败。rdagent 访问 IAM/ECS 必须正确 DNS 解析。

---

本网站内容持续更新中。
