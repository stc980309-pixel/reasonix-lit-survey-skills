---
name: literature-survey
description: 文献调研全流程：理解问题→诊断根因→搜索→下载全部→对比分析→方案→Word报告。只搜Nature/Science/子刊/Angew/JACS/AM/AEM/AFM。下载所有论文不询问。每次启动自动加载。
---
# 文献调研标准化流程

## "调研"的定义

**调研 ≠ 搜论文下载。调研 = 理解问题 → 诊断根因 → 文献佐证 → 提出方案 → 输出报告。**

用户说"调研XX"，不是要一堆 PDF 列表，而是要你：
1. 先搞懂他们在做什么、卡在哪
2. 找到卡住的根本原因
3. 用文献数据佐证你的诊断
4. 给出可执行的解决方案

## 铁律

- **期刊范围**：仅限 Nature、Science、Nature Communications、Nature Photonics、Nature Materials、Nature Chemistry、Nature Physics、Nature Nanotechnology、Science Advances、Angew、JACS、AM、AEM、AFM
- **下载策略**：所有相关论文全部直接下载，不询问
- **下载方法**：Wiley/ACS/Elsevier → MCP 浏览器 CDP 9223；OA → curl
- **先理解再搜索**：先读用户论文/数据，诊断问题，再搜文献

## 标准流程（7步）

### Step 0: 理解用户体系（新增！最重要！）
- 读用户论文、毕业论文、实验数据
- 搞清楚：什么材料、什么工艺、什么参数、卡在哪
- 提取关键参数：Tg、Td、热压温度/压力/时间、膜厚、透过率等

### Step 1: 诊断根因
- 把用户参数和文献中成功案例的参数对比
- 找出差距（如"60°C vs 文献185°C"、"Tg+20°C vs 标准Tg+50-100°C"）
- 这个诊断比下载100篇论文都重要

### Step 2: 搜索文献
多条并行搜索，覆盖不同角度：
```
scholar-search_search_papers + venue 筛选
web_search 补充检索
```
从已知高相关论文的 references/citations 扩展。

### Step 3: 筛选
- 必须来自白名单期刊
- 标题/摘要与用户目标相关
- 优先有明确工艺参数的
- 去重

### Step 4: 下载全部（不询问）
OA 论文 → curl；Wiley/ACS/订阅 → MCP 浏览器三步法（预热主页→文章页→点PDF→fetch pdfdirect）。

### Step 5: 对比分析
把用户参数和文献参数放在同一张表里对比，直观看差距。

### Step 6: 提出可执行方案
- 按优先级排列（首选→备选）
- 每条含具体参数（温度、压力、时间、模具、配方）
- 含常见失败模式及对策

### Step 7: 输出 Word 报告
1. 问题诊断（为什么现在不行）
2. 方案对比表
3. 实验计划（第1/2/3天做什么）
4. 失败模式与对策
5. 参考文献

## 关键注意事项

- 不要一上来就搜论文 → 先理解用户体系
- 用户说"用不上" → 说明你没理解他的体系
- CDP 端口：9223；Edge：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
- 文件夹命名：`{year}_{JournalAbbrev}_{FirstAuthor}_{short_title}_{doi_suffix}`
