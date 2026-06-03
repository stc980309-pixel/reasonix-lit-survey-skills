---
name: acs-download
description: ACS论文自动下载技法：先逛主页建cookie→再访问文章页绕过Cloudflare，已验证Dong 2026 JACS
---

# ACS 论文自动下载技法

> **核心发现**: ACS Cloudflare 不拦截所有页面——先访问 `pubs.acs.org` 主页建立 cookie，再访问文章页即可绕过。

---

## 两步法（已验证 Dong 2026 JACS）

### Step 1: 导航到 ACS 主页建立 session
```
playwright_browser_navigate → https://pubs.acs.org
```
- 等待页面加载，建立 cookie/session
- 不需要任何操作，只需访问一次

### Step 2: 导航到文章并下载 PDF
```
playwright_browser_navigate → https://pubs.acs.org/doi/abs/10.1021/<article-id>
```
- 确认看到 "Access provided by CHINA UNIV GEOSCIENCES"
- 确认看到 "Subscribed" 标签
- 点击 "Open PDF" 按钮（新标签页打开）
- 切换到 PDF 标签页，fetch 并触发下载到 Downloads

### Step 3: 迁移文件
```
copy_file: Downloads/<file>.pdf → project/literature/papers/
```

---

## 关键原则
- **必须先访问主页**，直接访问文章页会触发 Cloudflare
- 文章页 URL 用 `/doi/abs/` 而非 `/doi/10.1021/...`
- PDF 在新标签页打开，需要切换标签

## 适用期刊
全部 ACS 旗下：JACS, Nano Lett., ACS Nano, Chem. Mater., Chem. Rev. 等
