---
name: paper-download-iron-rule
description: 文献下载铁律：必须用 Playwright MCP 浏览器工具，禁止 Python/curl，CDP 9223，先预热再下载
---
# 文献下载铁律

**以后任何涉及下载学术论文的操作，必须且只能使用以下方法：**

## 唯一正确方式

1. 确保 Edge CDP 在端口 9223 运行（没有就 `run_background` 启动）
2. 用 `playwright_browser_*` MCP 工具（navigate、click、run_code_unsafe）
3. 先逛出版社首页预热 → 再进文章页 → 按出版社策略下载

## 绝对禁止

- ❌ 写 Python 脚本调用 Playwright
- ❌ 用 curl / requests / web_fetch 下载 paywall 论文
- ❌ 启动 headless Chromium
- ❌ 直接 goto DOI 不预热
- ❌ 用 paper_downloader/main.py（那是给用户手动用的 CLI 工具，不是 Reasonix 自己用的）

## 关键事实

- CDP 端口：9223（不是 9222）
- Edge 路径：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
- PDF 保存路径：`.playwright-mcp/` → `copy_file` 到目标文件夹
- 出版社策略参考：skill `paper-downloader`、skill `wiley-download`、skill `acs-download`

**Why**: 2025-05-30 浪费数小时用 Python Playwright + headless 模式试图下载 Wiley 论文，全部被 Cloudflare 拦截。最终发现用 MCP 浏览器 + 真实 Edge + 预热 + 9223 端口三步法 成功下载 Angewandte 论文（main.pdf 1.4MB + SI 765KB）。任何偏离此方法的行为 = 重蹈覆辙。

**How to apply**: 用户说"下载这篇论文"→ 立即检查 Edge CDP 9223 → playwright_browser_navigate 预热 → navigate DOI → 按出版社策略下载 → copy_file 到 papers/。如果 playwright_browser_navigate 报 ECONNREFUSED ::1:9223 → run_background 启动 Edge CDP。永远不要回到 Python/curl 路线。
