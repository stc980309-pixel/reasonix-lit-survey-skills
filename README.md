# reasonix-lit-survey-skills
适配中国地质大学（武汉）的师生使用deepseek-reasonix 进行顶级期刊的自动化调研、自动文献下载的skill。可以绕过期刊的认证识别，但是必须要在中国地质大学（武汉）的校园网的环境下运行
# Reasonix 文献调研 + 论文下载技能包

适用于中国地质大学（武汉）校园网环境的 Reasonix 文献调研全流程技能包。

## 适用场景

- 导师说"调研一下XX方向" — 自动搜索→筛选→下载→分析→出Word报告
- 需要下载 Wiley/ACS/Elsevier/RSC 等出版社的付费论文 PDF + SI
- 生成中文文献调研 Word 报告
- PDF 智能提取，省 95% token

## 文件结构

```
reasonix-lit-survey-skills/
├── README.md                           # 本文件
├── skills/                             # 放入 .reasonix/skills/
│   ├── literature-survey.md            # 文献调研7步全流程（主入口）
│   ├── paper-download.md               # 按出版社通用下载策略
│   ├── paper-downloader.md             # 各出版社详细下载代码模板
│   ├── acs-download.md                 # ACS 两步法绕过 Cloudflare
│   ├── wiley-download.md              # Wiley 三步法 pdfdirect 下载
│   ├── pdf-extract.md                  # PDF 智能提取省 token
│   └── docx-chinese.md                 # python-docx 中文 Word 避坑
└── rules/                              # 参考规则（可放入 .reasonix/memory/global/）
    ├── survey-definition.md            # 调研 = 搜索→筛选→下载→汇总
    ├── literature-survey-iron-rule.md  # 期刊白名单 + 流程铁律
    ├── lit-review-standard.md          # 四问分析深度标准
    ├── lit-review-workflow.md          # 7步完整流程
    ├── paper-download-iron-rule.md     # 下载唯一正确方式
    └── paper-download-lessons.md       # CDP 9223 核心教训
```

## 安装方法

### 1. 安装 Skill 文件

将所有 `skills/` 下的 `.md` 文件复制到 Reasonix 的 skills 目录：

```
C:\Users\<你的用户名>\.reasonix\skills\
```

### 2. 安装 Rule 文件（可选）

将 `rules/` 下的 `.md` 文件复制到 Reasonix 的 memories 目录：

```
C:\Users\<你的用户名>\.reasonix\memory\global\
```

### 3. 重启 Reasonix

重启后即可使用。输入 `/literature-survey` 开始文献调研，输入 `/paper-downloader` 下载论文。

## 前置条件

1. **校园网环境**：必须在校园网内（或 VPN 连回校园网），出版社通过 IP 认证
2. **Edge CDP 模式**：启动 Edge 浏览器时需要开启远程调试端口

   ```powershell
   # 关闭所有 Edge 窗口后执行：
   & "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --remote-debugging-port=9223 --user-data-dir="C:\edge_cdp_mcp"
   ```

3. **Python 环境**（仅 pdf-extract 和 docx-chinese 需要）：
   - PyMuPDF：`pip install PyMuPDF`
   - python-docx：`pip install python-docx`

## 期刊白名单

Nature / Science / Nature Communications / Nature Photonics / Nature Materials / Nature Chemistry / Nature Physics / Nature Nanotechnology / Science Advances / Angewandte Chemie / JACS / Advanced Materials / Advanced Energy Materials / Advanced Functional Materials

## 下载策略速查

| 出版社 | 方法 | 状态 |
|--------|------|------|
| **Wiley** (AM, AFM, Angew, etc.) | 三步法：abstract → ePDF → pdfdirect fetch | ✅ 已验证 |
| **ACS** (JACS, Nano Lett., etc.) | 两步法：主页建 cookie → 文章页点 PDF | ✅ 已验证 |
| **Nature** OA | curl nature.com/articles/{id}.pdf | ✅ 直接 |
| **Elsevier** / ScienceDirect | 浏览器点击 PDF 按钮 | ⚠️ JS 重 |
| **RSC** (Chem. Sci., etc.) | 优先 PMC OA | ⚠️ 部分 |
| **Nature** / **Science** 订阅版 | 查 ResearchSquare / arXiv 预印本 | ⚠️ 有限 |

## 工作流程

```
用户说"调研 XX 方向"
    │
    ├─ Step 0: 理解用户体系（读论文/数据/参数）
    ├─ Step 1: 诊断根因（对比文献参数找差距）
    ├─ Step 2: 搜索文献（scholar-search + web_search）
    ├─ Step 3: 筛选（白名单期刊，去重，优先有参数）
    ├─ Step 4: 下载全部 PDF + SI（不询问用户）
    ├─ Step 5: 对比分析（用户 vs 文献参数表）
    ├─ Step 6: 提出可执行方案
    └─ Step 7: 输出 Word 报告
```

## 注意事项

- **不要用 curl 下载付费论文**：Reasonix shell 在云端，不在校园网内。必须用 MCP 浏览器 + CDP 9223
- **下载全部论文，不询问用户**：找到的所有相关论文直接下载
- **先预热再下载**：先导航到出版社首页建 session，再访问文章页
- **CDP 端口固定 9223**：这是 Reasonix Playwright MCP 工具硬编码的端口
- **文件命名规范**：`{year}_{JournalAbbrev}_{FirstAuthor}_{short_title}_{doi_suffix}/`

## 贡献

欢迎提交 Issue 和 PR 改进下载策略或增加新出版社支持。

## 许可

本技能包仅供学术用途。下载论文需要合法的校园网订阅权限，请遵守各出版社的使用条款。
