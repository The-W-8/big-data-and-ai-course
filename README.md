# big-data-and-ai-course

大数据与人工智能课程学习仓库：围绕 **Agent（智能体）、大模型上下文（Context）、Skill（AI 技能）** 三个核心概念的自学笔记、概念关系梳理，以及一个可复用的"概念自学笔记生成器" Skill。

## 仓库用途

- 沉淀课程学习过程中产出的 AI 概念结构化笔记（AI 生成初稿 + 人工核查修订）；
- 通过自带的 `concept-self-learner` Skill，把"学习任意新概念"变成可重复执行的标准流程；
- 作为可追溯的知识库：笔记中的每个关键论断都附有可核查的公开资料链接。

## 目录结构

```
big-data-and-ai-course/
├─ .workbuddy/
│  └─ skills/
│     └─ concept-self-learner/
│        └─ SKILL.md          # 概念自学笔记生成器 Skill
├─ learning-materials/
│  ├─ agent.md                # AI Agent 概念笔记
│  ├─ llm-context.md          # 大模型上下文概念笔记
│  └─ skill-concept.md        # Skill 概念笔记
├─ concept-relationship.md    # 三者关系说明（表格 + Mermaid 图）
├─ README.md                 # 本文件
└─ .gitignore                # 忽略密钥、环境文件、缓存
```

## Skill 存放路径

```
.workbuddy/skills/concept-self-learner/SKILL.md
```

## 如何调用这个 Skill

在 WorkBuddy / 支持 Skill 加载的 AI 助手中，直接用自然语言下达指令即可，例如：

> "请使用 concept-self-learner skill，帮我生成一份关于「检索增强生成（RAG）」的自学笔记。"

调用要点：

1. Skill 由 AI 根据请求自动匹配触发（判断依据是 SKILL.md 中的 `name` 与 `description`），无需手动选择；
2. **必填输入**：概念名称（如"思维链""KV 缓存""多模态"等任意 AI 概念）；
3. **可选输入**：学习背景（入门/进阶）、侧重方向（原理/工程/应用）、输出语言（默认中文）；
4. 产出物为一份固定五部分结构的 Markdown 笔记：①个人解释 ②核心机制或组成 ③一个具体应用场景 ④易混淆问题或使用边界 ⑤可核查的公开资料链接，保存到 `learning-materials/` 下即可。

## 仓库内已包含的学习资料

| 文件 | 内容 |
| --- | --- |
| `learning-materials/agent.md` | AI Agent：定义、四大组成（LLM/规划/记忆/工具）、编码智能体案例、与 Chatbot/Workflow 的区别 |
| `learning-materials/llm-context.md` | 大模型上下文：窗口机制、Lost in the Middle、RAG 应用、上下文 vs 记忆 vs 微调 |
| `learning-materials/skill-concept.md` | Skill：SKILL.md 结构、渐进式披露三级加载、周报技能案例、与 Prompt/MCP 的区别 |
| `concept-relationship.md` | 三者关系：上下文如何影响 Agent 运行、Skill 如何沉淀可复用知识（含 Mermaid 图） |

三份概念笔记的资料来源包括 Lilian Weng 的 Agent 综述、Anthropic 官方工程博客与 Agent Skills 文档、arXiv 论文等，链接均已在各笔记第 5 部分列明。

## AI 生成初稿之后的人工核查清单

⚠️ 本仓库的笔记均由 AI 生成初稿，**发布/提交前必须完成以下人工核查与修改**：

### 事实核查

- [ ] **逐条验证链接**：亲手打开每条资料链接，确认可访问、内容与描述一致（AI 可能编造 URL）；
- [ ] **核对关键论断**：对照原始资料检查笔记中的事实陈述（如模型参数、机制细节、论文结论），AI 存在幻觉风险；
- [ ] **甄别资料时效性**：AI 引用的资料可能过时，涉及快速演进的技术（窗口大小、模型版本等）应以官方最新文档为准。

### 内容修订

- [ ] **改写"个人解释"**：把 AI 的解释真正消化后用自己的话重写，这是笔记内化为个人知识的关键一步；
- [ ] **补充个人理解与疑问**：在易混淆、使用边界等部分补充自己的实际体会和未解决问题；
- [ ] **校准应用场景**：将通用示例替换或补充为自己实际工作/项目中的场景，让笔记可落地；
- [ ] **检查通俗性**：确保非本领域背景的读者能读懂解释部分，删掉未拆解的行话。

### 工程规范

- [ ] **术语中英对照统一**：全文术语口径一致（如统一使用"上下文窗口/Context Window"的某一种写法）；
- [ ] **Markdown 渲染检查**：确认表格、Mermaid 图在目标平台正常渲染；
- [ ] **敏感信息检查**：确认笔记中没有 API Key、内部链接等敏感内容（配合 `.gitignore` 使用）。
