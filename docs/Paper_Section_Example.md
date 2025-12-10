# 论文章节示例：OCS2控制框架介绍
# Example Paper Section: Introduction to OCS2 Control Framework

本文档提供一个具体的论文章节示例，展示如何将OCS2框架介绍部分融入到人形机器人控制的论文中。

This document provides a concrete example of how to integrate an OCS2 framework introduction into a humanoid robot control paper.

---

## 示例论文章节结构 / Example Paper Section Structure

以下是一个推荐的章节结构，适用于会议论文（如IROS、ICRA）或期刊论文（如T-RO、RAL）。

Below is a recommended section structure suitable for conference papers (e.g., IROS, ICRA) or journal papers (e.g., T-RO, RAL).

---

## 第3章：基于OCS2的人形机器人运动控制系统
## Chapter 3: OCS2-Based Motion Control System for Humanoid Robots

### 3.1 OCS2最优控制框架概述
### 3.1 Overview of OCS2 Optimal Control Framework

#### 中文示例 / Chinese Example:

为了实现人形机器人的实时最优运动控制，本文采用OCS2（Optimal Control for Switched Systems）工具箱作为非线性模型预测控制（NMPC）的核心框架。OCS2是由苏黎世联邦理工学院（ETH Zurich）机器人系统实验室开发的开源最优控制工具箱[1]，专门针对具有混合动力学特性的机器人系统设计。

OCS2框架的核心优势在于其能够高效处理具有离散模态切换的连续动力学系统。对于腿式机器人而言，足部与地面的接触状态切换构成了典型的混合系统特征。OCS2通过模态序列（Mode Schedule）管理这些离散事件，同时在每个模态下求解连续的最优控制问题，从而实现了理论严谨性与工程实用性的良好平衡。

与传统的控制方法相比，基于OCS2的方法具有以下特点：
1. **理论完备性**：基于最优控制理论，提供收敛性和稳定性保证
2. **实时性能**：针对实时控制场景优化，支持多线程并行计算
3. **灵活性**：模块化设计便于定制动力学模型、代价函数和约束条件
4. **可扩展性**：集成多种求解算法（DDP、SQP、IPM），适应不同应用需求

---

#### English Example:

To achieve real-time optimal motion control for humanoid robots, this work employs the OCS2 (Optimal Control for Switched Systems) toolbox as the core framework for Nonlinear Model Predictive Control (NMPC). OCS2 is an open-source optimal control toolbox developed by the Robotic Systems Lab at ETH Zurich [1], specifically designed for robotic systems with hybrid dynamics.

The core advantage of the OCS2 framework lies in its ability to efficiently handle continuous dynamical systems with discrete mode switching. For legged robots, the switching of foot-ground contact states constitutes a typical hybrid system characteristic. OCS2 manages these discrete events through a Mode Schedule while solving continuous optimal control problems within each mode, achieving a good balance between theoretical rigor and engineering practicality.

Compared to traditional control methods, the OCS2-based approach features:
1. **Theoretical Completeness**: Based on optimal control theory with convergence and stability guarantees
2. **Real-time Performance**: Optimized for real-time control with multi-threaded parallel computation support
3. **Flexibility**: Modular design facilitates customization of dynamics models, cost functions, and constraints
4. **Scalability**: Integrates multiple solving algorithms (DDP, SQP, IPM) for diverse application needs

---

### 3.2 最优控制问题描述
### 3.2 Optimal Control Problem Formulation

#### 中文示例 / Chinese Example:

在OCS2框架下，人形机器人的运动控制问题被表述为如下最优控制问题：

$$
\begin{aligned}
\min_{\mathbf{u}(\cdot)} \quad & \phi(\mathbf{x}(t_f)) + \int_{t_0}^{t_f} l(\mathbf{x}(t), \mathbf{u}(t), t) \, dt \\
\text{s.t.} \quad & \mathbf{x}(t_0) = \mathbf{x}_0 \\
& \dot{\mathbf{x}}(t) = \mathbf{f}(\mathbf{x}(t), \mathbf{u}(t), t) \\
& \mathbf{g}(\mathbf{x}(t), \mathbf{u}(t), t) = \mathbf{0} \\
& \mathbf{h}(\mathbf{x}(t), \mathbf{u}(t), t) \geq \mathbf{0}
\end{aligned}
$$

其中，$\mathbf{x}(t) \in \mathbb{R}^n$ 为系统状态，$\mathbf{u}(t) \in \mathbb{R}^m$ 为控制输入，$\phi(\cdot)$ 为终端代价函数，$l(\cdot)$ 为运行代价函数，$\mathbf{f}(\cdot)$ 为系统动力学方程，$\mathbf{g}(\cdot)$ 和 $\mathbf{h}(\cdot)$ 分别为等式约束和不等式约束。

**状态空间定义：** 本文采用OCS2的质心动力学模型，系统状态定义为：

$$
\mathbf{x} = [\mathbf{h}_{com}^T, \mathbf{q}_b^T, \mathbf{q}_j^T]^T \in \mathbb{R}^{24}
$$

其中：
- $\mathbf{h}_{com} \in \mathbb{R}^6$ 为归一化质心动量，包括线动量和角动量
- $\mathbf{q}_b \in \mathbb{R}^6$ 为基座的六自由度位姿（位置+姿态）
- $\mathbf{q}_j \in \mathbb{R}^{12}$ 为12个关节的位置

**控制输入定义：** 控制输入包括接触力和关节速度：

$$
\mathbf{u} = [\mathbf{f}_c^T, \mathbf{v}_j^T]^T \in \mathbb{R}^{24}
$$

其中：
- $\mathbf{f}_c \in \mathbb{R}^{12}$ 为四个接触点（左右足的足尖和足跟）的接触力
- $\mathbf{v}_j \in \mathbb{R}^{12}$ 为12个关节的速度

这种状态和输入的定义方式源于质心动力学理论[2]，相比完整动力学模型大幅降低了计算复杂度，同时保持了对关键运动特性的准确描述。

---

#### English Example:

Under the OCS2 framework, the motion control problem for humanoid robots is formulated as the following optimal control problem:

$$
\begin{aligned}
\min_{\mathbf{u}(\cdot)} \quad & \phi(\mathbf{x}(t_f)) + \int_{t_0}^{t_f} l(\mathbf{x}(t), \mathbf{u}(t), t) \, dt \\
\text{s.t.} \quad & \mathbf{x}(t_0) = \mathbf{x}_0 \\
& \dot{\mathbf{x}}(t) = \mathbf{f}(\mathbf{x}(t), \mathbf{u}(t), t) \\
& \mathbf{g}(\mathbf{x}(t), \mathbf{u}(t), t) = \mathbf{0} \\
& \mathbf{h}(\mathbf{x}(t), \mathbf{u}(t), t) \geq \mathbf{0}
\end{aligned}
$$

where $\mathbf{x}(t) \in \mathbb{R}^n$ is the system state, $\mathbf{u}(t) \in \mathbb{R}^m$ is the control input, $\phi(\cdot)$ is the terminal cost function, $l(\cdot)$ is the running cost function, $\mathbf{f}(\cdot)$ represents system dynamics, and $\mathbf{g}(\cdot)$ and $\mathbf{h}(\cdot)$ are equality and inequality constraints respectively.

**State Space Definition:** This work adopts OCS2's centroidal dynamics model with the system state defined as:

$$
\mathbf{x} = [\mathbf{h}_{com}^T, \mathbf{q}_b^T, \mathbf{q}_j^T]^T \in \mathbb{R}^{24}
$$

where:
- $\mathbf{h}_{com} \in \mathbb{R}^6$ is the normalized centroidal momentum (linear and angular)
- $\mathbf{q}_b \in \mathbb{R}^6$ is the six-DOF base pose (position + orientation)
- $\mathbf{q}_j \in \mathbb{R}^{12}$ are the positions of 12 joints

**Control Input Definition:** The control input comprises contact forces and joint velocities:

$$
\mathbf{u} = [\mathbf{f}_c^T, \mathbf{v}_j^T]^T \in \mathbb{R}^{24}
$$

where:
- $\mathbf{f}_c \in \mathbb{R}^{12}$ are contact forces at four contact points (toes and heels of left and right feet)
- $\mathbf{v}_j \in \mathbb{R}^{12}$ are velocities of 12 joints

This state and input definition originates from centroidal dynamics theory [2], significantly reducing computational complexity compared to full dynamics models while maintaining accurate descriptions of key motion characteristics.

---

### 3.3 约束条件设计
### 3.3 Constraint Design

#### 中文示例 / Chinese Example:

为了确保生成的运动轨迹满足物理约束和安全要求，本文在OCS2框架中实现了以下约束条件：

**1) 摩擦锥约束（Friction Cone Constraint）**

确保接触力满足库伦摩擦定律，防止足部滑动：

$$
\sqrt{f_{c,x}^2 + f_{c,y}^2} \leq \mu f_{c,z}
$$

其中 $\mu$ 为摩擦系数，$(f_{c,x}, f_{c,y}, f_{c,z})$ 为接触点的三维接触力。在实现中，摩擦锥被近似为金字塔约束以提高求解效率。

**2) 支撑足零运动约束（Zero Motion at Stance Foot）**

支撑相的足部位置应保持不变，速度为零：

$$
\mathbf{p}_{foot}(t) = \mathbf{p}_{foot}^{ref}, \quad \dot{\mathbf{p}}_{foot}(t) = \mathbf{0}
$$

该约束通过OCS2的状态约束接口实现，确保支撑足不发生滑动。

**3) 摆动足零力约束（Zero Force at Swing Foot）**

摆动相的足部不应产生接触力：

$$
\mathbf{f}_{c,swing}(t) = \mathbf{0}
$$

**4) 摆动足轨迹约束（Swing Foot Trajectory Constraint）**

摆动足应沿规划的三次样条曲线运动，特别是在z轴方向：

$$
z_{foot}(t) = \text{CubicSpline}(t; z_0, z_f, \dot{z}_0, \dot{z}_f)
$$

其中 $z_0, z_f$ 为起始和终止高度，$\dot{z}_0, \dot{z}_f$ 为起始和终止速度。通过配置文件，可调整摆动高度（默认6cm）、起飞速度（6cm/s）和着陆速度（-4cm/s）。

**5) 足部Roll轴零运动约束（Zero Roll Motion Constraint）**

为防止足部侧向翻转，限制足部绕Roll轴的旋转：

$$
\theta_{roll,foot}(t) = 0, \quad \dot{\theta}_{roll,foot}(t) = 0
$$

这些约束的设计既保证了运动的物理可行性，又为优化求解器提供了明确的边界条件，使得NMPC能够生成稳定、安全的运动轨迹。

---

#### English Example:

To ensure generated motion trajectories satisfy physical constraints and safety requirements, this work implements the following constraints in the OCS2 framework:

**1) Friction Cone Constraint**

Ensures contact forces satisfy Coulomb's friction law to prevent foot slipping:

$$
\sqrt{f_{c,x}^2 + f_{c,y}^2} \leq \mu f_{c,z}
$$

where $\mu$ is the friction coefficient and $(f_{c,x}, f_{c,y}, f_{c,z})$ are the 3D contact forces. In implementation, the friction cone is approximated as a pyramid constraint for improved solving efficiency.

**2) Zero Motion at Stance Foot**

The stance foot position should remain constant with zero velocity:

$$
\mathbf{p}_{foot}(t) = \mathbf{p}_{foot}^{ref}, \quad \dot{\mathbf{p}}_{foot}(t) = \mathbf{0}
$$

This constraint is implemented through OCS2's state constraint interface, ensuring no slipping at the stance foot.

**3) Zero Force at Swing Foot**

The swing foot should produce no contact forces:

$$
\mathbf{f}_{c,swing}(t) = \mathbf{0}
$$

**4) Swing Foot Trajectory Constraint**

The swing foot should follow a planned cubic spline trajectory, particularly in the z-axis:

$$
z_{foot}(t) = \text{CubicSpline}(t; z_0, z_f, \dot{z}_0, \dot{z}_f)
$$

where $z_0, z_f$ are initial and final heights, $\dot{z}_0, \dot{z}_f$ are initial and final velocities. Configuration files allow adjustment of swing height (default 6cm), lift-off velocity (6cm/s), and touchdown velocity (-4cm/s).

**5) Zero Roll Motion Constraint**

To prevent lateral foot rotation, the roll axis rotation is constrained:

$$
\theta_{roll,foot}(t) = 0, \quad \dot{\theta}_{roll,foot}(t) = 0
$$

These constraint designs ensure motion physical feasibility while providing clear boundary conditions for the optimization solver, enabling NMPC to generate stable and safe motion trajectories.

---

### 3.4 求解算法配置
### 3.4 Solver Algorithm Configuration

#### 中文示例 / Chinese Example:

本文采用序列二次规划（Sequential Quadratic Programming, SQP）算法求解上述最优控制问题。SQP通过多重打靶法将连续时间的最优控制问题转化为离散的非线性规划（NLP）问题，再迭代求解一系列二次规划（QP）子问题。

**关键配置参数：**

- **时域长度**：$T = 1.5$ s，能够覆盖约1.5个步态周期
- **时间离散化**：$\Delta t = 0.015$ s，对应约67个节点
- **更新频率**：100 Hz，即每0.01s求解一次
- **SQP迭代次数**：1次/周期（实时性考虑）
- **并行线程数**：6个线程
- **QP求解器**：HPIPM（高性能内点法）
- **积分器**：RK2（二阶Runge-Kutta）

**实时性能优化策略：**

1. **热启动（Warm Start）**：利用上一周期的解作为初始猜测，显著加快收敛速度
2. **代码生成与缓存**：使用CppAD自动微分工具预生成动力学函数的C++代码并缓存，避免运行时重复编译
3. **并行计算**：充分利用多核处理器，对不同时间节点的计算进行并行化
4. **编译优化**：必须使用RelWithDebInfo或Release编译级别，启用编译器优化

通过以上配置，NMPC模块能够稳定地以100Hz频率运行，平均求解时间约为5-8ms，满足实时控制要求。表1展示了不同模块的运行频率统计。

**表1：控制系统各模块运行频率**

| 模块 | 频率 | 功能 |
|------|------|------|
| NMPC | 100 Hz | 基于OCS2的轨迹优化 |
| MRT | 500 Hz | 模型参考跟踪 |
| WBC | 500 Hz | 全身控制（QP） |
| 状态估计 | 500 Hz | 卡尔曼滤波 |
| PD控制 | >1000 Hz | 关节级力矩控制 |

---

#### English Example:

This work employs the Sequential Quadratic Programming (SQP) algorithm to solve the optimal control problem. SQP transforms the continuous-time optimal control problem into a discrete Nonlinear Programming (NLP) problem through multiple shooting, then iteratively solves a series of Quadratic Programming (QP) subproblems.

**Key Configuration Parameters:**

- **Horizon Length**: $T = 1.5$ s, covering approximately 1.5 gait cycles
- **Time Discretization**: $\Delta t = 0.015$ s, corresponding to ~67 nodes
- **Update Frequency**: 100 Hz (solving every 0.01s)
- **SQP Iterations**: 1 per cycle (for real-time performance)
- **Parallel Threads**: 6 threads
- **QP Solver**: HPIPM (High-Performance Interior Point Method)
- **Integrator**: RK2 (2nd-order Runge-Kutta)

**Real-time Performance Optimization Strategies:**

1. **Warm Start**: Uses the previous cycle's solution as initial guess, significantly accelerating convergence
2. **Code Generation and Caching**: Pre-generates C++ code for dynamics functions using CppAD automatic differentiation and caches it, avoiding runtime recompilation
3. **Parallel Computation**: Fully utilizes multi-core processors by parallelizing computations across different time nodes
4. **Compilation Optimization**: Must use RelWithDebInfo or Release build level with compiler optimizations enabled

With these configurations, the NMPC module runs stably at 100Hz with average solving time of 5-8ms, meeting real-time control requirements. Table 1 shows the operating frequency statistics for different modules.

**Table 1: Operating Frequencies of Control System Modules**

| Module | Frequency | Function |
|--------|-----------|----------|
| NMPC | 100 Hz | OCS2-based trajectory optimization |
| MRT | 500 Hz | Model reference tracking |
| WBC | 500 Hz | Whole body control (QP) |
| State Estimation | 500 Hz | Kalman filtering |
| PD Control | >1000 Hz | Joint-level torque control |

---

### 3.5 与其他控制模块的集成
### 3.5 Integration with Other Control Modules

#### 中文示例 / Chinese Example:

OCS2作为NMPC的核心求解引擎，通过标准化的接口与其他控制模块协同工作，构成完整的分层控制架构（如图1所示）。

**上游接口：步态规划器**
- 接收步态规划器生成的接触序列（ModeSchedule）
- 接收参考轨迹（期望速度、姿态等）
- 这些信息通过SwitchedModelReferenceManager传递给OCS2

**下游接口：模型参考跟踪（MRT）**
- OCS2求解得到的最优轨迹存储在内部缓冲区
- MRT节点以500Hz频率查询当前时刻的最优状态和输入
- 通过线性插值提供高频参考信号

**与全身控制（WBC）的协同**
- MRT提供的关节位置、速度作为WBC的参考
- WBC重新求解QP问题，输出关节加速度和力矩
- 这种两层优化结构能够补偿建模误差，提高鲁棒性

通过ROS2的标准通信机制，OCS2与系统其他部分保持松耦合，便于独立测试和参数调优。

---

#### English Example:

OCS2 serves as the core solving engine for NMPC, working cooperatively with other control modules through standardized interfaces to form a complete hierarchical control architecture (as shown in Figure 1).

**Upstream Interface: Gait Planner**
- Receives contact sequence (ModeSchedule) from the gait planner
- Receives reference trajectories (desired velocity, posture, etc.)
- This information is passed to OCS2 through SwitchedModelReferenceManager

**Downstream Interface: Model Reference Tracking (MRT)**
- Optimal trajectory solved by OCS2 is stored in an internal buffer
- MRT node queries optimal states and inputs at current time at 500Hz
- Provides high-frequency reference signals through linear interpolation

**Coordination with Whole Body Control (WBC)**
- Joint positions and velocities provided by MRT serve as WBC references
- WBC re-solves QP problems to output joint accelerations and torques
- This two-layer optimization structure compensates for modeling errors and improves robustness

Through ROS2's standard communication mechanisms, OCS2 maintains loose coupling with other system components, facilitating independent testing and parameter tuning.

---

## 参考文献格式示例 / Reference Format Examples

### IEEE格式 / IEEE Format:

```
[1] F. Farshidian, E. Jelavic, A. Satapathy, M. Giftthaler, and J. Buchli, 
    "Real-time motion planning of legged robots: A model predictive control 
    approach," in 2017 IEEE-RAS 17th International Conference on Humanoid 
    Robotics (Humanoids), 2017, pp. 577-584.

[2] P. M. Wensing and D. E. Orin, "Generation of dynamic humanoid behaviors 
    through task-space control with conic optimization," in 2013 IEEE 
    International Conference on Robotics and Automation, 2013, pp. 3103-3109.

[3] R. Grandia, F. Farshidian, R. Ranftl, and M. Hutter, "Feedback MPC for 
    torque-controlled legged robots," in 2019 IEEE/RSJ International 
    Conference on Intelligent Robots and Systems (IROS), 2019, pp. 4730-4737.
```

---

## 图表建议 / Figure Recommendations

建议包含以下图表来增强文章的可读性：

Recommended figures to enhance readability:

1. **系统架构图** / System Architecture Diagram
   - 显示OCS2与其他模块的关系
   - 标注数据流和频率

2. **控制流程图** / Control Flow Diagram
   - 时序关系
   - 决策节点

3. **状态空间示意图** / State Space Illustration
   - 可视化24维状态的构成
   - 使用机器人模型示意

4. **约束可视化** / Constraint Visualization
   - 摩擦锥示意图
   - 摆动足轨迹示例

5. **性能数据图** / Performance Data Charts
   - 求解时间统计（箱线图）
   - 跟踪误差曲线

---

## 写作技巧总结 / Writing Tips Summary

### ✅ 好的做法 / Good Practices:
1. 先给出直观解释，再展示数学公式
2. 每个公式都配有符号说明
3. 使用真实的项目参数（24维状态、100Hz等）
4. 说明技术选择的原因
5. 讨论实时性能和优化策略

### ❌ 避免的做法 / Practices to Avoid:
1. 直接罗列OCS2的所有功能
2. 缺少数值示例和具体配置
3. 只有理论没有实现细节
4. 不讨论局限性和权衡
5. 忽略与系统其他部分的关系

---

*本示例基于humanoid_control项目的实际实现*
*This example is based on the actual implementation of the humanoid_control project*
