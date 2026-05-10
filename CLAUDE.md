# 语言设定与核心角色 (Global Rules)
- **语言指令**：无论输入何种语言，你必须始终使用**简体中文**进行思考、回复和知识库的编写。
- **角色定义**：你正在维护一个 **LLM Wiki**（根据 [Karpathy 的规范](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f))，你的任务是将碎片化的信息编译成结构化、高度相互链接的 Obsidian 知识库。

# 核心目录与权限边界 (Immutability & Architecture)
你必须严格遵守以下文件操作权限，这是不可逾越的底线：

## raw/ 按项目分类（初期）
raw/
├── tools/         # 工具类原始素材
├── cybersecurity/ # 安全类原始素材
├── ai/            # AI/LLM 类原始素材

## archive/ 对应项目归档
archive/
├── tools/        # 归档到对应项目
├── cybersecurity/ # 归档到对应项目
├── ai/           # 归档到对应项目

- `/raw/` (不可变层 - Immutable)：
  - **绝对只读**。这里存放我的原始素材、网页剪藏和自媒体文案。
  - **禁止修改或删除此目录下的任何文件**。它是事实的唯一真相来源。
- `/assets/` (媒体资产层)：
  - 存放图片、PDF和媒体。引用时使用 Obsidian 标准语法 `![[文件名称.png]]`。
- `/wiki/` (编译输出层 - You Own This)：
  - 这是你的专属工作区。你需要在此处创建、更新、提炼知识并解决矛盾。
  - 不需要严格目录结构，可以是杂乱的
  - 主要目标：双向链接，便于检索

# Wiki 核心文件契约 (The Wiki Schema)
当你在 `/wiki/` 中工作时（尤其是执行写入操作后），必须维护以下基石：

1. **`wiki/index.md` (总目录)**：
   每次向 wiki 新增知识页后，必须同步更新此文件，将其按分类加入目录中。
   格式要求： [[页面名称]] — 一句话描述。
    - Entities/Concepts: 使用 TitleCase 命名。
    - Sources/Syntheses: 使用 kebab-case 命名。
    范例：
    ```markdown
    # Wiki Index

    ## Sources
    - [[摘要-source-slug]] — 该资料的核心主旨摘要。

    ## Entities
    - [[EntityName]] — 该实体的身份定义或核心功能。

    ## Concepts
    - [[ConceptName]] — 该概念或框架的核心定义。

    ## Syntheses
    - [[synthesis-slug]] — 该页面回答的复杂问题。
    ```
2. **`wiki/log.md` (操作日志)**：
    只能追加写入（Append-only）。每次操作后记录：`## [YYYY-MM-DD] <动作> | <操作简述>`。
    操作类型： ingest, query, lint, sync
    范例：
    ```markdown
    ## [2026-04-11] ingest | 引入项目 Claude Code 核心概念
    - **变更**: 新增 [[ClaudeCode]], [[摘要-claude-code-docs]]; 更新 [[index.md]]
    - **冲突**: 无 (或: 冲突 [[RAG架构]], 已标注)

    ## [2026-04-11] query | 解析 Karpathy LLM-Wiki 理念
    - **输出**: 已保存至 [[分析-karpathy-wiki-philosophy]]

    ## [2026-04-11] lint | 周度健康检查
    - **结果**: 修复 2 处死链，发现 1 个孤儿页面 [[UnlinkedPage]]
    ```
3. **内容分类**：
   - `/wiki/concepts/`：存放概念、框架、方法论（如 `Agent_Skill.md`）。
   - `/wiki/entities/`：存放人物、公司、工具、产品（如 `Claude_Code.md`）。
   - `/wiki/sources/`：存放从 `raw/` 提炼出的原始素材摘要。
4. **强制双向链接**：
   每一个 wiki 页面必须包含 `## 关联连接` 区域，使用 Obsidian 双链 `[[页面名称]]` 链接到其他相关概念。绝不能产生孤岛页面。
5. **矛盾处理原则**：
   如果新摄入的知识与旧知识冲突，不要静默覆盖。在页面中新建 `## 知识冲突` 区块，将两种说法都保留并做对比。

# 工作流指令说明 (Workflows / Skills)

当被要求执行以下操作时，请遵循 Agent Skills 中定义的核心逻辑：

- `/ingest <路径>`：将 raw/ 源文件编译到 wiki/ 并归档到 archive/{project}/
- `/query <问题>`：在 Wiki 知识库中检索并回答
- `/lint`：全局健康检查
- `/annotate <页面> <标注>`：为页面添加标签或批注

**自动触发**：
- memory：每轮对话结束时自动记录对话纪要
- annotate：发现知识冲突时自动标注

# /ingest 三步工作流

每次执行 `/ingest <raw/PROJECT/文件名>` 后，必须完成以下三步：

## Step 1: 归档
按源文件的原始分类移动到对应的 `archive/` 子目录：
- `raw/tools/*` → `archive/tools/`
- `raw/cybersecurity/*` → `archive/cybersecurity/`
- `raw/ai/*` → `archive/ai/`
- 其他 `raw/PROJECT/*` → `archive/PROJECT/`

如果对应的 `archive/{PROJECT}/` 目录不存在，则先创建该目录。

## Step 2: 生成知识文件
- 在 `/wiki/` 下创建对应知识点页面
- 包含 YAML frontmatter + 正文 + 关联连接

## Step 3: 生成双向链接摘要
- 在 `wiki/index.md` 中添加摘要
- 摘要必须包含：
  - 核心要点（3-5 条）
  - 与 wiki 知识点的双向链接
  - 与 raw 源文件的链接
- **不能太简单**，需要足够信息量

```markdown
## Entities
- [[ClaudeCode]] — Anthropic 官方 CLI 工具
  - 核心：本地运行 Claude，集成 git hook
  - 关联：[[ANTHROPIC_API]], [[ClaudeCode_Settings]]
  - 源文件：raw/tools/claude-code.md
```

# /query 多级检索工作流

当执行 `/query <问题>` 时，按以下顺序检索：

## 规则：对"具体文章"提问 → 上下文优先
- 如果问题提到具体文章名，先去上下文检索
- 上下文没有再去文章（raw/archive）中检索

## 常规检索顺序

| 层级  | 位置        | 说明                               |
| --- | --------- | -------------------------------- |
| 1   | index.md  | 先查索引中的摘要                         |
| 2   | wiki/ 知识点 | 无法回答 → 根据 index 链接去 Wiki         |
| 3   | 原始文档      | 仍然无法回答 → 根据 wiki 链接去 raw/archive |

### 工作流

1. 读取 wiki/index.md，寻找相关知识点
2. 如找到匹配：
   - 读取关联的 wiki 页面
   - 如能回答 → 返回答案（带 [[wikilink]] 引用）
3. 如不能回答：
   - 根据 wiki 页面的链接去对应原始文档
   - 读取 raw 或 archive 中的原文
   - 如能回答 → 返回答案
4. 如仍无法回答：
   - 声明"本地知识库未找到"

### 示例
- "Claude Code 是什么？" → 先读 index → 有 [[ClaudeCode]] 链接 → 读 wiki/ClaudeCode.md → 回答
- "raw/tools 中的 XX 文章讲了什么？" → 直接去 raw/tools/XX.md 检索

# 上下文维护 (Context Memory)

使用 `/memory` 技能自动记录。详细规范见 `.claude/skills/memory/SKILL.md`

## 为什么需要
- 对话 token 限制约 200k，超出会压缩
- 关闭会话后上下文消失
- 需要外部文档来"记住"学习进度

## 机制：对话压缩（自动触发）
- 每轮对话结束时，自动生成"对话纪要"
- 包含：本轮学了什么、还有什么要继续
- 保存位置：memory/{YYYY-MM-DD}-{主题slug}.md

# 知识标注方式

使用 `/annotate` 技能进行标注。详细规范见 `.claude/skills/annotate/SKILL.md`

## 标签（快速标记）
```yaml
tags: [待验证, 有争议, 已过时, 新见解, 已确认]
```

| 标签 | 含义 |
|------|------|
| 待验证 | 内容未经确认，需核实 |
| 有争议 | 存在不同观点，需标注冲突 |
| 已过时 | 内容已过时，需更新 |
| 新见解 | 新发现或更新观点 |
| 已确认 | 已被验证的内容 |

## 批注（Callout 格式）
```markdown
> [!note] 标注内容
> 可以包含 [[双链]] 引用

> [!warning] 警告/冲突标注
> 这个观点与 [[另一个页面]] 的说法有冲突

> [!info] 补充说明
> 相关上下文或背景信息
```

## 知识冲突模板
```markdown
## 知识冲突

### 观点A（本文）
[原观点描述]

### 观点B（来自 [[其他页面]]）
[冲突观点描述]

### 对比分析
| 方面 | 观点A | 观点B |
|------|------|------|
| 来源 | xxx | xxx |
| 时间 | xxx | xxx |
```

# 页面 Frontmatter (YAML) 规范
所有生成的 wiki 页面必须包含以下 YAML 头部：
---
title: "页面标题"
type: concept | entity | source | synthesis
tags: [知识标签]
sources:[关联的raw文件相对路径]
last_updated: YYYY-MM-DD
---