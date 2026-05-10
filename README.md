# LLM Wiki 知识库

本项目是一个基于 [Karpathy 的 LLM Wiki 理念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 构建的 Obsidian 知识库。

## 核心理念

将碎片化的信息编译成**结构化、高度相互链接**的知识网络，便于 AI 辅助学习和研究。

## 目录结构

```
LLM-Wiki-Vault/
├── assets/                      ← 媒体资产层：存放图片、PDF、附件
│
├── raw/                       ← 原始资料收件箱（只读事实层）
│   ├── tools/                ← 工具类原始素材
│   ├── cybersecurity/          ← 安全类原始素材
│   └── ai/                 ← AI/LLM 类原始素材
│
├── archive/                  ← 已归档（按原始分类存储）
│   ├── tools/
│   ├── cybersecurity/
│   └── ai/
│
├── wiki/                   ← 知识编译输出层（LLM 拥有完全写权限）
│   ├── index.md            ← 全局内容字典
│   ├── log.md            ← 行为流水线
│   ├── concepts/          ← 概念层：方法论、框架
│   ├── entities/          ← 实体层：人物、公司、工具
│   └── sources/          ← 摘要层：源文件核心观点提炼
│
├── memory/                 ← 对话纪要（自动生成）
├── CLAUDE.md              ← 全局心智规范
└── .claude/             ← Claude Code 配置
    └── skills/           ← Agent Skills
        ├── ingest/       ← 摄取技能：编译 raw 到 wiki，自动归档
        ├── query/        ← 查询技能：知识检索与回答
        ├── lint/         ← 体检技能：死链、孤儿页面检查
        ├── annotate/     ← 标注技能：标签与批注
        └── memory/       ← 记忆技能：对话纪要自动记录
```

## 使用方式

在 Obsidian 中打开本 vault，使用 Claude Code 执行操作。

### 常用命令

| 命令 | 功能 |
|------|------|
| `/query <问题>` | 在知识库中搜索相关内容 |
| `/ingest <路径>` | 将源文件编译到知识库 |
| `/lint` | 检查知识库健康度 |
| `/annotate <页面> <标注>` | 添加标签或批注 |

## Wiki 核心文件契约

- **wiki/index.md**：全局目录，维护所有页面的索引
- **wiki/log.md**：操作日志，记录 ingest/query/lint 历史
- **强制双链**：每个页面必须包含 `## 关联连接`

## 知识标注

使用 frontmatter 标签和 Callout 批注：

```yaml
tags: [待验证, 有争议, 已过时, 新见解, 已确认]
```

```markdown
> [!warning] 知识冲突
> 这个观点与 [[另一个页面]] 的说法有冲突
```