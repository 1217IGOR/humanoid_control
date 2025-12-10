# Guide for Writing about OCS2 Control Framework in Academic Papers

## Overview of Writing Approach

Based on the analysis of this project, when writing a paper section about the OCS2 control framework, you should adopt a "whole-to-part, theory-to-practice" approach. This guide will help you systematically introduce the OCS2 framework in the context of humanoid robot control.

---

## I. Recommended Paper Structure

### 1. Introduction to OCS2 Control Framework

**Key Points:**
- Background and design philosophy of OCS2
- Core advantages as an optimal control toolbox
- Wide applications in legged robot control

**Content Framework:**

```
1.1 OCS2 Overview
   - OCS2 (Optimal Control for Switched Systems) is an open-source optimal control 
     toolbox developed by Robotic Systems Lab (RSL) at ETH Zurich
   - Specifically designed for robotic systems with hybrid dynamics
   - Provides a complete toolchain from problem modeling to real-time solving

1.2 Core Design Philosophy
   - Modular design: decomposing optimal control problems into independent, reusable modules
   - Real-time performance: optimized for real-time control scenarios
   - Extensibility: supports multiple solvers and dynamics models
   - Usability: provides high-level APIs and rich robotic examples

1.3 Application Scenarios
   - Motion control of legged robots
   - Dynamical systems with contact switching
   - Optimal control problems with complex constraints
```

---

### 2. Theoretical Foundation of OCS2

**Key Points:**
- Mathematical formulation of optimal control problems
- How OCS2 handles switched systems
- Theoretical basis of solving algorithms

**Content Framework:**

```
2.1 Optimal Control Problem Formulation
   
   In OCS2, the standard optimal control problem is formulated as:
   
   minimize: φ(x(t_f)) + ∫[t_0,t_f] l(x(t), u(t), t) dt
   
   subject to:
   - Initial state constraint: x(t_0) = x_0
   - System dynamics: ẋ(t) = f(x(t), u(t), t)
   - State-input equality constraints: g_1(x(t), u(t), t) = 0
   - State-only equality constraints: g_2(x(t), t) = 0
   - Inequality constraints: h(x(t), u(t), t) ≥ 0
   
   where:
   - x(t) ∈ R^n: system state
   - u(t) ∈ R^m: control input
   - φ(·): terminal cost function
   - l(·): running cost function

2.2 Hybrid Systems and Mode Switching
   
   - OCS2 is specifically designed to handle hybrid dynamical systems with discrete events
   - Supports system switching between different operational modes
   - In legged robots, typical mode switching scenarios include:
     * Changes in foot contact states (contact ↔ swing)
     * Gait pattern transitions (standing → walking → running)
   
   - Hybrid system representation:
     ẋ = f_σ(t)(x, u, t)
     where σ(t) denotes the current operational mode

2.3 Solution Algorithms
   
   OCS2 integrates multiple efficient numerical solution methods:
   
   a) Differential Dynamic Programming (DDP)
      - Iterative optimization method based on local quadratic approximation
      - Suitable for unconstrained or soft-constrained problems
      - High computational efficiency, suitable for real-time applications
   
   b) Sequential Quadratic Programming (SQP)
      - Transforms nonlinear optimization into a series of QP subproblems
      - Excels at handling hard constraints
      - Uses multiple shooting for time discretization
      - This project primarily uses this method
   
   c) Interior Point Method (IPM)
      - Handles inequality constraints through barrier functions
      - Provides more stable constraint satisfaction performance
```

---

### 3. Application Architecture in Humanoid Robot Control

**Key Points:**
- How this project uses OCS2
- Relationships between modules
- Role of OCS2 in the overall control system

**Content Framework:**

```
3.1 Overall Control Architecture

This project builds a hierarchical control architecture based on OCS2:

┌─────────────────────────────────────────────────┐
│          High-Level Planning                     │
│  - Gait Planning                                 │
│  - Reference Generation                          │
└─────────────────┬───────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────┐
│     Nonlinear MPC (NMPC with OCS2)              │
│  - Optimal control problem solving with OCS2     │
│  - Frequency: 100Hz                              │
│  - Prediction horizon: 1.5s                      │
└─────────────────┬───────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────┐
│     Model Reference Tracking (MRT)               │
│  - High-frequency interpolation (500Hz)          │
│  - Provides optimal state and input references   │
└─────────────────┬───────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────┐
│     Whole Body Control (WBC)                     │
│  - QP optimization for joint torques             │
│  - Frequency: 500Hz                              │
└─────────────────┬───────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────┐
│          PD Controller                           │
│  - Joint-level torque output                     │
│  - Frequency: >1000Hz                            │
└─────────────────────────────────────────────────┘

3.2 OCS2 Core Module Configuration

a) Dynamics Model
   - Uses OCS2's Centroidal Dynamics model
   - State space definition:
     x = [h_com^T, q_b^T, q_j^T]^T ∈ R^24
     * h_com ∈ R^6: normalized centroidal momentum
     * q_b ∈ R^6: base pose (position + orientation)
     * q_j ∈ R^12: joint positions
   
   - Control input definition:
     u = [f_c^T, v_j^T]^T
     * f_c ∈ R^12: contact forces at four contact points
     * v_j ∈ R^12: joint velocities

b) Cost Function
   - Quadratic tracking cost
   - Includes state tracking error and control input cost
   - Parameters adjustable through configuration files

c) Constraints
   This project implements the following constraints in OCS2:
   
   - Friction Cone Constraint
     Ensures contact forces satisfy Coulomb friction law
   
   - Zero Motion at Stance Foot
     Guarantees no slipping at the support foot
   
   - Zero Force at Swing Foot
     No contact forces during swing phase
   
   - Swing Foot Trajectory Constraint
     Constrains swing foot to follow planned cubic spline trajectory
   
   - Zero Roll Motion of Foot
     Prevents lateral foot rotation

3.3 OCS2-ROS2 Interface Integration
   
   - Uses OCS2's ROS2 interface for communication
   - MRT (Model Reference Tracking) node provides real-time trajectory tracking
   - Supports dynamic updates of reference trajectories and gait commands
```

---

### 4. Key Implementation Details

**Key Points:**
- In-depth introduction to OCS2 API usage
- Reasoning behind technical choices
- Code-level implementation highlights

**Content Framework:**

```
4.1 Problem Construction

Constructing an optimal control problem in OCS2 requires implementing:

a) RobotInterface
   - Inherit from ocs2::RobotInterface base class
   - Implement HumanoidInterface class
   - Encapsulate all problem-related configurations and settings

b) Dynamics System Definition
   - Use ocs2::SystemDynamicsBase interface
   - This project uses automatic differentiation (CppAD)
   - Define state derivative: ẋ = f(x, u, t)

c) Cost Function Implementation
   - Inherit from ocs2::StateInputCost
   - Implement quadratic tracking cost
   - Support online adjustment of weight matrices

d) Constraint Definition
   - Implement multiple constraint types:
     * StateInputConstraint: state-input constraints
     * StateConstraint: state-only constraints
   - Provide first-order derivatives to accelerate solving

4.2 Gait Scheduling and Reference Management

a) Mode Switching Sequence
   - Use OCS2's ModeSchedule to manage contact state switching
   - Define foot contact modes:
     * Double Support
     * Left Stance
     * Right Stance
   
b) SwitchedModelReferenceManager
   - Inherit from ocs2::SwitchedModelReferenceManager
   - Manage reference trajectories for different modes
   - Handle smooth transitions during gait switching

c) Foot Trajectory Planning
   - Swing foot uses cubic spline interpolation
   - Configurable swing height, lift-off/touchdown velocities
   - Synchronized with NMPC horizon

4.3 Solver Configuration

This project primarily uses SQP solver with key parameters:

- Time discretization: dt = 0.015s (~67 nodes in 1.5s horizon)
- Iterations: 1 SQP iteration per MPC cycle (for real-time performance)
- Parallel threads: 6 threads for parallel computation
- QP subproblem solver: HPIPM (High-Performance Interior Point Method)
- Integrator type: RK2 (2nd-order Runge-Kutta)

Performance optimization strategies:
- Use warm start to improve convergence speed
- Cache auto-differentiation generated dynamics libraries
- Set reasonable convergence tolerance to balance accuracy and speed

4.4 Real-Time Performance Optimization

a) Compilation Optimization
   - Must use RelWithDebInfo or Release build level
   - Enable C++ compiler optimization flags
   
b) Code Generation and Caching
   - Pre-compile CppAD-generated dynamics function libraries
   - Avoid repeated compilation at runtime
   
c) Computational Resource Allocation
   - Set appropriate thread priorities
   - Utilize multi-core parallel computation
```

---

### 5. Advantages and Limitations of OCS2

**Key Points:**
- Objective evaluation of OCS2's advantages
- Honest discussion of limitations
- How this project leverages strengths and mitigates weaknesses

**Content Framework:**

```
5.1 Technical Advantages

a) Modularity and Extensibility
   - Clear interface design facilitates functionality extension
   - Rich foundational components for reuse
   - Supports custom dynamics models and constraints

b) High-Performance Real-Time Solving
   - Optimized for real-time control scenarios
   - Supports multi-threaded parallel computation
   - Provides multiple solver algorithm choices

c) Solid Theoretical Foundation
   - Based on mature optimal control theory
   - Provides convergence and stability guarantees
   - Validated by numerous academic papers

d) Active Community Support
   - Open source with continuous updates
   - Rich example code
   - Detailed documentation

5.2 Limitations

a) Steep Learning Curve
   - Requires deep understanding of optimal control theory
   - Relatively complex API design
   - High debugging difficulty

b) High Requirements on Modeling Accuracy
   - Needs accurate robot models
   - Sensitive to parameters
   - Inaccurate models lead to performance degradation

c) Computational Resource Demands
   - Real-time solving requires strong computational power
   - May be challenging for embedded platforms
   - Complex constraints increase computational burden

5.3 Project's Mitigation Strategies

- Use simplified centroidal dynamics model to reduce computational complexity
- Limit SQP iterations to ensure real-time performance
- Compensate modeling errors through WBC
- Fully utilize modern multi-core processor parallel capabilities
```

---

### 6. Experimental Results and Analysis

**Key Points:**
- Demonstrate OCS2's effectiveness in practical applications
- Provide quantitative and qualitative analysis
- Compare with other methods (if available)

**Content Framework:**

```
6.1 Simulation Environment Setup
   - Use MuJoCo physics engine
   - Humanoid robot model parameters
   - Test scenario design

6.2 Performance Metrics
   
   a) Real-Time Performance
      - MPC solving time statistics
      - CPU utilization
      - Module operation frequencies
   
   b) Control Accuracy
      - Trajectory tracking error
      - Base pose stability
      - Foot landing accuracy
   
   c) Robustness
      - Disturbance response capability
      - Parameter variation adaptability
      - Smoothness of gait transitions

6.3 Experimental Results
   - Provide simulation videos and charts
   - Analyze successful and failed cases
   - Discuss result validity

6.4 Comparison with Traditional Methods
   - Compare with PID control
   - Compare with heuristic methods
   - Highlight advantages of OCS2 approach
```

---

### 7. Conclusion and Future Work

**Key Points:**
- Summarize OCS2 application results in this project
- Propose future improvement directions
- Outlook on OCS2's development prospects

**Content Framework:**

```
7.1 Summary
   - OCS2 provides a powerful optimal control framework for humanoid robots
   - Successfully achieved real-time motion control
   - Validated effectiveness of optimization-based control methods

7.2 Future Improvement Directions
   
   a) Algorithm Level
      - Explore more efficient solving algorithms
      - Research adaptive horizon length
      - Improve constraint handling methods
   
   b) Application Extensions
      - Extend to more complex motion tasks
      - Combine with learning methods for parameter optimization
      - Adapt to physical robot platforms
   
   c) System Integration
      - Enhance integration of perception and decision-making
      - Improve environmental adaptability
      - Achieve multi-robot cooperative control

7.3 OCS2 Development Outlook
   - Expand to more general robotic platforms
   - Integrate with modern machine learning techniques
   - Simplify usage barriers, improve usability
```

---

## II. Writing Recommendations

### 1. Language Style
- Use accurate academic terminology
- Maintain objective, rigorous expression
- Appropriately use figures and tables for illustration
- Balance theoretical depth and readability

### 2. Content Organization
- Follow "overview-details-summary" structure
- Each section should be self-contained yet interconnected
- Provide overview before diving into details
- Define key concepts when first introduced

### 3. Technical Detail Management
- Avoid over-simplification leading to inaccuracy
- Avoid excessive technicality causing incomprehension
- Include key equations with textual explanations
- Selectively show code snippets (if journal permits)

### 4. Figure Usage
Recommended figure types:
- System architecture diagram
- Control flow diagram
- State space definition illustration
- Time series data plots (tracking performance)
- Comparison experiment bar charts
- Simulation result snapshots

### 5. References
Essential references to cite:
- OCS2 original papers and technical reports
- Centroidal dynamics papers
- SQP/DDP algorithm principle papers
- Legged robot control surveys
- Related work referenced by this project

---

## III. Core Concepts Summary

When writing about OCS2, ensure you understand and can clearly explain:

1. **Standard Form of Optimal Control Problems**
   - Cost function, dynamics constraints, boundary conditions, path constraints

2. **Hybrid Systems and Mode Switching**
   - Discrete events, continuous dynamics, mode sequences

3. **Numerical Solution Methods**
   - Direct vs indirect methods
   - Shooting methods, collocation methods
   - Differences and applicable scenarios for SQP, DDP, IPM

4. **Centroidal Dynamics Model**
   - Centroidal momentum, angular momentum
   - Contact force modeling
   - Relationship with full dynamics

5. **Real-Time MPC Implementation**
   - Warm start, horizon management
   - Computational budget allocation
   - Trade-off between real-time performance and optimality

6. **Constraint Handling Techniques**
   - Hard vs soft constraints
   - Barrier functions, slack variables
   - Handling constraint violations

---

## IV. Example Paragraphs

### Example 1: Introducing OCS2

> OCS2 (Optimal Control for Switched Systems) is an open-source optimal control toolbox specifically designed for hybrid dynamical systems, developed by the Robotic Systems Lab at ETH Zurich. This framework provides a complete optimal control solution for systems with contact switching characteristics, such as legged robots. The core advantage of OCS2 lies in its modular design philosophy, which decomposes complex optimal control problems into independent modules such as dynamics, cost functions, and constraints, facilitating user customization for specific applications. Additionally, OCS2 integrates multiple efficient solving algorithms, including Differential Dynamic Programming (DDP), Sequential Quadratic Programming (SQP), and Interior Point Method (IPM), enabling the solution of high-dimensional nonlinear optimization problems while meeting real-time requirements. These characteristics have led to the widespread adoption of OCS2 in fields such as mobile manipulation robots, humanoid robots, and quadruped robots.

### Example 2: Technical Details

> In this project, we construct the optimal control problem for the humanoid robot based on OCS2's centroidal dynamics model. The system state is 24-dimensional, consisting of 6-dimensional normalized centroidal momentum, 6-dimensional base pose, and 12-dimensional joint positions. The control input includes 12-dimensional contact forces at four contact points and 12-dimensional joint velocities. To handle contact switching between feet and the ground, we utilize OCS2's mode switching management mechanism, converting the contact sequence generated by the gait planner into a ModeSchedule object. For each mode, the SwitchedModelReferenceManager provides corresponding reference trajectories for different contact states. Particularly, for the swing foot, we implement a cubic spline-based trajectory planner that ensures appropriate velocities at lift-off and touchdown moments to avoid impacts. This trajectory is imposed through OCS2's state constraint interface, enabling the optimization solver to automatically satisfy foot trajectory requirements when generating motion plans.

### Example 3: Discussing Advantages and Limitations

> The OCS2 framework has clear advantages in real-time performance and theoretical rigor, but also faces some challenges in practical applications. First, OCS2 has high requirements for system modeling accuracy, and inaccurate dynamics models can lead to deviations between optimization results and actual execution. To address this, we added a Whole Body Control (WBC) layer to the control architecture, compensating for modeling errors by re-solving QP problems. Second, to ensure a 100Hz NMPC update rate, we had to limit SQP iterations to 1 per cycle, which may affect solution optimality. However, through reasonable warm-start strategies and parameter tuning, this setting still achieves satisfactory control performance in practice. Additionally, OCS2 has a steep learning curve, requiring developers to have a solid foundation in optimal control theory and deep understanding of the API. Nevertheless, once mastered, the flexibility and performance advantages it provides are worth the upfront investment.

---

## V. Checklist

Use this checklist for self-review after completing the draft:

- [ ] Is OCS2 clearly defined?
- [ ] Is the reason for choosing OCS2 explained?
- [ ] Is a complete mathematical problem description provided?
- [ ] Are state and control input specifically defined?
- [ ] Are all important constraints listed?
- [ ] Is the reasoning for solver algorithm choice explained?
- [ ] Is OCS2's interface with other modules shown?
- [ ] Is performance data provided to support claims?
- [ ] Are advantages and limitations discussed?
- [ ] Are all mathematical symbols clearly defined?
- [ ] Are figures and tables clear and helpful?
- [ ] Are references complete?

---

## VI. Additional Resources

### Recommended Reading
1. OCS2 Official Documentation: https://leggedrobotics.github.io/ocs2/
2. OCS2 GitHub Repository: https://github.com/leggedrobotics/ocs2
3. Related Papers:
   - "OCS2: An Optimal Control and Trajectory Optimization Toolbox"
   - Classic papers on centroidal dynamics
   - Survey papers on legged robot MPC

### Related Projects
- ANYmal robot control (OCS2 official example)
- MIT Cheetah
- Other OCS2-based open-source projects

### Community Resources
- ROS Discourse forums
- GitHub Issues and Discussions
- Technical reports from related research groups

---

## Conclusion

Writing about the OCS2 control framework requires finding a balance between theoretical depth and practical application. This guide provides a systematic framework and specific writing recommendations, but the final content organization should be adjusted based on your paper type, target audience, and specific research content.

The key is to help readers understand:
1. What OCS2 is and why it is useful
2. How OCS2 is applied in this project
3. What results were achieved and what lessons were learned

Best wishes for your writing!
