# AI 工具实战：用 AI 提升工作效率

**培训时长**：60 分钟 | **适合人群**：会写 SQL，没用过 AI 编程工具

---

## 第一部分：本次培训的内容与结构（5 分钟）

大家多少都在用一些 AI 工具了，ChatGPT、Claude 之类的。今天想以一个项目为主线，介绍我在做这个项目过程中用到的工具，以及两个可能还没接触过的方向：**Agent Skills** 和 **AI Coding**。

内容分三块，递进来的：

```
AI 工具介绍  →  Agent Skills  →  AI Coding 实战
（项目里用了哪些）  （怎么让 AI 更专业）  （真实项目完整演示）
```

**第一块：AI 工具介绍**——以这次 AI 生成宏观经济分析报告的项目为背景，介绍项目里实际用到的工具，按用途分四类：对话类、知识管理类、原型生成类、AI IDE 类。

**第二块：Agent Skills**——Anthropic 在 2025 年底发布的开放标准，核心是解决"通用 AI 在专业场景下表现不够好"的问题。会做一个 Live Demo，用 Claude Code 装一个 skill，展示安装前后分析同一份宏观经济 GDP 数据的结果对比。

**第三块：AI Coding 实战**——把前两块的工具全串起来，展示从内容收集到报告制作的完整工作流。

---

## 第二部分：AI 工具介绍（5 分钟）

以项目里实际用到的工具为主，按用途分四类快速过一遍。

| 类型 | 代表工具 | 一句话定位 |
|---|---|---|
| **对话类** | Claude / ChatGPT / Gemini / DeepSeek / Kimi | 自然语言问答，写报告、整理纪要、分析数据的高频入口 |
| **知识管理类** | NotebookLM / ima.copilot | 基于上传的文档来回答，不凭空生成，有来源 |
| **原型生成类** | Google AI Studio / Bolt.new / v0.dev / Lovable | 描述需求直接生成可运行的应用，不需要动手写代码 |
| **AI IDE 类** | Claude Code / Cursor / Windsurf / GitHub Copilot | 直接读项目文件、运行代码、改文件，AI 真正帮你写代码 |

后面的 Demo 会用到其中的 NotebookLM、Google AI Studio 和 Claude Code——第三部分展开 Claude Code，第四部分展开三者的协作。

---

## 第三部分：Agent Skills（15 分钟）

### 背景

AI 通用能力很强，但在专业场景下有时候会答得不够准——它不了解你的行业惯例、数据规范，或者某个领域的标准分析方法。Agent Skills 就是针对这个问题的解法。

Agent Skills 是一套开放标准，本质是给 AI 安装"专业技能包"。每个 skill 是一个文件夹，里面一个 `SKILL.md`，写清楚 AI 在这个场景下该怎么思考、用什么方法、输出什么格式。AI 会在合适的时候自动加载，也可以用 `/技能名` 手动调用。

可以理解成：**装了统计分析 skill，AI 就会用 Z-score 判断异常值；装了 SQL 规范 skill，它就会按你们团队的命名规范写查询。**

### 生态现状

Anthropic 在 2025 年 12 月把它作为开放标准发布，现在 OpenAI Codex、Cursor、GitHub、Microsoft、Figma、Atlassian 都已经采用。Vercel 随后推出了配套的 [skills.sh](https://skills.sh)——一个开放的 skill 目录，可以搜索和安装各类 skill，支持 Claude Code、Cursor、GitHub Copilot 等主流 AI IDE，也是我自己常用的平台。

### Live Demo

**场景**：用 Claude Code 读取宏观经济 GDP 数据，分析 2020Q1—2025Q4 共 24 个季度的增速规律。

---

#### Before（未安装 Skill）

提示词：

```
分析 GDP现价当季值等_20260228_103456.xlsx 这些数据，然后在同级目录下生成报告 分析报告.md 文件
```

生成的报告结构不错——覆盖总量、产业结构、需求侧、价格水平、区域格局，逻辑清晰，关键事件（2020Q1 新冠冲击、2021Q1 低基数效应、2022Q2 上海封控）也标注到位。

**但专业性有明显短板**：

- 2020Q1 的 **-6.8%** 和 2021Q1 的 **+18.9%** 被直接放进走势图，没有识别为统计异常值，容易误导判断
- 价格分析只给出年度均值（如"2024 年平减指数约 99.3"），看不出通缩是在加深还是在收窄
- 区域分析只有规模排名，广东第一、江苏第二，但没有增速分化，不知道谁在加速、谁在减速
- 结论全是定性判断，"增速保持稳定"——稳定到什么程度？没有数据支撑

---

#### 安装 Skill

```bash
npx skills add https://github.com/anthropics/knowledge-work-plugins --skill statistical-analysis
```

---

#### After（安装 Skill 之后）

同样的提示词，安装 skill 后生成的报告：

**异常值处理**：
- 用 IQR 法自动识别统计极值：上界 6.45%、下界 3.65%
- **2021Q1（+18.9%）** 被标注为低基数效应造成的统计异常，**2020Q1（-6.8%）** 标注为疫情冲击极值
- 剔除两端异常值后，真实增速中枢约 **4.7%**，标准差收窄至 1.1 个百分点

**通缩量化**：
- 逐季拆解"名义增速 − 实际增速 = 价格效应"
- 明确标注通缩启动（2023Q2，价格拖累 -0.8%）、最深点（2025Q2，-1.3%）、边际收窄（2025Q4，-0.6%）
- 进一步细拆至三个产业：**第二产业是通缩重灾区**（2025 年均值 97.5），第三产业是缓冲垫

**风险信号追踪**：
- 建筑业增速时序：5.8%（2024Q1）→ 3.1%（2025Q1）→ **-0.6%（2025Q2）→ -2.3%（Q3）→ -2.5%（Q4）**，首次转负到持续恶化的完整路径一目了然
- 结论附带信心程度标注（高 / 中），坦诚区分确定性判断和不确定性判断

---

两份报告生成后，再让 Claude Code 以宏观经济分析专家视角对两份报告做对比评价：Before **7.63 / 10**，After **8.65 / 10**，核心差距集中在"分析方法严谨性"（6.5 vs 9.5 分）。这个评分本身，也是 Claude Code 做的。

同样的 AI，同样的数据——装了统计分析 skill，分析从"描述性概览"变成了"统计驱动的经济诊断"。

个人觉得 Agent Skills 很可能会成为新时代的 App——Agent 是新 OS，Skill 是装在上面的专业能力包，写一次、到处用、社区共享，和当年 App Store 的逻辑如出一辙。不需要懂 Prompt 工程，装个 Skill 就能让 AI 在特定领域直接变专业。

---

## 第四部分：AI Coding 实战（25 分钟）

### AI Coding 是什么

**AI Coding** 是用 AI 工具完成软件开发工作的方式——不管会不会写代码，都可以用自然语言描述想要的东西，让 AI 来实现。

### Live Demo：AI 生成宏观经济分析报告

用到了今天讲的所有工具。

---

#### Step 1：Claude Code 提炼报告编写指南

在做任何内容之前，先用 Claude Code 读取历史上积累的 7 篇宏观经济分析报告，让它从中提炼总结出一份指南文档——宏观经济分析报告应该包含哪些模块、每个模块的写作逻辑是什么、有哪些关键指标必须覆盖。

这份指南文档后续会作为整个项目的内容基准，确保 AI 生成的内容符合专业的报告标准。

---

#### Step 2：NotebookLM 收集内容

这里选择 NotebookLM 而不是直接让 Claude Code 读取本地文件，是因为参考文章数量非常多——这个项目里一共有 105 篇，来自国家统计局、微信公众号等渠道的当季解读文章，文档体量也很大。如果用本地文件系统，Claude Code 需要逐一处理这些文件，还要做额外的索引和检索工作，反而麻烦。NotebookLM 原生就是为大量文档的联合检索和问答设计的，上传进去直接用，省去了很多额外工作。

在 NotebookLM 里上传这 105 篇宏观经济解读文章，提问时把 Step 1 生成的编写指南作为提示词的一部分带进去，让 NotebookLM 按照指南要求的模块结构和写作逻辑来产出内容：

> "根据以下报告编写指南，从上传的文章中整理出符合要求的报告章节内容：[粘贴 Step 1 的指南]"

这样产出的内容不只是信息摘要，而是已经按照专业报告的格式和逻辑组织好的章节素材，可以直接用于后续的 Web App。

---

#### Step 3：Google AI Studio 生成 React 框架

初始设计阶段选 Google AI Studio 有个很实际的原因：**它免费，而且用的是 Gemini 2.0 Pro，做前端效果不错。**

在做初始框架的时候，风格和样式往往要反复试——换布局、换配色、换交互方式，这个阶段如果用 Claude Code 或 Codex 来回生成，token 额度消耗会很快。用 AI Studio 先把整体框架和视觉风格定下来，再交给 Claude Code 做内容填充和迭代，是更省成本的做法。

把需求丢给 Google AI Studio：

> "做一个宏观经济 GDP 分析的展示网页，幻灯片式翻页，包含数据图表和文字分析"

它直接输出一套可以运行的 React 项目框架，下载到本地。

---

#### Step 4：Claude Code + Agent Skill 迭代

AI Studio 下载下来的项目是 React 框架，所以在 Claude Code 里先装上 react-best-practice skill——这个 skill 包含了 React 项目的最佳实践规范，让 coding agent 在迭代修改时能更准确地理解项目结构，避免改出问题。

```bash
npx skills add react-best-practice
```

然后用自然语言驱动迭代：

> "把 Step 2 整理的 GDP 分析内容填入对应幻灯片，数据部分接入 GDP.xlsx，图表用折线图展示趋势"

Claude Code 读取项目文件，结合 skill 里的规范，自动改代码、填内容、调样式。

---

#### Step 5：最终成品

*（此处展示 Web App 实际运行效果截图）*

---

NotebookLM、Google AI Studio、Claude Code、Agent Skills——今天讲的东西，在这一个项目里全用上了。

---

## 第五部分：下一步（10 分钟，含 Q&A）

### 可以从哪里开始

**今天**
- 把下一份要写的报告，试着用 Claude 或 ChatGPT 辅助起草
- 或者找一段复杂的 SQL 丢进去，看看它能给出什么

**本周**
- 在 NotebookLM 里上传一份经常要查阅的参考文档，试着直接问问题

**进阶**
- 装一下 Claude Code，去 [skills.sh](https://skills.sh) 找个相关的 skill 试试

### 工具资源汇总

| 工具 | 链接 | 场景 |
|---|---|---|
| Claude | [claude.ai](https://claude.ai) | 对话、写作、分析 |
| ChatGPT | [chatgpt.com](https://chatgpt.com) | 对话、写作、分析 |
| DeepSeek | [chat.deepseek.com](https://chat.deepseek.com) | 对话、推理、免费 |
| Kimi | [kimi.moonshot.cn](https://kimi.moonshot.cn) | 长文档处理 |
| NotebookLM | [notebooklm.google.com](https://notebooklm.google.com) | 文档知识库 |
| ima.copilot | [ima.qq.com](https://ima.qq.com) | 知识库、微信生态 |
| Google AI Studio | [aistudio.google.com](https://aistudio.google.com) | 原型生成 |
| Bolt.new | [bolt.new](https://bolt.new) | 快速全栈原型 |
| v0.dev | [v0.dev](https://v0.dev) | React UI 生成 |
| Claude Code | [claude.ai/code](https://claude.ai/code) | AI IDE + Agent Skills |
| Cursor | [cursor.com](https://cursor.com) | AI IDE |
| Windsurf | [codeium.com/windsurf](https://codeium.com/windsurf) | AI IDE |
| Skills.sh | [skills.sh](https://skills.sh) | 搜索安装 skills |

### Q&A

有问题现在可以聊，或者培训后想深入某个工具也欢迎找我。
