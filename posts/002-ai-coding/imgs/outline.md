---
type: mixed
density: per-section
style: crayon-flat (蜡笔粗线+纯色平涂，无渐变，无小新人物)
image_count: 13
output_dir: posts/002-ai-coding/imgs/
---

## Illustration 01

**Position**: ## 01 做这份报告用到的工具 / 章节开头
**Purpose**: 直观展示4类AI工具的定位与分工关系
**Visual Content**: 4个工具类别（对话类/知识管理类/原型生成类/AI IDE类）的框架图，各类标注代表工具
**Type**: framework
**Filename**: 01-framework-tool-landscape.png

## Illustration 02

**Position**: ### 为什么需要它 / 章节开头
**Purpose**: 可视化"通用AI遇到专业场景的能力缺口"这一核心痛点
**Visual Content**: 左侧通用AI与右侧专业场景之间的鸿沟，中间用Skill桥接
**Type**: infographic
**Filename**: 02-infographic-skill-gap.png

## Illustration 03

**Position**: ### 生态现状 / 章节开头
**Purpose**: 展示Agent Skills生态的发展态势和主要参与者
**Visual Content**: Anthropic发布 → OpenAI/Cursor/GitHub/Microsoft接入 的生态链，skills.sh平台居中
**Type**: timeline
**Filename**: 03-timeline-skill-ecosystem.png

## Illustration 04

**Position**: #### 测试数据集 / 章节开头
**Purpose**: 可视化测试数据集的规模和维度结构
**Visual Content**: GDP数据集：24个季度 × 74个指标，覆盖名义GDP/三大产业/平减指数/需求侧/31省
**Type**: infographic
**Filename**: 04-infographic-gdp-dataset.png

## Illustration 05

**Position**: #### 安装 Skill 之前 / 章节开头
**Purpose**: 展示未安装Skill时分析报告的4个专业性缺陷
**Visual Content**: 4个问题点：-6.8%/-18.9%未识别异常、年度均值遮蔽趋势、区域只有排名无增速、结论无数据支撑
**Type**: infographic
**Filename**: 05-infographic-before-skill.png

## Illustration 06

**Position**: #### 安装之后 / 章节开头
**Purpose**: 展示安装统计分析Skill后报告的质量提升
**Visual Content**: 3大改进：IQR异常值检测（上界6.45%/下界3.65%）、通缩逐季量化（2025Q2最深-1.3%）、风险路径追踪（建筑业-2.5%）
**Type**: infographic
**Filename**: 06-infographic-after-skill.png

## Illustration 07

**Position**: #### 效果对比 / 章节开头
**Purpose**: 用视觉方式突出7维度评分对比的核心结论
**Visual Content**: 左侧安装前7.40分 vs 右侧安装后8.90分，重点突出统计分析方法维度6.0→9.5的最大跨度
**Type**: comparison
**Filename**: 07-comparison-skill-score.png

## Illustration 08

**Position**: ### Step 1：Claude Code 提炼报告编写指南 / 章节开头
**Purpose**: 可视化Step 1的操作逻辑：从历史报告到指南文档
**Visual Content**: 7篇历史报告 → Claude Code读取提炼 → 指南文档（写作逻辑+章节结构+标题即结论规范）
**Type**: flowchart
**Filename**: 08-flowchart-step1-guideline.png

## Illustration 09

**Position**: ### Step 2：NotebookLM 收集内容 + 生产初稿 / 章节开头
**Purpose**: 可视化Step 2：105篇文档+指南System Prompt → 按章节产出初稿
**Visual Content**: 105篇文章上传NotebookLM → 指南注入System Prompt → 按章节查询 → 各章节初稿
**Type**: flowchart
**Filename**: 09-flowchart-step2-notebooklm.png

## Illustration 10

**Position**: ### Step 3：Google AI Studio 生成前端框架 / 章节开头
**Purpose**: 展示Step 3：一句提示词生成可运行React框架的过程
**Visual Content**: 麦肯锡风格提示词 → Google AI Studio(Gemini 3 Pro免费) → 可运行React框架（封面/目录/内容页/结束页）
**Type**: flowchart
**Filename**: 10-flowchart-step3-aistudio.png

## Illustration 11

**Position**: ### Step 4：AI Coding 工具 + Agent Skill 迭代 / 章节开头
**Purpose**: 展示Step 4：基础组件+独立文件的多人协作架构
**Visual Content**: 6个基础组件（BaseContentSlide/BaseCard/BaseLineChart等）← 各成员用不同工具（Claude Code/Cursor/Trae）→ 33个组件文件+27个数据文件
**Type**: framework
**Filename**: 11-framework-step4-aicoding.png

## Illustration 12

**Position**: ### Step 5：部署上线 / 章节开头
**Purpose**: 展示最终交付物：40张幻灯片交互系统
**Visual Content**: 部署到腾讯云 → 浏览器访问，40张幻灯片（封面+目录+8章节+结束页），16:9自适应，键盘/滚轮切换
**Type**: infographic
**Filename**: 12-infographic-step5-deploy.png

## Illustration 13

**Position**: ## 04 总结 / 章节开头
**Purpose**: 用一张图收束整个工作流的核心逻辑
**Visual Content**: 三层结构：专业工具分段负责（NotebookLM/AI Studio/Claude Code）+ Agent Skill关键节点注入专业规范 + 可复用框架（换数据集/行业/历史报告）
**Type**: framework
**Filename**: 13-framework-summary-workflow.png
