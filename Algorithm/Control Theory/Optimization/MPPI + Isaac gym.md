# 主要内容

基础方法：改进式 MPPI（Model Predictive Path Integral Control）

属于采样型随机最优控制（sampling-based stochastic optimal control），用采样替代优化器、用蒙特卡洛优化替代梯度优化的 MPC，把控制问题转成概率分布估计问题

本文采用传统 MPPI 控制架构，用支持 GPUs 并行计算的仿真器（文中采用 isaacgym）替代传统的显式动力学建模过程（用高性能数值求解替代解析方程求解）

# 范式创新

用

> **仿真 → 采样 → 控制**

替代

> **建模 → 优化 → 控制**

# 解决问题

1. 解决了 **传统 MPPI 依赖显式动力学建模，难以处理动力学不光滑**（碰撞/摩擦/接触 可能会导致数学上梯度消失、雅克比不稳定、非凸等问题）**类型任务** 的问题
2. 解决了 **传统 MPPI rollout 计算量大** 的问题

# 适用场景

1. 复杂接触但非抓取类操作任务（pushing、避障等）可用作底层主控制器/规划器
2. 模仿学习类任务可用作数据生成器（最优控制+函数逼近）
3. 长序列任务可在异常状态（顶层策略失效）时用作局部动作生成（微调）

# 局限性

* horizon 有限，无法处理长序列任务
* 高层语义难以表达，loss function 难设计

# 参考资料

- [1] [Sampling-based MPC Leveraging Parallelizable Physics Simulations](https://arxiv.org/pdf/2307.09105)
- [2] [mppi-isaac code](https://github.com/tud-amr/mppi-isaac) 