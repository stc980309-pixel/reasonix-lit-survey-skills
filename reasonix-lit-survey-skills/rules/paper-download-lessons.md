---
name: paper-download-lessons
description: Paper download via Playwright MCP: CDP port 9223, warm-up first, use existing skills not Python scripts
---
# Paper 下载核心教训 (2025-05-30)

## 黄金法则：永远用 Playwright MCP 工具，不要写 Python 脚本

**错**：写 `paper_downloader/` Python 项目用 Playwright 启动 headless Chromium → Cloudflare 拦截
**对**：用 Reasonix 内置的 `playwright_browser_*` MCP 工具连用户真实 Edge（CDP port 9223）

## 技术关键点

1. **CDP 端口必须是 9223**（MCP 工具硬编码），不是 9222
2. **必须预热**：先 `playwright_browser_navigate` 到出版社首页建 session，再进文章页
3. **Wiley 三步法**（已验证可行）：
   - `playwright_browser_navigate` → `https://onlinelibrary.wiley.com`（预热）
   - `playwright_browser_navigate` → `https://doi.org/{DOI}`（文章页）
   - `page.getByRole('link', { name: 'PDF', description: 'ePDF', exact: true }).click()`
   - `page.url().replace('/epdf/', '/pdfdirect/')` → fetch 下载
4. **ACS 两步法**：先 `pubs.acs.org` 主页建 cookie → 再文章页 `doi/abs/{DOI}` → 点 PDF
5. **Nature OA**：直接 curl `nature.com/articles/{id}.pdf` 即可
6. 文件下载到 `.playwright-mcp/` 后 `copy_file` 到论文文件夹
7. 用户 Edge 在 `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`

## 已有 Skill 存档（务必先查阅）

- `.reasonix/skills/wiley-download.md` — Wiley 三步法完整技法
- `.reasonix/skills/acs-download.md` — ACS 两步法
- `.reasonix/skills/paper-download.md` — 通用下载策略

## 启动 Edge CDP 的命令

```
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --remote-debugging-port=9223 --user-data-dir="C:\edge_cdp_mcp"
```

## 核心教训

**在做任何文献下载之前，先查已有 skills，然后用 MCP 浏览器工具，不要重新发明轮子。**
Python paper_downloader 项目只能作为 CLI 工具给用户手动用，Reasonix 自己下载必须走 MCP 浏览器。
