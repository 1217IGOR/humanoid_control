# Documentation / 文档

本文件夹包含人形机器人控制项目的文档资料，特别是关于OCS2控制框架的论文写作指南。

This folder contains documentation for the humanoid control project, particularly guides for writing about the OCS2 control framework in academic papers.

---

## 📚 文档清单 / Document List

### 1. OCS2框架论文写作指南 (中文)
**文件名:** `OCS2_Framework_Paper_Guide.md`

**内容概要：**
- 完整的7部分论文结构框架
- 从理论到实践的详细写作指导
- 包含示例段落和常见问题解答
- 适合深入阅读和参考

**适用对象：**
- 准备撰写关于OCS2框架的中文论文
- 需要详细写作指导和理论支持
- 首次使用OCS2撰写学术论文

**阅读时间：** 约30-45分钟

---

### 2. OCS2 Framework Paper Writing Guide (English)
**Filename:** `OCS2_Framework_Paper_Guide_EN.md`

**Content Overview:**
- Complete 7-section paper structure framework
- Detailed writing guidance from theory to practice
- Includes example paragraphs and Q&A
- Suitable for in-depth reading and reference

**Target Audience:**
- Preparing to write papers about OCS2 framework in English
- Need detailed writing guidance and theoretical support
- First-time writing academic papers using OCS2

**Reading Time:** ~30-45 minutes

---

### 3. OCS2快速参考 / Quick Reference (双语 / Bilingual)
**文件名 / Filename:** `OCS2_Quick_Reference.md`

**内容概要 / Content Overview:**
- 核心概念速查表
- 本项目的OCS2配置参数
- 公式参考和术语对照
- 三步写作流程
- 最后检查清单

**适用对象 / Target Audience:**
- 需要快速查阅关键信息
- 已经阅读过详细指南，需要快速回顾
- 论文写作过程中的即时参考

**阅读时间 / Reading Time:** 约10-15分钟 / ~10-15 minutes

---

## 🎯 如何使用这些文档 / How to Use These Documents

### 场景1：第一次撰写OCS2相关论文
**推荐路径：**
1. 先阅读 `OCS2_Framework_Paper_Guide.md`（或英文版）
2. 理解完整的结构框架
3. 根据指南开始起草论文
4. 写作过程中参考 `OCS2_Quick_Reference.md`

### Scenario 1: First Time Writing OCS2 Papers
**Recommended Path:**
1. Read `OCS2_Framework_Paper_Guide_EN.md` (or Chinese version)
2. Understand the complete structure
3. Start drafting based on the guide
4. Reference `OCS2_Quick_Reference.md` during writing

---

### 场景2：已有草稿，需要改进
**推荐路径：**
1. 使用 `OCS2_Quick_Reference.md` 中的检查清单
2. 对照详细指南检查是否遗漏重要内容
3. 参考示例段落改进写作质量

### Scenario 2: Have Draft, Need Improvement
**Recommended Path:**
1. Use checklist in `OCS2_Quick_Reference.md`
2. Check against detailed guide for missing content
3. Reference example paragraphs to improve quality

---

### 场景3：快速查阅技术参数
**推荐路径：**
1. 直接查阅 `OCS2_Quick_Reference.md`
2. 找到对应的参数表格或公式
3. 必要时查阅详细指南了解背景

### Scenario 3: Quick Technical Reference
**Recommended Path:**
1. Go directly to `OCS2_Quick_Reference.md`
2. Find relevant parameter tables or formulas
3. Check detailed guide for context if needed

---

## 📖 论文结构框架概览 / Paper Structure Overview

所有文档都基于以下7部分结构框架：

All documents are based on the following 7-section structure:

1. **OCS2控制框架概述** / Introduction to OCS2
   - 背景、理念、应用 / Background, philosophy, applications

2. **OCS2的理论基础** / Theoretical Foundation
   - 数学描述、算法 / Math formulation, algorithms

3. **OCS2在人形机器人控制中的应用架构** / Application Architecture
   - 系统集成、模块关系 / System integration, module relationships

4. **关键技术实现细节** / Key Implementation Details
   - API使用、技术选择 / API usage, technical choices

5. **OCS2的优势与局限** / Advantages and Limitations
   - 客观评价 / Objective evaluation

6. **实验结果与分析** / Experimental Results and Analysis
   - 性能数据、对比 / Performance data, comparisons

7. **总结与展望** / Conclusion and Future Work
   - 成果、未来方向 / Achievements, future directions

---

## 🔑 本项目中OCS2的关键配置 / Key OCS2 Configuration in This Project

### 状态空间 / State Space
```
x = [h_com, q_b, q_j]^T ∈ R^24
- h_com ∈ R^6: 归一化质心动量 / normalized centroidal momentum
- q_b ∈ R^6: 基座位姿 / base pose
- q_j ∈ R^12: 关节位置 / joint positions
```

### 控制输入 / Control Input
```
u = [f_c, v_j]^T ∈ R^24
- f_c ∈ R^12: 接触力 / contact forces (4 points)
- v_j ∈ R^12: 关节速度 / joint velocities
```

### 求解器配置 / Solver Configuration
- **算法 / Algorithm:** SQP (Sequential Quadratic Programming)
- **频率 / Frequency:** 100Hz
- **时域 / Horizon:** 1.5s
- **离散化 / Discretization:** dt = 0.015s (~67 nodes)
- **迭代 / Iterations:** 1 per cycle
- **线程 / Threads:** 6 parallel

---

## 💡 写作技巧提示 / Writing Tips

### 中文论文 / Chinese Papers
1. ✅ 使用准确的专业术语
2. ✅ 理论与实践相结合
3. ✅ 公式配合文字解释
4. ✅ 客观讨论优缺点
5. ❌ 避免成为使用手册
6. ❌ 避免过度技术化

### English Papers
1. ✅ Use accurate technical terminology
2. ✅ Combine theory with practice
3. ✅ Explain equations with text
4. ✅ Objectively discuss pros and cons
5. ❌ Avoid becoming a user manual
6. ❌ Avoid excessive technicality

---

## 📚 推荐阅读顺序 / Recommended Reading Order

### 对于中文读者 / For Chinese Readers
```
1. OCS2_Quick_Reference.md (第一部分：中文速查)
   ↓ (了解概览)
2. OCS2_Framework_Paper_Guide.md (完整阅读)
   ↓ (深入理解)
3. OCS2_Quick_Reference.md (在写作时作为参考)
```

### For English Readers
```
1. OCS2_Quick_Reference.md (Section 2: English Quick Reference)
   ↓ (Get overview)
2. OCS2_Framework_Paper_Guide_EN.md (Complete reading)
   ↓ (Deep understanding)
3. OCS2_Quick_Reference.md (Use as reference during writing)
```

---

## 🔗 相关资源链接 / Related Resources

### OCS2官方资源 / Official OCS2 Resources
- 📖 文档 / Documentation: https://leggedrobotics.github.io/ocs2/
- 💻 代码库 / Repository: https://github.com/leggedrobotics/ocs2
- 🔧 ROS2版本 / ROS2 Port: https://github.com/legubiao/ocs2_ros2

### 本项目资源 / This Project Resources
- 📄 主README / Main README: `../README.md`
- ⚙️ 配置文件 / Config: `../humanoid_interface/config/mpc/task.info`
- 💻 接口代码 / Interface: `../humanoid_interface/include/humanoid_interface/HumanoidInterface.h`

---

## ❓ 常见问题 / FAQ

### Q1: 我应该先看哪个文档？
**A:** 如果是第一次写作，建议先看 `OCS2_Framework_Paper_Guide.md`；如果需要快速查阅，直接看 `OCS2_Quick_Reference.md`。

### Q1: Which document should I read first?
**A:** If writing for the first time, start with `OCS2_Framework_Paper_Guide_EN.md`; if need quick reference, go directly to `OCS2_Quick_Reference.md`.

---

### Q2: 这些文档适合什么类型的论文？
**A:** 适合任何需要介绍OCS2控制框架的学术论文，包括：
- 硕士/博士学位论文
- 会议论文（如ICRA, IROS, Humanoids等）
- 期刊论文（如T-RO, RAL等）
- 技术报告

### Q2: What types of papers are these documents suitable for?
**A:** Suitable for any academic papers introducing OCS2 framework, including:
- Master's/PhD theses
- Conference papers (e.g., ICRA, IROS, Humanoids)
- Journal papers (e.g., T-RO, RAL)
- Technical reports

---

### Q3: 文档中的参数是否适用于所有OCS2项目？
**A:** 不是。文档中的具体参数（如状态维度、频率等）是基于本humanoid_control项目的。其他项目使用OCS2时，参数会有所不同。但写作框架和方法是通用的。

### Q3: Are the parameters in the documents applicable to all OCS2 projects?
**A:** No. Specific parameters (like state dimensions, frequencies) are based on this humanoid_control project. Other OCS2 projects will have different parameters. However, the writing framework and methods are universal.

---

## 📝 反馈与改进 / Feedback and Improvement

如果您在使用这些文档时有任何问题、建议或发现错误，欢迎：
- 提交GitHub Issue
- 提交Pull Request
- 联系项目维护者

If you have any questions, suggestions, or find errors while using these documents:
- Submit a GitHub Issue
- Submit a Pull Request
- Contact project maintainers

---

## 📄 许可证 / License

这些文档遵循与主项目相同的BSD-3许可证。

These documents follow the same BSD-3 license as the main project.

---

*最后更新 / Last Updated: 2025-12-10*
*创建者 / Created by: GitHub Copilot for 1217IGOR/humanoid_control*
