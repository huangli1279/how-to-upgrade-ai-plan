[![Peter Steinberger, il creatore di OpenClaw, lavorerà per OpenAI - la ...](https://tse4.mm.bing.net/th/id/OIF.RPnRykzOa0H2Np0IxLlCFg?pid=Api)](https://www.repubblica.it/tecnologia/2026/02/16/news/peter_steinberg_openclaw_openai-425162039/?utm_source=chatgpt.com)

下面整理的是 **OpenClaw 作者 Peter Steinberger（@steipete）** 在多篇文章/访谈里反复强调的 *vibe coding（他更常称“agentic engineering”）* 实战技巧与“反直觉但很管用”的习惯做法。

---

## 1) 先把“闭环”当成第一原则：能跑、能测、能验证

* 他认为产能瓶颈更多变成了**推理/思考与验证**，而不是敲代码；因此默认从 **CLI/纯文本形态**开始做，因为 **agent 可以直接调用 CLI 并验证输出**，更容易“closing the loop（闭合环路）”。([Steipete][1])
* 在访谈里他也强调：终端/CLI 的好处是**没有 UI 干扰，你和 agent 就是在对话**，而且你可以用“Discuss / 给选项 / 再 Build”来控制它什么时候真的开始改代码。([Lex Fridman][2])

**怎么用：**
把任何新点子先做成 1 个命令（哪怕很丑），让 agent 能：运行 → 看输出/跑测试 → 继续修。

---

## 2) “先聊清楚，再开工”：用对话代替繁琐的 plan-mode 仪式

* 在《Shipping at Inference-Speed》里他说自己不太依赖所谓 plan-mode：更像是**先和模型一起读代码/查资料/形成计划**，满意后再丢一句 “build”。([Steipete][1])
* 《Just Talk To It》结论也很直白：别把时间浪费在各种“charade（花架子）”上，**直接跟它聊、玩、形成直觉**。([Steipete][3])

**好用的触发词（他在文中/访谈都提到类似用法）：**
“Discuss / give me options / don’t write code yet / take your time / comprehensive / read all related code / form hypotheses” ([Steipete][3])

---

## 3) 并行多 agent，但别过度“自动化编排”

* 他常见的工作方式是 **3–8 个 codex CLI 并行跑**，用“我在中间切换任务/心智模型”来当真正的调度器。([Steipete][3])
* 他不太信“一键全自动 orchestrator”，更像是：**做一个最小版本 → 上手玩 → 获得新想法 → 迭代演化**，因为“风格/品味/人味”短期很难自动化掉。([Lex Fridman][2])

---

## 4) 输入侧：语音脑暴 + 让 AI 帮你把“含糊”变“规格”

在他早期展示 vibe coding 工作流时（《The Future of Vibe Coding》）：

* **语音优先脑暴**：先把功能/用户故事用语音倾倒出来。([Steipete][4])
* 再让 Gemini/模型把口述内容整理成**结构化 spec**；他强调“spec 越好，后面生成代码摩擦越小”。([Steipete][4])

（他在 Lex 访谈也提到自己很依赖语音输入来“写”提示。([Lex Fridman][2])）

---

## 5) 工具侧：能用 CLI 就别用“吃上下文”的重协议/重插件

* 他非常“CLI 派”：**很多 MCP/插件更应该做成 CLI**，因为 CLI 天然可组合（例如配合 jq 过滤输出），而且不会把一大坨协议描述塞进上下文造成“context tax”。([Lex Fridman][2])
* codex 没有后台任务时，他会直接用 **tmux** 解决“dev server/测试卡住”的问题。([Steipete][3])

---

## 6) 代码库要“为 agent 可维护”而设计：别强迫它按你的洁癖写

他在访谈里给了一个很关键的心态转换：

* 接受“代码未必像你亲手写的那么完美”，重点是**推动项目向前**；而且要把代码库设计成 **agent 好导航、好搜索、好延展** 的形状。([Lex Fridman][2])
* 他甚至直说：别老跟 agent 选的命名较劲——名字越“在权重里更自然”，下次它越容易自己找回并保持一致。([Lex Fridman][2])

---

## 7) 质量保障：让 agent 负责“测试/文档/重构”，你负责品味与方向

* 《Just Talk To It》里他建议：**每做完一个 feature/fix 就让模型顺手补测试**（同一上下文里写出来的测试往往更贴合实现，也能顺带挖出 bug）。([Steipete][3])
* 他会把文档维护当成“feature 的一部分”，并用 docs/规则文件来给后续 agent 提供稳定上下文。([Lex Fridman][2])
* 他把相当一部分时间留给“集中重构日”，用工具找重复/死代码、升级依赖、拆大文件等（大多也交给 agent 做）。([Steipete][3])

---

## 8) 交付节奏：主干可发布、少折腾分支状态

他倾向“线性演化”：

* 更偏向 **main 直接推进**、减少多状态切换带来的认知负担（适合个人/小团队；大团队另说）。([Steipete][1])

---

## 9) 最后的提醒：vibe coding 很上头，记得给自己刹车

他写过一个很具体的例子：自己做 VibeTunnel 时一度**在和朋友外出时还在手机上写/改**，最后为了心理健康决定停下来。([Steipete][1])

---

# 一页“照抄就能用”的 Steinberger 风格清单

1. 任何新功能先做成 **CLI/可验证最小闭环**。([Steipete][1])
2. 先说“Discuss / options / don’t write code yet”，确认方向再说“Build”。([Lex Fridman][2])
3. 并行 3–8 个 agent：主线功能 / 小修小补 / 文档重构 分开跑。([Steipete][3])
4. **能 CLI 就 CLI**（少用上下文税很高的重插件/重协议）。([Lex Fridman][2])
5. 每个改动让 agent **补测试 + tricky 注释**。([Steipete][3])
6. 接受“没那么完美”，把代码库做成 **agent 好读好搜好改**。([Lex Fridman][2])

如果你愿意，我也可以把这些技巧整理成一份“可直接粘贴到 AGENTS.md / 项目 README 的团队规范模板”（中英双语都行）。

[1]: https://steipete.me/posts/2025/shipping-at-inference-speed "Shipping at Inference-Speed | Peter Steinberger"
[2]: https://lexfridman.com/peter-steinberger-transcript/ "Transcript for OpenClaw: The Viral AI Agent that Broke the Internet - Peter Steinberger | Lex Fridman Podcast #491 - Lex Fridman"
[3]: https://steipete.me/posts/just-talk-to-it "Just Talk To It - the no-bs Way of Agentic Engineering | Peter Steinberger"
[4]: https://steipete.me/posts/2025/the-future-of-vibe-coding "The Future of Vibe Coding: Building with AI, Live and Unfiltered | Peter Steinberger"
