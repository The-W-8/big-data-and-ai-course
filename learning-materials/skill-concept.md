# Skill（AI 技能）自学笔记

> 概念英文名：Agent Skill / Skill（本文指 AI 领域的 Skill 概念，以 Anthropic 的 Agent Skills 为代表实现）
> 所属分支：大模型应用工程 / 知识沉淀
> 本笔记按"个人解释 → 核心机制 → 应用场景 → 易混淆边界 → 自测题 → 参考文献"组织，参考文献均为一手原始技术报告。

## 1. 个人解释

一句话定义：**Skill 是把"某类任务该怎么做"的流程知识，固化为 AI 能自动发现、自动加载的标准化能力包——通常是一个带 SKILL.md 说明书的文件夹。**

展开说：没有 Skill 时，你每次想让 AI 按特定规范干活，都得重新粘贴一遍长篇要求（比如"周报要按这个模板写，格式要求如下……"）；换个会话还得再来一次。Skill 的做法是把这些操作流程写成一份结构化说明书（SKILL.md），放到约定目录下。之后每当你的请求与说明书匹配，AI 会**自动把它读进来**，瞬间从"通才"变成该任务的"熟手"。可以类比为给一位聪明的新同事发一本《入职操作手册》：他能力很强，但需要手册告诉他"我们这儿这类事是这么办的"。它解决的核心痛点是：**把一次性的提示词，变成可版本管理、可团队共享、可跨会话复用的任务知识资产。**

## 2. 核心机制或组成

**（1）文件结构。** 一个 Skill 是一个目录，唯一必需的文件是 SKILL.md，可附脚本与资料：

```
my-skill/
├── SKILL.md          # 必需：YAML 元数据 + 正文指令
├── references.md     # 可选：按需加载的参考资料
├── examples.md       # 可选：示例
└── scripts/          # 可选：可执行脚本
    └── helper.py
```

**（2）SKILL.md 的两部分构成。**

- **YAML 元数据**（`---` 包裹）：`name`（技能名）+ `description`（做什么、什么时候用）——这是 AI 判断"该不该调用我"的唯一依据；
- **Markdown 正文**：具体的工作流程、规范、注意事项。

**（3）渐进式披露（Progressive Disclosure）——三级按需加载：**

| 级别 | 加载时机 | Token 开销 |
| --- | --- | --- |
| Level 1：元数据（name + description） | 会话启动时常驻 | 每个 Skill 仅约百 token |
| Level 2：SKILL.md 正文 | 任务匹配、Skill 被触发时 | 中等 |
| Level 3：参考资料 / 脚本 | 正文引用到时才读取 | 未用到则近乎为零 |

这个机制使得可以安装大量 Skill 而几乎不占用上下文——只有真正被用到的内容才进入模型视野，未触发的 Skill 常驻成本极低。

## 3. 一个具体应用场景

**团队统一的周报生成技能：**

某团队规定周报必须包含"本周完成 / 风险阻塞 / 下周计划"三段，且格式、用语有内部规范。过去每次都要把规范粘贴给 AI，且每个人的写法不一。做成 skill 后：

1. 在项目 `.workbuddy/skills/`（或同类约定目录）下创建 `weekly-report/SKILL.md`，元数据写明"当用户要求写周报时使用"，正文写入完整模板与规范；
2. 团队成员通过 Git 共享该目录；
3. 任何人说"帮我整理这周的周报"，AI 根据描述自动匹配并加载该技能，按统一模板产出初稿；
4. 规范更新时只需改这一个文件，全员生效（版本可追溯）。

"写一次、全员自动复用"，这就是 Skill 沉淀可复用任务知识的直观体现。

## 4. 容易混淆的问题或使用边界

**易混淆概念对比：**

| 概念 | 生命周期 | 适合放什么 | 上下文开销 |
| --- | --- | --- | --- |
| 普通提示词（Prompt） | 单次对话 | 一次性任务要求 | 每次都要重写 |
| 系统提示 / 全局指令（如 CLAUDE.md） | 整个会话常驻 | 全局事实与偏好 | 一直占用 |
| Skill | 按需加载（相关时才进入上下文） | 可复用的领域流程与操作手册 | 未触发近乎为零 |
| MCP / 工具 | 常驻连接 | 给模型"接上"外部系统与数据 | 间接占用 |

**使用边界与常见误用：**

- **Skill 是模型调用，不是用户手选**：是否使用某个 Skill 由 AI 根据 description 自主判断，description 写得含糊就不会被触发。
- **Skill 与 Prompt/MCP 是互补而非替代**：Skill 管流程知识，MCP/工具管外部连接，二者可组合使用。
- **不跨平台自动同步**：不同产品（如网页端与 API 端）的 Skill 需分别管理。
- **判断标准**：当全局指令里某段内容从"事实"长成"操作步骤"时，就应抽成 Skill——让常驻上下文保持精简。
- **注意区分"AI 的 Skill"与"游戏技能树"等无关领域**：本概念专指 AI 应用的知识封装模式。

## 5. 自测题（含参考答案）

### 选择题

**1. 一个 Skill 是否被使用，由谁决定？**

A. 用户每次手动选择
B. AI 根据系统提示中的 name 与 description 元数据自主判断
C. 按固定时间表定时执行
D. 随机触发

**2. 渐进式披露（Progressive Disclosure）的第一级加载的是什么内容？**

A. SKILL.md 全文
B. YAML 元数据（name + description）
C. 技能附带的全部脚本
D. 参考资料文件全文

**3. Skill 与一次性 Prompt 的最大区别是？**

A. Skill 的文字更长
B. Skill 可被版本管理、团队共享、按需自动加载、跨会话复用
C. Skill 只能用于编程任务
D. Prompt 比 Skill 更适合沉淀团队规范

**4. 下列哪种内容更适合做成 Skill 而不是写进系统提示？**

A. 全局偏好类的稳定事实（如"默认使用中文"）
B. 可复用的领域操作流程（如"周报按团队模板三段式生成"）
C. 一次性的任务要求
D. 用户当前会话的临时需求

### 简答题

**1. 描述一次 Skill 从安装到完成任务的完整三级加载过程。**

**2. 为什么团队可以沉淀几十上百个 Skill，却几乎不挤占模型的上下文窗口？**

### 参考答案

**选择题：** 1-B（见第 4 节使用边界）；2-B（见第 2 节三级加载表）；3-B（见第 1 节，知识资产化）；4-B（见第 4 节判断标准：从"事实"长成"操作步骤"就该抽成 Skill）。

**简答题：**

1. ① 启动时，系统提示只常驻每个 Skill 的元数据（name + description，约百 token 量级）；② 当用户请求与某 Skill 的 description 匹配时，AI 才读取该 SKILL.md 正文进入上下文；③ 正文若引用了参考资料或脚本，仅在真正用到时才读取文件内容或执行脚本（脚本只返回输出，源码不进上下文）。
2. 因为渐进式披露：未触发的 Skill 只有元数据常驻（每个约百 token），正文与资料只在任务匹配时才进入上下文，脚本执行也只把输出带回。知识的"存放"发生在文件系统，知识的"消费"才发生在上下文——两者解耦，因此沉淀数量与上下文占用几乎无关。

## 6. 参考文献

[1] Anthropic. Introducing Agent Skills. Anthropic Official Announcement, 2025-10-16. https://www.anthropic.com/news/skills

[2] Anthropic. Equipping Agents for the Real World with Agent Skills. Anthropic Engineering Blog, 2025. https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills

[3] Anthropic. Agent Skills Overview. Claude Platform Official Documentation. https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview

[4] Anthropic. skills: Anthropic's Official Repository of Agent Skills. GitHub. https://github.com/anthropics/skills

> 正文关键论断来源：Skill 目录结构与 SKILL.md 元数据构成出自 [2]；渐进式披露三级加载机制出自 [2][3]；触发方式（模型自动判断、无需手动选择）与跨产品使用出自 [1]；真实示例写法参考 [4]。
