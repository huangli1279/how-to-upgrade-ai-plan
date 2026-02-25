# 工作交接文档

**项目**：AI 工具实战培训文档
**目录**：`posts/002-ai-coding/`
**交接日期**：2026-02-25

---

## 项目背景

这是一份面向公司内部同事的 AI 工具培训教程，受众是会写 SQL 但没有使用过 AI 编程工具的同事。培训时长约 60 分钟，包含 PPT 讲解和 Live Demo。

培训的核心主线是：以一个真实项目（宏观经济 GDP 分析 Web App）为贯穿，介绍项目过程中用到的 AI 工具，并顺带讲解 Agent Skills 和 AI Coding 两个方向。

---

## 目录结构

```
posts/002-ai-coding/
├── FRAMEWORK.md     # 培训框架大纲（已定稿）
├── DOCUMENT.md      # 培训正文文档（主要工作产出）
├── handoff.md       # 本文件
└── assets/          # 截图等素材（待补充）
```

---

## 当前进度

### 已完成
- [x] 培训框架设计（见 `FRAMEWORK.md`）
- [x] 全文五个部分正文初稿（见 `DOCUMENT.md`）
  - 第一部分：培训结构说明
  - 第二部分：AI 工具介绍（四类工具 + 工具对比表）
  - 第三部分：Agent Skills 概念 + Live Demo 脚本
  - 第四部分：AI Coding 实战 Demo 工作流（五步）
  - 第五部分：行动清单 + 工具资源汇总

### 待完成
- [ ] `assets/` 目录下补充 Demo 截图
  - Agent Skills Before/After 对比截图
  - 宏观经济 GDP 分析 Web App 成品截图（对应 Step 5）
- [ ] 第四部分 Step 5 目前是占位符，需替换为实际截图和成品说明
- [ ] 根据实际培训演练结果，调整各部分的时间分配
- [ ] 如有需要，制作配套 PPT 或演示幻灯片

---

## 培训内容结构

```
第一部分（5 分钟）   培训结构说明
第二部分（20 分钟）  AI 工具介绍：对话类 / 知识管理类 / 原型生成类 / AI IDE 类
第三部分（10 分钟）  Agent Skills：概念 + Live Demo（Claude Code + GDP.xlsx）
第四部分（15 分钟）  AI Coding 实战：宏观经济 Web App 完整工作流
第五部分（10 分钟）  行动清单 + Q&A
```

---

## Live Demo 说明

### Demo 1：Agent Skills（第三部分）

**所需文件**：`宏观经济GDP.xlsx`（需提前准备好放在本地）

**演示步骤**：
1. 先不装 skill，在 Claude Code 里问 GDP 数据分析问题，展示泛泛的回答
2. 执行安装命令：
   ```bash
   /skill install xlsx

   mkdir -p ~/.claude/skills/statistical-analysis
   curl -o ~/.claude/skills/statistical-analysis/SKILL.md \
     https://raw.githubusercontent.com/anthropics/knowledge-work-plugins/main/data/skills/statistical-analysis/SKILL.md
   ```
3. 同样的问题再问一遍，展示有统计方法支撑的专业回答

**注意**：建议提前在演示机器上安装好 skill，现场只展示安装命令，用已装好的版本演示 After 效果，避免网络问题导致翻车。

---

### Demo 2：AI Coding 实战（第四部分）

**工作流五步**：

| 步骤 | 工具 | 产出 |
|---|---|---|
| Step 1 | Claude Code | 从历史宏观经济分析报告中提炼出报告编写指南 |
| Step 2 | NotebookLM | 基于指南提示词，从 105 篇参考文章中产出章节素材 |
| Step 3 | Google AI Studio | 生成幻灯片式 React Web 项目框架（免费，风格迭代成本低） |
| Step 4 | Claude Code + react-best-practice skill | 填充内容、接入数据、迭代样式 |
| Step 5 | — | 成品展示 |

**演示建议**：第四部分不建议现场从头还原全流程（耗时太长），建议提前录制或截图，现场以"回放 + 讲解"的方式呈现，重点展示 Step 1 和 Step 4 的 Claude Code 操作。

---

## 关键设计决策记录

| 决策 | 原因 |
|---|---|
| Step 2 用 NotebookLM 而非本地文件系统 | 参考文章共 105 篇，体量大，NotebookLM 原生支持大量文档联合检索，省去额外索引工作 |
| Step 3 用 Google AI Studio 而非 Claude Code 生成框架 | AI Studio 免费，初始设计阶段需要反复试样式，用 Claude Code 会快速消耗 token 额度 |
| Step 4 安装 react-best-practice skill | AI Studio 产出的是 React 项目，装此 skill 帮助 Claude Code 更准确理解项目结构，减少迭代出错 |
| 第二部分工具范围聚焦在项目实际用到的 | AI 工具种类太多，做大而全的盘点没有意义，以项目为背景聚焦更有说服力 |

---

## 联系方式

有问题或需要进一步了解背景，找原作者。
