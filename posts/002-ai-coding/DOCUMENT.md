# AI 工具实战：用 AI 提升你的工作效率

**培训时长**：60 分钟 | **适合人群**：会写 SQL，没用过 AI 编程工具

---

## 第一部分：AI 正在改变我们的工作方式（5 分钟）

你有没有遇到过这样的情况：一份报告要写两个小时，一段复杂的 SQL 要调试半天，一份会议纪要整理完发现已经下班了。

这些事情不是因为你能力不够，而是因为过去这些工作只能靠你自己一个人完成。

现在不一样了。

AI 工具的出现，不是要替代你，而是让你在同样的时间里做更多的事——或者把同样的事做得更好。不管是你每天都在做的重复性工作，还是需要深度思考的复杂任务，AI 都能在不同程度上帮到你。

今天的培训分三个层次：

```
用 AI 对话  →  给 AI 装技能  →  用 AI 造工具
（今天就能做）  （稍微进阶）    （努力一下也能做到）
```

我们会先认识现在主流的 AI 工具都有哪些、各自适合做什么；然后介绍一个新标准——Agent Skills，它能让 AI 变得更专业；最后我会用一个真实项目，把今天讲到的工具全部串起来，让你看到这一套东西用起来是什么效果。

---

## 第二部分：AI 工具全景（20 分钟）

AI 工具现在非常多，容易让人不知道从哪里下手。这里按用途分成四类，帮你快速找到和自己工作最相关的那个。

---

### 对话类：Claude.ai / ChatGPT

**是什么**：你用自然语言提问，AI 直接给你答案。不需要安装任何东西，打开网页就能用。

**适合做什么**：
- 写报告初稿、整理会议纪要、润色邮件
- 解释一段你看不懂的复杂 SQL，或者帮你优化查询逻辑
- 把一堆散乱的数据整理成结构化的表格或结论

**对 SQL 用户的具体例子**：

> 把这段 SQL 粘贴进去，问它："这段查询在做什么？有没有性能问题？"
> 它不仅会解释逻辑，还会指出哪个 JOIN 可能导致全表扫描，并给出改写建议。

**推荐入口**：[claude.ai](https://claude.ai) / [chatgpt.com](https://chatgpt.com)

---

### 知识管理类：NotebookLM

**是什么**：Google 出品的 AI 知识库工具。你上传文档，它帮你提问、总结、提炼关键信息。

**适合做什么**：
- 上传一份 20 页的行业报告，直接问"核心风险有哪些"
- 把多份参考资料上传，让它综合提炼观点
- 生成音频摘要（Podcast 形式），通勤路上听

**对 SQL 用户的具体例子**：

> 把公司季度数据报告、行业分析文章一起上传，问它："我们的业务和行业整体趋势有什么差距？"
> 它会基于你上传的材料给出有依据的分析，而不是凭空生成。

**推荐入口**：[notebooklm.google.com](https://notebooklm.google.com)

---

### 创作类：Google AI Studio

**是什么**：Google 的 AI 开发平台，可以用来快速生成代码框架、原型页面，支持多模态输入（文字、图片、文件）。

**适合做什么**：
- 描述你想要的页面或工具，直接生成可运行的代码框架
- 上传截图或设计稿，让它生成对应的前端代码
- 快速搭建原型，验证想法

**对 SQL 用户的具体例子**：

> 告诉它："我想做一个展示 GDP 数据的网页，有折线图和数据表格"，它直接输出一个可以运行的 React 项目框架，你不需要知道 React 是什么。

**推荐入口**：[aistudio.google.com](https://aistudio.google.com)

---

### AI IDE 类：Claude Code / Cursor / GitHub Copilot

**是什么**：在代码编辑器里内置 AI 的工具。和前面几类不同，这类工具可以直接读取你的项目文件、运行代码、修改文件，是真正意义上的"AI 帮你写代码"。

| 工具 | 定位 | 适合谁 |
|---|---|---|
| **Claude Code** | 命令行 AI 编程助手，能操作文件、运行命令、安装 Agent Skills | 想深度使用 AI 辅助开发的人 |
| **Cursor** | AI 增强版 VS Code，界面友好 | 有一定代码基础，想提高开发效率 |
| **GitHub Copilot** | 代码补全为主，集成在各主流编辑器 | 已有开发习惯，想要 AI 辅助补全 |

**今天的 Agent Skills 演示和 AI Coding 实战 Demo 都会用到 Claude Code。**

---

## 第三部分：Agent Skills（10 分钟）

### 什么是 Agent Skills

你有没有想过：AI 已经很厉害了，为什么有时候还是答不好专业问题？

原因是 AI 本身是通用的，它不了解你的行业惯例、你公司的数据规范、或者某个专业领域的分析方法。**Agent Skills 解决的就是这个问题。**

Agent Skills 是一套开放标准，让你可以给 AI 安装"专业技能包"。每个 skill 就是一个文件夹，里面有一个 `SKILL.md` 文件，写清楚 AI 在这个场景下应该怎么思考、用什么方法、输出什么格式。AI 会在合适的时候自动加载它，也可以用 `/技能名` 手动调用。

一个类比：**Agent Skills 就像给 AI 考了一张专业资格证。** 装了统计分析 skill，它就懂得用 Z-score 判断异常值；装了 SQL 规范 skill，它就会按你公司的命名规范写查询。

### 生态现状

Agent Skills 由 Anthropic 于 2025 年 12 月发布为开放标准，目前：

- **已采用的平台**：OpenAI Codex、Cursor、GitHub、Microsoft、Figma、Atlassian
- **可用 skill 数量**：SkillsMP 上 96,000+，ClawHub 上 5,700+
- **去哪里找**：[agentskills.io](https://agentskills.io) / [skillsmp.com](https://skillsmp.com)

### Live Demo

**场景**：用 Claude Code 分析一份宏观经济 GDP.xlsx 数据

#### Before（安装 skill 之前）

在 Claude Code 中提问：

```
我有一份近 24 个月的 GDP 增速数据，上个月环比下降了 1.2%，
这是正常波动还是需要警觉？
```

结果：AI 给出的是泛泛的回答，没有具体的统计判断方法，也无法读取 xlsx 文件。

#### 安装 Skill

```bash
# 安装 xlsx 读取能力
/skill install xlsx

# 安装统计分析方法论
mkdir -p ~/.claude/skills/statistical-analysis
curl -o ~/.claude/skills/statistical-analysis/SKILL.md \
  https://raw.githubusercontent.com/anthropics/knowledge-work-plugins/main/data/skills/statistical-analysis/SKILL.md
```

#### After（安装 skill 之后）

同样的问题，Claude Code 现在会：

- 直接读取 `宏观经济GDP.xlsx` 文件
- 自动用 **Z-score 或 IQR 方法**判断 1.2% 是否为统计异常
- 同时报告均值和中位数（不只给一个数字）
- 区分"统计显著"和"业务显著"
- 给出可以直接运行的 Python 分析代码

**这就是 skill 的价值：同样的 AI，装了专业知识之后，给出的是专业级别的答案。**

---

## 第四部分：AI Coding 实战（15 分钟）

### 什么是 AI Coding

**AI Coding** 是指借助 AI 工具完成软件开发工作的方式——不管你会不会写代码，你都可以用自然语言描述你想要的东西，让 AI 来写实现。

其中有一种风格叫 **Vibe Coding**：不预先规划所有细节，而是凭直觉和感觉不断和 AI 对话、迭代，把一个模糊的想法一步步变成真实产品。

### 和 SQL 的类比

你每天写 SQL，其实已经在做类似的事了：

```
SQL：  告诉数据库"我要什么数据"，数据库帮你取出来
AI Coding：告诉 AI "我要什么功能"，AI 帮你写出来
```

区别只在于：SQL 的语法是固定的，AI Coding 用的是你的母语。

### Live Demo：宏观经济 GDP 分析 Web App

下面是这个项目的完整制作过程，你会看到前面讲到的工具是怎么配合使用的。

---

#### Step 1：用 NotebookLM 收集内容

首先在 NotebookLM 里上传多篇宏观经济解读文章，提问整理：

- "近期 GDP 数据的核心解读是什么？"
- "有哪些值得关注的趋势和风险？"

NotebookLM 基于上传的材料，生成结构化的分析内容，作为 Web App 的文字素材。

**用到了**：第二部分介绍的知识管理类工具 → NotebookLM

---

#### Step 2：用 Google AI Studio 生成 React 框架

把需求描述给 Google AI Studio：

> "我想做一个宏观经济 GDP 分析的展示网页，需要幻灯片式的翻页效果，包含数据图表和文字分析"

Google AI Studio 直接输出一套可以运行的 React 项目框架，把代码下载到本地。

**用到了**：第二部分介绍的创作类工具 → Google AI Studio

---

#### Step 3：用 Claude Code + Agent Skill 迭代项目

在本地项目里打开 Claude Code，先安装 react-best-practice skill：

```bash
/skill install react-best-practice
```

然后用自然语言驱动迭代：

> "把 Step 1 整理的 GDP 分析内容填入对应的幻灯片，数据部分接入 GDP.xlsx，图表用折线图展示趋势"

Claude Code 读取项目文件，结合 react-best-practice skill 的规范，自动修改代码、填充内容、调整样式。

**用到了**：第二部分介绍的 AI IDE → Claude Code，第三部分介绍的 Agent Skills → react-best-practice skill

---

#### Step 4：最终成品

*（此处展示宏观经济 GDP 分析 Web App 实际运行效果截图）*

---

**你刚才看到的 NotebookLM、Google AI Studio、Claude Code、Agent Skills，在这一个项目里全都用到了。**

这不是未来的技术，是现在就可以做的事。你不需要会写 React，不需要懂前端，你只需要知道自己想要什么，然后用语言告诉 AI。

---

## 第五部分：下一步（10 分钟）

### 三个行动清单

今天的培训结束之后，你可以按这个节奏开始：

**今天就能做**
- 打开 [claude.ai](https://claude.ai)，把下一份要写的报告交给 AI 来辅助起草
- 或者把一段让你头疼的复杂 SQL 粘贴进去，让它解释一遍

**本周可以试**
- 注册 [NotebookLM](https://notebooklm.google.com)，上传一份你经常要查阅的参考文档
- 试着问它几个你平时需要翻文档才能回答的问题

**进阶挑战**
- 下载并安装 [Claude Code](https://claude.ai/code)
- 从 [agentskills.io](https://agentskills.io) 或 [skillsmp.com](https://skillsmp.com) 找一个和你工作相关的 skill 装上试试

### 工具资源汇总

| 工具 | 链接 | 适合场景 |
|---|---|---|
| Claude.ai | [claude.ai](https://claude.ai) | 对话、写作、分析 |
| ChatGPT | [chatgpt.com](https://chatgpt.com) | 对话、写作、分析 |
| NotebookLM | [notebooklm.google.com](https://notebooklm.google.com) | 文档知识库 |
| Google AI Studio | [aistudio.google.com](https://aistudio.google.com) | 原型生成、代码框架 |
| Claude Code | [claude.ai/code](https://claude.ai/code) | AI IDE，安装 Agent Skills |
| Cursor | [cursor.com](https://cursor.com) | AI IDE，界面友好 |
| Agent Skills 生态 | [agentskills.io](https://agentskills.io) | 标准规范 |
| SkillsMP | [skillsmp.com](https://skillsmp.com) | 搜索和安装 skills |

### Q&A

有任何问题，欢迎现在提问。培训结束后如果想深入了解某个工具，也可以直接找我。

