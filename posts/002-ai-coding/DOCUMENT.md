# AI 工具实战：用 AI 提升工作效率

**培训时长**：60 分钟 | **适合人群**：会写 SQL，没用过 AI 编程工具

---

## 第一部分：本次培训的内容与结构（5 分钟）

大家多少都在用一些 AI 工具了，ChatGPT、Claude 之类的。今天想以一个真实项目为主线，介绍我在做这个项目过程中用到的工具，以及两个可能还没接触过的方向：**Agent Skills** 和 **AI Coding**。

内容分三块，递进来的：

```
AI 工具介绍  →  Agent Skills  →  AI Coding 实战
（项目里用了哪些）  （怎么让 AI 更专业）  （真实项目完整演示）
```

**第一块：AI 工具介绍**——以最后那个宏观经济报告 Web App 为背景，介绍项目里实际用到的工具，按用途分四类：对话类、知识管理类、原型生成类、AI IDE 类。

**第二块：Agent Skills**——Anthropic 在 2025 年底发布的开放标准，核心是解决"通用 AI 在专业场景下表现不够好"的问题。会做一个 Live Demo，用 Claude Code 装两个 skill，现场分析一份宏观经济 GDP 数据。

**第三块：AI Coding 实战**——用一个真实项目把前两块的工具全串起来，展示从内容收集到产品上线的完整工作流。

---

## 第二部分：AI 工具全景（20 分钟）

AI 工具种类非常多，这里不打算做一个大而全的盘点。我聚焦在最后那个宏观经济报告 Web App 项目里实际用到的工具，按用途分四类来介绍——正好也是第四部分会串起来演示的完整工作流。

---

### 对话类

最基础的一类，自然语言提问，直接给答案，开网页就能用。写报告、整理纪要、润色邮件都是高频场景；拿来解释或优化 SQL 也非常顺手——把查询粘进去，问它有没有性能问题，它会指出哪个 JOIN 可能导致全表扫描，并给出改写建议。

| 工具 | 特点 | 入口 |
|---|---|---|
| **Claude** | Anthropic 出品，长文本和分析能力强 | [claude.ai](https://claude.ai) |
| **ChatGPT** | OpenAI 出品，生态最成熟，插件丰富 | [chatgpt.com](https://chatgpt.com) |
| **Gemini** | Google 出品，与 Google Workspace 深度集成 | [gemini.google.com](https://gemini.google.com) |
| **DeepSeek** | 国产，推理能力强，中文理解好，免费 | [chat.deepseek.com](https://chat.deepseek.com) |
| **Kimi** | 月之暗面出品，长文档处理能力强，支持上传文件 | [kimi.moonshot.cn](https://kimi.moonshot.cn) |

---

### 知识管理类

和对话类的区别在于：它是基于你上传的材料来回答，不会凭空生成内容。把公司报告、行业文章一起上传，问"我们的业务和行业趋势有什么差距"，它给的是有来源的分析。

| 工具 | 特点 | 入口 |
|---|---|---|
| **NotebookLM** | Google 出品，支持多文档联合问答，可生成播客摘要 | [notebooklm.google.com](https://notebooklm.google.com) |
| **ima.copilot** | 腾讯出品，打通微信公众号生态，支持共享知识库协作，适合国内用户 | [ima.qq.com](https://ima.qq.com) |

---

### 原型生成类

说清楚想要什么，AI 直接生成可以跑的应用，不需要动手写代码。比如告诉它"做一个展示 GDP 数据的网页，有折线图和数据表格"，它输出一个完整的 React 项目——不用知道 React 是什么。

| 工具 | 特点 | 入口 |
|---|---|---|
| **Google AI Studio** | 多模态输入，可上传文件或截图直接生成代码 | [aistudio.google.com](https://aistudio.google.com) |
| **Bolt.new** | 全栈应用生成，浏览器内直接运行，适合快速验证想法 | [bolt.new](https://bolt.new) |
| **v0.dev** | Vercel 出品，专注 React UI 组件生成，质量高 | [v0.dev](https://v0.dev) |
| **Lovable** | 对非技术背景友好，支持 GitHub 同步 | [lovable.dev](https://lovable.dev) |

---

### AI IDE 类

前面几类基本是"问答"模式，这类不一样——它可以直接读你的项目文件、运行代码、改文件，是真正意义上的 AI 在帮你写代码。

| 工具 | 定位 | 适合谁 |
|---|---|---|
| **Claude Code** | 命令行 AI 编程助手，能操作文件、运行命令、安装 Agent Skills | 想深度使用 AI 辅助开发的人 |
| **Cursor** | AI 增强版 VS Code，多文件编辑能力强，界面友好 | 有一定代码基础，想提高开发效率 |
| **Windsurf** | 价格更低，具备 Agentic 能力 | 想用 AI IDE 但预算有限 |
| **GitHub Copilot** | 代码补全为主，集成在各主流编辑器 | 已有开发习惯，想要 AI 辅助补全 |

后面的 Agent Skills Demo 和 AI Coding 实战都会用 Claude Code。

---

## 第三部分：Agent Skills（10 分钟）

### 背景

AI 通用能力很强，但在专业场景下有时候会答得不够准——它不了解你的行业惯例、数据规范，或者某个领域的标准分析方法。Agent Skills 就是针对这个问题的解法。

Agent Skills 是一套开放标准，本质是给 AI 安装"专业技能包"。每个 skill 是一个文件夹，里面一个 `SKILL.md`，写清楚 AI 在这个场景下该怎么思考、用什么方法、输出什么格式。AI 会在合适的时候自动加载，也可以用 `/技能名` 手动调用。

可以理解成：**装了统计分析 skill，AI 就会用 Z-score 判断异常值；装了 SQL 规范 skill，它就会按你们团队的命名规范写查询。**

### 生态现状

Anthropic 在 2025 年 12 月把它作为开放标准发布，现在 OpenAI Codex、Cursor、GitHub、Microsoft、Figma、Atlassian 都已经采用。SkillsMP 上有 96,000+ 个可用 skill，去 [agentskills.io](https://agentskills.io) 或 [skillsmp.com](https://skillsmp.com) 都能找到。

### Live Demo

**场景**：用 Claude Code 分析宏观经济 GDP.xlsx

#### Before

在 Claude Code 里问：

```
我有一份近 24 个月的 GDP 增速数据，上个月环比下降了 1.2%，
这是正常波动还是需要警觉？
```

结果是泛泛的回答，没有具体的统计判断方法，xlsx 文件也读不了。

#### 安装 Skill

```bash
# 安装 xlsx 读取能力
/skill install xlsx

# 安装统计分析方法论
mkdir -p ~/.claude/skills/statistical-analysis
curl -o ~/.claude/skills/statistical-analysis/SKILL.md \
  https://raw.githubusercontent.com/anthropics/knowledge-work-plugins/main/data/skills/statistical-analysis/SKILL.md
```

#### After

同样的问题，现在 Claude Code 会：

- 直接读取 `宏观经济GDP.xlsx`
- 用 Z-score 或 IQR 方法判断 1.2% 是否为统计异常
- 同时给出均值和中位数
- 区分"统计显著"和"业务显著"
- 给出可以直接运行的 Python 分析代码

同样的 AI，装了专业知识之后，给出的是专业级别的答案。

个人觉得 Agent Skills 很可能会成为新时代的 App——Agent 是新 OS，Skill 是装在上面的专业能力包，写一次、到处用、社区共享，和当年 App Store 的逻辑如出一辙。不需要懂 Prompt 工程，装个 Skill 就能让 AI 在特定领域直接变专业。

---

## 第四部分：AI Coding 实战（15 分钟）

### AI Coding 是什么

**AI Coding** 是用 AI 工具完成软件开发工作的方式——不管会不会写代码，都可以用自然语言描述想要的东西，让 AI 来实现。

### Live Demo：宏观经济 GDP 分析 Web App

这是一个真实项目，用到了今天讲的所有工具。

---

#### Step 1：Claude Code 提炼报告编写指南

在做任何内容之前，先用 Claude Code 读取历史上积累的宏观经济分析报告，让它从中提炼总结出一份指南文档——宏观经济分析报告应该包含哪些模块、每个模块的写作逻辑是什么、有哪些关键指标必须覆盖。

这份指南文档后续会作为整个项目的内容基准，确保 AI 生成的内容符合专业的报告标准。

---

#### Step 2：NotebookLM 收集内容

这里选择 NotebookLM 而不是直接让 Claude Code 读取本地文件，是因为参考文章数量非常多——这个项目里一共有 105 篇，文档体量也很大。如果用本地文件系统，Claude Code 需要逐一处理这些文件，还要做额外的索引和检索工作，反而麻烦。NotebookLM 原生就是为大量文档的联合检索和问答设计的，上传进去直接用，省去了很多额外工作。

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
/skill install react-best-practice
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

## 第五部分：下一步（10 分钟）

### 可以从哪里开始

**今天**
- 把下一份要写的报告，试着用 Claude 或 ChatGPT 辅助起草
- 或者找一段复杂的 SQL 丢进去，看看它能给出什么

**本周**
- 在 NotebookLM 里上传一份经常要查阅的参考文档，试着直接问问题

**进阶**
- 装一下 Claude Code，去 [skillsmp.com](https://skillsmp.com) 找个相关的 skill 试试

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
| SkillsMP | [skillsmp.com](https://skillsmp.com) | 搜索安装 skills |
| Agent Skills 规范 | [agentskills.io](https://agentskills.io) | 标准文档 |

### Q&A

有问题现在可以聊，或者培训后想深入某个工具也欢迎找我。
