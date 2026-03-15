# Skill: urlens

基于URL链接读取文章并按提炼方法论进行总结的技能。

## Triggers

- "提炼公众号文章"
- "总结网页文章"
- "提取文章要点"
- "帮我看看这篇文章"
- "分析这个链接"

## Description

读取公众号文章或其他网页链接，按方法论进行结构化提炼总结。支持两种输出格式：
- **Markdown格式**（默认）：便于阅读的纯文本文件
- **JSON格式**：用于对接工作流

## Usage

当用户发送公众号文章链接或其他网页文章链接，要求提炼总结时使用此skill。

## Workflow

### Step 1: 检查 agent-browser 是否安装

```bash
agent-browser --version
```

如果命令失败，则需要引导用户安装。

### Step 2: 安装 agent-browser（如未安装）

**Step 1: 安装 CLI 和 Chromium**

```bash
npm install -g agent-browser
agent-browser install
```

**Step 2: 添加 skill 到 AI 助手**

```bash
npx skills add vercel-labs/agent-browser
```

安装完成后，重新调用此skill。

### Step 3: 读取文章内容

使用 agent-browser 打开文章URL并获取内容：

```bash
agent-browser goto <文章URL>
agent-browser wait load networkidle
agent-browser extract text --selector body
```

### Step 4: 询问用户输出格式

询问用户希望输出的格式：
- **Markdown格式**（默认）：`.md` 文件，便于阅读
- **JSON格式**：`.json` 文件，用于对接工作流

如果用户未指定，默认使用 **Markdown格式**。

### Step 5: 询问用户输出路径

询问用户希望将提炼结果保存到哪里，包括：
- 文件名
  - Markdown默认格式：`<标题>-摘要-YYYYMMDD.md`
  - JSON默认格式：`<标题>-摘要-YYYYMMDD.json`
- 保存路径（默认：用户主目录）

如果用户未指定，则使用默认路径：
- Markdown: `~/<标题>-摘要-YYYYMMDD.md`
- JSON: `~/<标题>-摘要-YYYYMMDD.json`

### Step 6: 按方法论提炼

读取文章提炼方法论文件：`references/文章提炼方法论.md`

按照以下结构进行提炼：

1. **元信息** - title, link, author, source, publish_date
2. **是否软广** - is_ad, reason, score
3. **作者身份** - job, industry, influence
4. **主题与实体** - themes, key_entities
5. **读者群体** - user_profile (行业、职业、性别、年龄)
6. **文章价值** - value_analysis, impact_scopes, frequency, value_uplifts, score
7. **核心观点与金句** - quote (需注明谁说的+时间+来源), core_insights
8. **关键速览** - highlights (最多10条)
9. **内容标签** - tags (最多5个)

### Step 7: 根据格式输出

根据用户选择的格式，key的翻译规则如下：

**JSON格式**：key保持英文不变
```json
{
  "is_ad": false,
  "author_profile": { "job": "..." },
  "user_profile": [...],
  "value_analysis": [...],
  "meta": { "title": "", "publish_date": "" }
}
```

**Markdown格式**：key翻译为中文
```markdown
# 文章标题

## 一、是否软广
- **is_ad**: false

## 二、作者身份
- **职业**: xxx
- **行业**: xxx

## 三、读者群体
| 行业 | 职业 | 性别 | 年龄 |

## 五、文章价值
### 定性分析
1. xxx
### 定量评估
| 维度 | 评估 |

## 九、元信息
- **标题**:
- **原文链接**:
- **作者**:
- **来源**:
- **发布日期**:
```

### Step 8: 保存结果

根据用户选择的格式保存：
- **Markdown格式**：保存为 `.md` 文件，纯文本便于阅读
- **JSON格式**：保存为 `.json` 文件，结构化数据便于程序处理

## Output Format Key Translation

| JSON Key | Markdown Key |
|----------|--------------|
| meta | 元信息 |
| title | 标题 |
| link | 原文链接 |
| author | 作者 |
| source | 来源 |
| publish_date | 发布日期 |
| is_ad | 是否软广 |
| reason | 判断依据 |
| score | 置信度 |
| author_profile | 作者身份 |
| job | 职业 |
| industry | 行业 |
| influence | 影响力 |
| themes | 主题 |
| key_entities | 关键实体 |
| user_profile | 读者群体 |
| 行业 | 行业 |
| 职业 | 职业 |
| 性别 | 性别 |
| 年龄 | 年龄 |
| value_analysis | 价值分析 |
| impact_scopes | 影响范围 |
| frequency | 频率 |
| value_uplifts | 价值程度 |
| quote | 金句 |
| core_insights | 核心观点 |
| highlights | 关键速览 |
| tags | 内容标签 |

## Output Format Examples

### Markdown 格式示例（key为中文）
```markdown
# 文章标题

> 原文链接：xxx

## 一、元信息
- **标题**: xxx
- **原文链接**: xxx
- **作者**: xxx
- **来源**: xxx
- **发布日期**: xxx

## 二、是否软广
- **是否软广**: false
- **判断依据**: InfoQ原创技术报道
- **置信度**: 5

## 三、作者身份
- **职业**: xxx
- **行业**: xxx
- **影响力**: xxx

## 四、主题与实体
- **主题**: xxx
- **关键实体**: xxx

## 五、读者群体
| 行业 | 职业 | 性别 | 年龄 |
|------|------|------|------|
| 互联网 | 产品 | 男 | 30岁 |

## 六、文章价值
### 定性分析
1. xxx

### 定量评估
| 维度 | 评估 |
|------|------|
| 影响范围 | xxx |
| 频率 | 中 |
| 价值程度 | 高 |
| 综合评分 | 5分 |

## 七、核心观点与金句
### 金句
1. xxx

### 核心观点
1. xxx

## 八、关键速览
1. xxx

## 九、内容标签
```
tag1  |  tag2
```
```

### JSON 格式示例（key为英文）
```json
{
  "meta": {
    "title": "标题",
    "link": "链接",
    "author": "作者",
    "source": "来源",
    "publish_date": "2026-01-01"
  },
  "is_ad": false,
  "reason": "判断依据",
  "score": 5,
  "author_profile": {
    "job": "职业",
    "industry": "行业",
    "influence": "影响力"
  },
  "themes": ["主题1"],
  "key_entities": ["实体1"],
  "user_profile": [
    { "行业": "互联网", "职业": "产品", "性别": "男", "年龄": "30岁" }
  ],
  "value_analysis": ["分析1"],
  "impact_scopes": "范围",
  "frequency": "中",
  "value_uplifts": "高",
  "quote": ["金句1"],
  "core_insights": ["观点1"],
  "highlights": ["要点1"],
  "tags": ["tag1"]
}
```

## Examples

```
用户：帮我提炼这篇公众号文章 https://mp.weixin.qq.com/s/xxx

执行流程：
1. agent-browser --version 检查安装
2. 询问用户输出格式（默认markdown，或json）
3. 询问用户保存路径（或使用默认）
4. agent-browser goto https://mp.weixin.qq.com/s/xxx
5. agent-browser wait load networkidle
6. agent-browser extract text --selector body
7. 按方法论提炼
8. 保存到用户指定路径

用户：帮我提炼并输出json格式 https://mp.weixin.qq.com/s/xxx
→ 输出JSON格式文件
```

## Dependencies

- agent-browser CLI
- agent-browser skill
- 文章提炼方法论文件：references/文章提炼方法论.md

## Metadata

- Version: 1.3.0
- Author: zhimeng
- Tags: article-summary, url-extraction, content-analysis, markdown, json

Base directory for this skill: file:///Users/zhimeng/.agents/skills/url-digest
Relative paths in this skill (e.g., references/) are relative to this base directory.
