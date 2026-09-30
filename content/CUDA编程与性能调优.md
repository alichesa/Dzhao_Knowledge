<div class="hero">
  <span class="hero-eyebrow">领域 · 高性能计算与推理部署</span>
  <h1>CUDA 编程与性能调优</h1>
  <p class="hero-sub">GPU 算子手写与性能调优。</p>
</div>

## 技能清单

| 技能 | 掌握程度 | 代表成果 | 关联 |
|---|---|---|---|
| CUDA 手写自定义算子 | 动手实践级 | 手写 gather kernel（float/half 模板），ctypes 接入 PyTorch，精度校验；比 torch-cpu 提速 55%+ | = C++ × 深度学习算子 × 性能调优的交叉点 |
| CUDA 性能调优 | 动手实践级 | warp 对齐、共享内存、const __restrict__（约 3 倍加速）、CUDA 流重叠、float4 向量化、减少 bank conflict | → BM3D 改造；TensorRT |

---
相关页面：[[知识体系]] · [[简历]] · [[模型压缩与推理加速]] · [[ONNX与模型转换]] · [[工程化部署]]
