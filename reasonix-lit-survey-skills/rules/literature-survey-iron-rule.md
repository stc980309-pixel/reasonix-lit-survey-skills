---
name: literature-survey-iron-rule
description: 文献调研铁律：白名单期刊、5步流程、下载全部不询问、MCP浏览器下载、Word报告
---
# 文献调研铁律（流程 + 期刊范围）

## 期刊白名单（只搜这些，AFM 是最低级别）
Nature, Science, Nature Communications, Nature Photonics, Nature Materials, Nature Chemistry, Nature Physics, Nature Nanotechnology, Science Advances, Angewandte Chemie, JACS, Advanced Materials (AM), Advanced Energy Materials (AEM), Advanced Functional Materials (AFM)

## 流程铁律（不可跳过任何步骤）
1. **搜索**：scholar-search + web_search，多条并行，不同关键词角度
2. **扩展**：从已知高相关论文的 references/citations 扩展
3. **筛选**：去重、排除无关、优先有明确工艺参数的
4. **下载全部**：找到的所有相关论文**直接下载，不问用户选哪些**。Wiley/ACS/Elsevier → MCP 浏览器 CDP 9223；OA → curl
5. **汇总**：生成 Word 报告（文献表 + 工艺分析 + 推荐方案）

## 下载方法（唯一正确方式）
- Wiley/ACS/Elsevier 等订阅期刊：**必须用 MCP 浏览器工具**（预热主页 → 文章页 → 点 PDF → fetch pdfdirect）
- OA 论文（Nat Commun OA、Sci Adv）：curl 直接下
- 禁止用 Python paper_downloader、禁止 headless Chromium、禁止不预热直接 goto DOI
- CDP 端口 9223，Edge CDP 命令：`"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --remote-debugging-port=9223 --user-data-dir="C:\edge_cdp_mcp"`

## 绝对禁止
- ❌ 下载前问用户"要下载哪些"
- ❌ 搜索非白名单期刊
- ❌ 用 curl/requests 下载 paywall 论文
- ❌ 只下载部分论文
- ❌ 不写 Word 报告

**Why**: 2025-05-30 调研热压厚透明材料，成功走通搜索→筛选→下载12篇→Word报告全流程。MCP浏览器下载6篇Wiley论文全部成功。
**How to apply**: 用户说"调研XX方向"→ 立即按5步流程走，下载所有相关论文，不询问。
