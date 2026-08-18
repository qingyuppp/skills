---
name: paper
description: 读论文流程 skill——入库（arxiv URL/标题→下载PDF→猜类别→建文件夹→初始化5个文件骨架）、带读（分小节第一性原理讲解+一次一道quiz当场评、可续读）、更新note（回填一句话总结/Pipeline/和锚定项目关联表+加✅前缀）。触发词：入库这篇论文/把这篇加入文献库、开始带读/继续带读/带读这篇、更新note/收尾这篇。
---

# 读论文 Skill

把读论文的「入库 → 带读 → 更新 note」流程固化。遵循用户 `论文/04-读论文的方法.md` 的根本目的：① 一句话描述 Novelty；② 几句话描述 Pipeline。带读遵循第一性原理 + 奥卡姆剃刀——原文再复杂，只讲基本原理。

## 配置（使用前按需修改）

```
VAULT_ROOT    = /path/to/your/obsidian/vault   # Obsidian vault 绝对路径
LIBRARY_ROOT  = 论文/01-文献库                  # vault 内的文献库相对路径
ANCHOR_PROJECT = MY_PROJECT                    # 所有关联讨论的锚定项目名称
```

> 路径含空格时，所有 Bash 命令必须对路径加引号。

## 锚定项目

`ANCHOR_PROJECT = MY_PROJECT`（按需修改为你的项目代号）

所有「和 X 的关联」「相对 X 的 gap」讨论，X 均指 ANCHOR_PROJECT。将来换项目只改这一行。

## 状态机

状态记在每篇论文 `Note.md` 的 frontmatter：

```yaml
phase: ingested   # ingested | reading | done
read_sections: [] # 已带读完的小节序号，如 [1, 2]
```

- `ingested`：已入库，待带读
- `reading`：带读中，`read_sections` 记已完成小节，可随时续读
- `done`：已收尾

「这篇」的解析：用户未指明时，取最近一个 `phase != done` 的论文文件夹；有歧义让用户指明。

---

## 阶段一：入库（ingest）

输入：arxiv URL 或论文标题。

1. **取 PDF + 元信息**
   - arxiv URL：`curl -L -o Paper.pdf "<arxiv-pdf-url>"` 下载 PDF 到目标文件夹；用 WebFetch 抓 arxiv abstract 页取 title / author / venue / 年份
   - 仅标题：WebSearch 找官方页 / arxiv 链接再下；下不到则回退——请用户手动把 `Paper.pdf` 放进目标文件夹后继续
2. **猜入库类别**：用 Bash `find "<LIBRARY_ROOT>" -maxdepth 1 -mindepth 1 -type d` 列出全部子目录（过滤掉散落的 `.md` 文件）；读摘要后猜最匹配的一个；**列出全部子目录让用户确认或改**（先猜后确认）
3. **建文件夹**：`<LIBRARY_ROOT>/<入库类别>/<Year>-<ShortName>/`
   - ShortName **优先用论文自报的简称**（如 CoDA、DataMind）；没有才从标题取缩写
   - 同名文件夹已存在 → 报错问用户，**不覆盖**
4. **初始化文件**（按下方「文件骨架模板」生成）：
   - `Note.md`：填 frontmatter（含 `phase: ingested`、`read_sections: []`）+ `# 标题` + `## 一句话总结`（占位）+ `## 和 ANCHOR_PROJECT 的关联`（空表）+ `### 带读`（空）。**带读内容此刻不写**
   - `PDF标注.md`：frontmatter `annotation-target` 指向 Paper.pdf + 标题 + 指向 Note 的链接。高亮由用户用 Obsidian PDF annotation 插件导出，skill 不替代
   - `Paper.pdf`：已下载就位
   - `Paper-zh.md`：默认创建空骨架
   - `Review.md`：默认创建；WebFetch/WebSearch 查 OpenReview（按标题或 arxiv id）。**有则放 review 页链接 + 投稿场次/评分**，正文留空；**查不到则写一行「未找到 OpenReview 页面」**。不自动抓全文审稿意见
5. **入库完成**：回复文件夹路径 + 提示「可执行 `开始带读这篇`」

---

## 阶段二：带读（reading）

1. **取材**：Read `Paper.pdf` 全文 + `PDF标注.md` 高亮（若有）。优先按用户高亮重点讲；高亮为空按 PDF 原结构讲
2. **先出大纲**：把论文划成 N 个小节（一般 3–5 个，按「问题→方法→实验→洞察」pipeline 逻辑；第一性原理 + 奥卡姆剃刀——原文再复杂只讲基本原理）。**大纲先给用户确认/调整**，定稿后写进 `Note.md` 的 `### 带读` 下作为各 `#### § N：标题`
3. **逐节带读循环**（一节内）：
   - **讲解时直接写入 `Note.md`**：开口讲之前，先把 `#### § N：标题` 标题行 append 进文件，然后边讲边把讲解内容写入该节下。**讲解内容只生成一次，对话输出即文件内容**，不存在"事后抄录"步骤
   - 当场出题：**3选1 → 判断 → 简答**，**一次一道**：出一道 → 等用户答 → 评分 + 给解释（错或含糊则追问/补讲，直到答对）→ 每题结束后立即把该题 Q&A **append 进 `Note.md`**，再出下一道
   - 三道都过 → `read_sections` 追加该节号，本节完毕
   - **Q&A**：题号 `（3选1）/（判断）/（简答）` 用全角括号，`答：` 用全角冒号并加粗，对错符为 `✓`（对）或 `错`。若用户本节开始前说过「直接给答案」，则所有题只写正确答案和解释，**不写「用户答 X，错」**。三种题型 × 答对/答错 的精确写法：

     ```markdown
     > **Q1（3选1）** <题干>
     > A. <选项A>
     > B. <选项B>
     > C. <选项C>
     > **答：<正确选项> ✓**                                   # 用户答对
     > **答：<正确选项>**（用户答 <用户选项>，错）— <解释>      # 用户答错

     > **Q2（判断）** <题干>
     > **答：<对/错> ✓** — <解释>                              # 用户答对（判断题无论对错都跟解释）
     > **答：<正确对/错>**（用户答<用户对/错>，错）— <解释>      # 用户答错

     > **Q3（简答）** <题干>
     > **答：** <用户答案> ✓                                   # 用户答对（仅「答：」加粗，正文与 ✓ 在外）
     > **答：** <正确答案要点>（用户答 <用户答案要点>，错）— <解释>  # 用户答错
     ```

   - `phase` 保持 `reading`
4. **发掘创新点（融在带读里）**：每节讲完，主动点出「这节相对 ANCHOR_PROJECT 的可借鉴/缺口/可延伸点」作为讨论引子。**默认口头讨论，不写文件**；用户说「放进 note」时，再写入 `Note.md` 该节末尾
5. **续读**：随时可停。下次「继续带读这篇」→ Read `read_sections` 找到下一未完成小节，接着讲

---

## 阶段三：收尾（finalize）

前置：带读全部小节完成（`read_sections` 覆盖完大纲）。

1. **回填 `## 一句话总结`**：据带读内容写 Novelty 一句话。直接写入（带读时用户已看过，不走草稿确认）
2. **回填 Pipeline**：在 `### 带读` 顶部写 `**Pipeline**` 步骤串（1→2→3...）。直接写入
3. **回填 `## 和 ANCHOR_PROJECT 的关联`**：把带读中讨论过的对比/缺口/可借鉴点整理成对比表，格式 `| | 本论文 | ANCHOR_PROJECT |`。直接写入
4. **置位** `phase: done`，`read_sections` 标全
5. **文件夹改名加 `✅ ` 前缀**：`<Year>-<ShortName>` → `✅ <Year>-<ShortName>`。用 Bash `mv`，路径加引号。注意 `✅` 后有一个空格
6. **可选**：若该篇来自某待读表（如 `7.20 待读.md`），把对应行标 `✅`

> 回填一律直接写入（带读阶段用户已逐节看过内容）。

---

## 文件骨架模板

### Note.md

```markdown
---
phase: ingested
read_sections: []
title_zh: "<中文标题>"
venue: <venue>
ccf: <ccf 或 非 CCF 收录>
url: <arxiv/官方 url>
github: <repo 或 N/A>
topics: [<topic>...]
---

# <原标题>

## 一句话总结

（待收尾回填）

## 和 ANCHOR_PROJECT 的关联

| | 本论文 | ANCHOR_PROJECT |
|---|---|---|
| | | |

### 带读
```

### PDF标注.md

```markdown
---
annotation-target: <LIBRARY_ROOT>/<入库类别>/<Year>-<ShortName>/Paper.pdf
---

# PDF 标注 - <ShortName>

> 论文：[[<LIBRARY_ROOT>/<入库类别>/<Year>-<ShortName>/Note]]
> PDF：[[<LIBRARY_ROOT>/<入库类别>/<Year>-<ShortName>/Paper.pdf]]
```

### Paper-zh.md

```markdown
# <原标题>（中文翻译）

（待翻译）
```

### Review.md

```markdown
# <ShortName> — OpenReview

> OpenReview 页面：<url 或 未找到 OpenReview 页面>
> 投稿场次/评分：<或 待补>

（审稿意见正文待补，不自动抓全文）
```

---

## 边界与不做的事

- 不替代 Obsidian PDF annotation 插件做高亮导出
- `Paper-zh.md` 默认只建空骨架，不做全文翻译
- `Review.md` 只放 OpenReview 链接 + 场次/评分，不自动抓全文审稿意见
- 创新点不自动写 `Idea.md`，由用户自己落
- 不做 SM-2 间隔复习（带读 quiz 一次性即时自测，无复习队列）
- 不在 `待读.md` 里自动增删条目，仅可选地标 `✅`
