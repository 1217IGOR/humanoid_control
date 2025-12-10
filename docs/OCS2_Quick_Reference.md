# OCS2控制框架论文写作快速参考
# Quick Reference for OCS2 Framework in Papers

## 中文速查 / Chinese Quick Reference

### 一、OCS2核心定义

**OCS2是什么？**
- 名称：Optimal Control for Switched Systems（切换系统的最优控制）
- 开发者：ETH Zurich机器人系统实验室
- 类型：开源最优控制工具箱
- 特点：专为混合动力学系统设计

**适用场景：**
- 腿式机器人
- 接触切换系统
- 复杂约束的最优控制问题

---

### 二、论文结构七要素

1. **OCS2概述** - 介绍背景、理念、应用
2. **理论基础** - 数学描述、混合系统、求解算法
3. **应用架构** - 本项目的系统集成方案
4. **实现细节** - API使用、关键技术选择
5. **优势局限** - 客观评价技术特点
6. **实验结果** - 性能数据、对比分析
7. **总结展望** - 成果总结、未来方向

---

### 三、本项目中的OCS2应用关键点

**系统状态定义 (24维):**
```
x = [h_com, q_b, q_j]^T
- h_com ∈ R^6: 归一化质心动量
- q_b ∈ R^6: 基座位姿
- q_j ∈ R^12: 关节位置
```

**控制输入定义:**
```
u = [f_c, v_j]^T
- f_c ∈ R^12: 接触力（4个接触点）
- v_j ∈ R^12: 关节速度
```

**主要约束:**
1. 摩擦锥约束
2. 支撑足零运动约束
3. 摆动足零力约束
4. 摆动足轨迹约束
5. 足部Roll轴零运动约束

**求解器配置:**
- 算法：SQP（序列二次规划）
- 频率：100Hz
- 时域：1.5s
- 离散：dt=0.015s（约67节点）
- 迭代：1次/周期
- 线程：6个并行线程

**控制架构层次:**
```
步态规划 (Gait Planning)
    ↓
NMPC (100Hz) - 基于OCS2
    ↓
MRT (500Hz) - 模型参考跟踪
    ↓
WBC (500Hz) - 全身控制
    ↓
PD Controller (>1000Hz)
```

---

### 四、写作要点提醒

**必须说明的内容:**
- [ ] OCS2的定义和来源
- [ ] 为何选择OCS2
- [ ] 状态和控制输入的具体定义
- [ ] 所有约束的物理意义
- [ ] 求解器选择及参数配置
- [ ] 与其他模块的接口
- [ ] 实时性能数据

**避免的误区:**
- ❌ 成为OCS2使用手册
- ❌ 过度技术化失去可读性
- ❌ 忽略理论支撑
- ❌ 缺少实验验证
- ❌ 不讨论局限性

**推荐的写作风格:**
- ✓ 理论与实践结合
- ✓ 公式配文字解释
- ✓ 使用图表辅助
- ✓ 客观评价优缺点
- ✓ 突出应用创新点

---

### 五、核心概念速查

**最优控制问题标准形式:**
```
minimize: φ(x(t_f)) + ∫l(x,u,t)dt
subject to:
  - x(t_0) = x_0
  - ẋ = f(x,u,t)
  - g(x,u,t) = 0
  - h(x,u,t) ≥ 0
```

**三种求解算法对比:**

| 算法 | 特点 | 适用场景 |
|------|------|---------|
| DDP | 快速、二次近似 | 无约束/软约束 |
| SQP | 硬约束处理好 | 复杂约束（本项目使用） |
| IPM | 稳定性好 | 严格约束满足 |

**质心动力学 vs 完整动力学:**
- 质心动力学：简化模型，计算效率高，适合MPC
- 完整动力学：精确模型，计算量大，适合WBC

---

### 六、常用术语对照表

| 中文 | 英文 | 缩写 |
|------|------|------|
| 最优控制 | Optimal Control | OC |
| 模型预测控制 | Model Predictive Control | MPC |
| 非线性MPC | Nonlinear MPC | NMPC |
| 序列二次规划 | Sequential Quadratic Programming | SQP |
| 微分动态规划 | Differential Dynamic Programming | DDP |
| 内点法 | Interior Point Method | IPM |
| 全身控制 | Whole Body Control | WBC |
| 质心动力学 | Centroidal Dynamics | - |
| 模型参考跟踪 | Model Reference Tracking | MRT |
| 摩擦锥 | Friction Cone | - |
| 步态规划 | Gait Planning | - |
| 接触切换 | Contact Switching | - |

---

### 七、参考文献模板

**OCS2核心文献:**
```
[1] Farshidian, F., et al. "OCS2: An Optimal Control and Trajectory 
    Optimization Toolbox." IEEE Control Systems Letters, 2020.
    
[2] Grandia, R., et al. "Feedback MPC for Torque-Controlled Legged 
    Robots." IEEE/RSJ International Conference on Intelligent 
    Robots and Systems (IROS), 2019.
```

**质心动力学相关:**
```
[3] Wensing, P. M., & Orin, D. E. "Generation of dynamic humanoid 
    behaviors through task-space control with conic optimization." 
    IEEE International Conference on Robotics and Automation, 2013.
```

**相关开源项目:**
```
[4] OCS2 GitHub Repository: https://github.com/leggedrobotics/ocs2
[5] OCS2 Documentation: https://leggedrobotics.github.io/ocs2/
```

---

## English Quick Reference / 英文速查

### 1. OCS2 Core Definition

**What is OCS2?**
- Name: Optimal Control for Switched Systems
- Developer: Robotic Systems Lab, ETH Zurich
- Type: Open-source optimal control toolbox
- Feature: Designed for hybrid dynamical systems

**Application Scenarios:**
- Legged robots
- Contact switching systems
- Complex constrained optimal control

---

### 2. Seven Elements of Paper Structure

1. **OCS2 Overview** - Background, philosophy, applications
2. **Theoretical Foundation** - Math formulation, hybrid systems, algorithms
3. **Application Architecture** - System integration in this project
4. **Implementation Details** - API usage, technical choices
5. **Advantages & Limitations** - Objective evaluation
6. **Experimental Results** - Performance data, comparisons
7. **Conclusion & Future** - Summary, future directions

---

### 3. Key Points in This Project

**System State (24-dim):**
```
x = [h_com, q_b, q_j]^T
- h_com ∈ R^6: normalized centroidal momentum
- q_b ∈ R^6: base pose
- q_j ∈ R^12: joint positions
```

**Control Input:**
```
u = [f_c, v_j]^T
- f_c ∈ R^12: contact forces (4 points)
- v_j ∈ R^12: joint velocities
```

**Main Constraints:**
1. Friction cone constraint
2. Zero motion at stance foot
3. Zero force at swing foot
4. Swing foot trajectory constraint
5. Zero roll motion of foot

**Solver Configuration:**
- Algorithm: SQP
- Frequency: 100Hz
- Horizon: 1.5s
- Discretization: dt=0.015s (~67 nodes)
- Iterations: 1 per cycle
- Threads: 6 parallel threads

**Control Hierarchy:**
```
Gait Planning
    ↓
NMPC (100Hz) - OCS2-based
    ↓
MRT (500Hz) - Model Reference Tracking
    ↓
WBC (500Hz) - Whole Body Control
    ↓
PD Controller (>1000Hz)
```

---

### 4. Writing Checklist

**Must Include:**
- [ ] OCS2 definition and origin
- [ ] Reason for choosing OCS2
- [ ] Specific state and input definitions
- [ ] Physical meaning of all constraints
- [ ] Solver choice and parameters
- [ ] Interface with other modules
- [ ] Real-time performance data

**Common Pitfalls:**
- ❌ Becoming an OCS2 user manual
- ❌ Over-technical without readability
- ❌ Ignoring theoretical support
- ❌ Lacking experimental validation
- ❌ Not discussing limitations

**Recommended Style:**
- ✓ Combine theory with practice
- ✓ Explain equations with text
- ✓ Use figures/tables
- ✓ Objectively evaluate pros/cons
- ✓ Highlight application innovations

---

### 5. Key Concepts Reference

**Standard OCP Form:**
```
minimize: φ(x(t_f)) + ∫l(x,u,t)dt
subject to:
  - x(t_0) = x_0
  - ẋ = f(x,u,t)
  - g(x,u,t) = 0
  - h(x,u,t) ≥ 0
```

**Algorithm Comparison:**

| Algorithm | Features | Use Cases |
|-----------|----------|-----------|
| DDP | Fast, quadratic approx. | Unconstrained/soft constraints |
| SQP | Good hard constraint handling | Complex constraints (used here) |
| IPM | Stable | Strict constraint satisfaction |

**Centroidal vs Full Dynamics:**
- Centroidal: Simplified, efficient, suitable for MPC
- Full: Accurate, computationally expensive, suitable for WBC

---

### 6. Important Numbers for This Project

**Dimensions:**
- State: 24D
- Control: 24D
- Contact forces: 12D (4 points × 3D)
- Joint positions: 12D
- Joint velocities: 12D

**Frequencies:**
- NMPC: 100 Hz
- MRT: 500 Hz
- WBC: 500 Hz
- PD: >1000 Hz

**Timing:**
- Horizon: 1.5 seconds
- Discretization: 0.015 seconds
- Nodes: ~67 (= 1.5 / 0.015)

**Performance:**
- Threads: 6
- SQP iterations: 1 per cycle
- Solver: HPIPM (for QP subproblems)

---

### 7. Quick Formula Reference

**Optimal Control Problem:**
$$\min_{u(\cdot)} \phi(x(t_f)) + \int_{t_0}^{t_f} l(x(t), u(t), t) dt$$

**State Space:**
$$x = [h_{com}^T, q_b^T, q_j^T]^T \in \mathbb{R}^{24}$$

**Control Input:**
$$u = [f_c^T, v_j^T]^T \in \mathbb{R}^{24}$$

**Centroidal Momentum:**
$$h_{com} = [L_x, L_y, L_z, k_x, k_y, k_z]^T$$
where $L$ is linear momentum, $k$ is angular momentum

---

### 8. Useful Resources

**Official:**
- Documentation: https://leggedrobotics.github.io/ocs2/
- GitHub: https://github.com/leggedrobotics/ocs2
- ROS2 Port: https://github.com/legubiao/ocs2_ros2

**This Project:**
- Main README: `/README.md`
- Task Config: `/humanoid_interface/config/mpc/task.info`
- Interface: `/humanoid_interface/include/humanoid_interface/HumanoidInterface.h`

**Community:**
- ROS Discourse
- GitHub Issues/Discussions
- Research papers from ETH RSL

---

## 论文写作三步走 / Three-Step Writing Process

### 第一步：明确OCS2定位 / Step 1: Position OCS2
- 说明OCS2是什么（工具箱）
- 解释为何需要它（混合系统、实时性）
- 概述其在系统中的作用（NMPC核心）

### 第二步：详述技术实现 / Step 2: Technical Implementation
- 给出数学问题描述
- 定义状态、输入、约束
- 说明求解器配置
- 展示与其他模块集成

### 第三步：验证与评价 / Step 3: Validation and Evaluation
- 提供实验数据
- 分析性能表现
- 讨论优势和局限
- 给出改进建议

---

## 关键提示 / Key Reminders

**🎯 核心目标：**
让读者理解OCS2在本项目中的价值，而非OCS2本身的所有细节

**⚠️ 常见错误：**
1. 过多描述OCS2的通用功能
2. 缺少本项目特定的应用细节
3. 理论与实践脱节
4. 没有性能数据支撑

**✅ 成功要素：**
1. 清晰的问题定义
2. 合理的技术选择
3. 完整的实验验证
4. 诚实的优缺点讨论

---

## 最后检查 / Final Check

**论文完成前，问自己：**
1. 读者能理解OCS2是什么吗？
2. 读者知道为什么选择OCS2吗？
3. 技术细节足够清楚吗？
4. 有实验数据支持吗？
5. 讨论了局限性了吗？
6. 突出了本项目的创新点吗？

**如果以上全部是"是"，那么这部分内容就准备好了！**
**If all above are "YES", then this section is ready!**

---

*本快速参考基于humanoid_control项目创建*
*This quick reference is based on the humanoid_control project*

*最后更新 / Last Updated: 2025-12-10*
