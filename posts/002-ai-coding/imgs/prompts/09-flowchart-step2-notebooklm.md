---
illustration_id: 09
type: flowchart
style: crayon-flat
---

Step 2：NotebookLM 收集内容生产初稿

Layout: 从左到右水平流程，左侧两个输入 → 中央处理 → 右侧按章节输出

STEPS:
1. 输入A：「105篇参考文章」— 文件堆叠图标（大叠），标注「上传NotebookLM」
2. 输入B：「编写指南」— 单个文档图标，标注「注入System Prompt」
3. 中央：「NotebookLM」— 大方块，标注「跨文档RAG检索」，按章节提问
4. 输出：「各章节初稿」— 多个文档图标排列，标注「GDP章节 / 生产端 / 消费 / 投资...」

CONNECTIONS:
- 输入A和输入B都有粗箭头指向NotebookLM
- NotebookLM右侧粗箭头指向各章节初稿输出
- 中央顶部：「逐章提问：请整理出GDP章节的内容」对话气泡

LABELS: 105篇文章、编写指南、System Prompt、NotebookLM、跨文档检索、各章节初稿、Step 2

COLORS:
- Background: cream white (#FAFAF5)
- 输入A（105篇文章）: Crayon Red (#D9230F) fill
- 输入B（编写指南）: Crayon Orange (#E8742A) fill
- 中央NotebookLM: Crayon Blue (#4A90D9) fill, largest block
- 输出章节初稿: Crayon Green (#5BA85F) fill
- 对话气泡: Crayon Yellow (#F0C132) fill
- 箭头: Bold black (#1A1A1A), thick crayon
- Step 2标签: Crayon Orange (#E8742A) pill

STYLE: Bold thick crayon outlines on all elements, flat solid colors with no gradients, slight paper texture. NO human characters. Two inputs converging to central processor then outputs.

ASPECT: 16:9

Clean composition with two inputs on left, central processor in middle, multiple outputs on right.
