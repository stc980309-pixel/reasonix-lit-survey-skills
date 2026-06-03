---
name: paper-downloader
description: 学术论文自动下载：用 Playwright MCP 浏览器 + 校园网权限下载 PDF + SI。支持 Wiley/ACS/Nature/Elsevier/RSC/Springer 等。每次启动自动加载。
---
# Paper Downloader — Reasonix 内置下载流程

## 核心原则

**必须用 `playwright_browser_*` MCP 工具，不能用 Python/curl/requests。**
浏览器出口 = 用户本机 = 校园网 IP = 出版社认证通过。

## 前置条件

Edge 必须以 CDP 模式运行在端口 9223：

```
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --remote-debugging-port=9223 --user-data-dir="C:\edge_cdp_mcp"
```

如果 `playwright_browser_navigate` 报 `connect ECONNREFUSED ::1:9223`，说明 Edge CDP 没启动。用 `run_background` 启动它。

## 通用流程

```
1. 预热：playwright_browser_navigate → 出版社首页（建 session，过 Cloudflare）
2. 文章：playwright_browser_navigate → doi.org/{DOI}（自动跳转出版社）
3. 下载主 PDF（按出版社策略）
4. 搜索 SI（Supporting Information / Supplementary）
5. copy_file 从 .playwright-mcp/ → papers/{folder}/
6. 写 metadata.json + download_status.txt
```

## 按出版社策略

### Wiley（三步法，已验证 ✅）
```
Step 1: playwright_browser_navigate → https://onlinelibrary.wiley.com (预热)
Step 2: playwright_browser_navigate → https://doi.org/{DOI}
Step 3: page.getByRole('link', { name: 'PDF', description: 'ePDF', exact: true }).click()
Step 4: 等待 4s，替换 /epdf/ → /pdfdirect/，fetch 下载
Step 5: SI: 回文章页，找 `downloadSupplement` 或 `sup-` 链接
```

### ACS（两步法，已验证 ✅）
```
Step 1: playwright_browser_navigate → https://pubs.acs.org (预热)
Step 2: playwright_browser_navigate → https://pubs.acs.org/doi/abs/{DOI}
Step 3: 确认 "Access provided by CHINA UNIV GEOSCIENCES"
Step 4: 点击 "Open PDF" → 新 tab 打开 → fetch 下载
```

### Nature（OA 直接 curl ✅，订阅用浏览器）
```
OA: curl nature.com/articles/{id}.pdf 即可
SI: static-content.springer.com/esm/art%3A{doi}/MediaObjects/
订阅: 用浏览器点击 "Download PDF"
```

### Elsevier / ScienceDirect
```
先点 "Download PDF" 或 "View PDF"
SI: 找 mmc1.pdf/mmc2.docx 或 "Supplementary material"
JS 重，必须浏览器点击
```

### RSC / Springer / Science / MDPI / Frontiers
```
通用策略：扫描 href 中 .pdf / /pdf/ 链接，fetch 下载
SI: 扫描 Supplementary/Supporting/ESI 关键词
```

### Generic（未知出版社）
```
1. 扫描所有 <a href> 含 pdf 的链接
2. 扫描 meta citation_pdf_url
3. 扫描 supplementary/supporting/data 链接
```

## SI 搜索关键词

```
Supplementary Information, Supporting Information, Supplementary Material,
Supplementary Data, Supplemental Material, Peer Review File, ESI,
mmc1.pdf, mmc2.docx, suppl_file, downloadSupplement
```

## 文件命名

```
{year}_{JournalAbbrev}_{FirstAuthor}_{short_title}_{doi_suffix}/
├── main.pdf
├── SI.pdf (或 SI_01.pdf, SI_02.xlsx ...)
├── metadata.json
├── download_status.txt
└── source_url.txt
```

## 下载代码模板

```js
// 通用 fetch 下载（在 playwright_browser_run_code_unsafe 中使用）
async (page) => {
  const result = await page.evaluate(async (url) => {
    const r = await fetch(url);
    if (!r.ok) return JSON.stringify({error: r.status});
    const blob = await r.blob();
    const objUrl = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = objUrl;
    a.download = 'FILENAME.pdf';
    a.click();
    URL.revokeObjectURL(objUrl);
    return JSON.stringify({size: blob.size, type: blob.type});
  }, url);
  return result;
}
```

## 失败处理

- 截图 + 保存 HTML 快照
- 写明具体失败原因（不是笼统"下载失败"）
- 常见失败：无订阅、Cloudflare 拦截、验证码、登录墙
- 不绕过付费墙，不用 Sci-Hub

## 重要提醒

- 每篇间隔 5-15 秒
- 同出版社连续超过 10 篇暂停确认
- 不保存用户账号密码
- 遇到登录/验证码暂停让用户手动处理
