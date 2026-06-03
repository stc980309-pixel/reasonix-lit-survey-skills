---
name: paper-download
description: 顶刊论文下载技法：pdfdirect绕过Cloudflare、PMC OA、arXiv预印本，记录于Sala 2024成功下载经验
---

# 顶刊论文下载技法 (Playwright MCP 浏览器模式)

> 适用场景：校园网环境 + Playwright MCP extension 模式  
> 前置条件：`/skill superpowers` 或 `/skill gstack` 激活方法论后再调用本 skill

---

## 核心原则

**不要用 curl** — Reasonix shell 在 AWS 云端，不在校园网内  
**用 Playwright extension 模式** — 浏览器的网络出口在你的本机，走校园网IP认证

---

## 按期刊分类的下载策略

### 1. arXiv 预印本 → 直接 curl ✅

```
curl -L -o <output.pdf> "https://arxiv.org/pdf/<arxiv-id>" -H "User-Agent: Mozilla/5.0"
```

arXiv 无 Cloudflare，curl 直接可用，不需要浏览器。

### 2. Wiley → 三步法：abstract→epdf→fetch pdfdirect ✅

> **详细技法**: `/skill wiley-download` — 已验证 2024-2026 全部 Wiley 论文

**关键发现**: 直接用 pdfdirect 只对 2024 年前论文有效。2025-2026 论文需要三步法：

1. **导航到 abstract 页** → 建立 session + 校园网 IP 认证（确认"China University Of Geosciences"出现）
2. **点击 PDF 按钮** → 进入 ePDF viewer 页面
3. **fetch pdfdirect** → 浏览器内 fetch 完整 PDF，触发下载到 Downloads

**代码模板见**: `/skill wiley-download`

**覆盖期刊**: Adv. Mater., Adv. Funct. Mater., Adv. Sci., Laser Photon. Rev., Angewandte, Small 等全部 Wiley 期刊

### 3. ACS (JACS, Nano Lett., ACS Nano 等) → 两步法绕过 Cloudflare ✅

> **详细技法**: `/skill acs-download` — 已验证 Dong 2026 JACS

**关键发现**: ACS Cloudflare 不拦截所有页面——先访问 `pubs.acs.org` 主页建立 cookie，再访问文章页即可绕过。

1. **导航到 ACS 主页** → `https://pubs.acs.org`，建立 session
2. **导航到文章页** → `https://pubs.acs.org/doi/abs/10.1021/<article-id>`
3. **确认校园网认证** → 看到 "Access provided by CHINA UNIV GEOSCIENCES" + "Subscribed"
4. **点击 Open PDF** → 新标签页打开 PDF
5. **切换标签 + fetch 下载** → 文件保存到 Downloads

### 4. RSC (Chem. Sci., Chem. Commun. 等) → 优先查 PMC OA

RSC 的 PDF 直链格式多变。先查是否在 PubMed Central 有 OA 版本：
- PMC URL: `https://www.ncbi.nlm.nih.gov/pmc/articles/PMC<id>/pdf/`
- 如果有 OA 许可（CC-BY），从 PMC 下载
- 如果 PMC 有 reCAPTCHA，也标记手动下载

### 5. Nature 系列 → ResearchSquare / arXiv

Nature Photonics 等论文经常在 ResearchSquare 或 arXiv 有预印本：
- 优先查 author preprint 版本
- `get_paper_details` 中的 `openAccessPdf` 字段会标出 OA 状态

---

## 通用下载检查清单

每篇论文按以下优先级尝试：

```
1. openAccessPdf.url 有值？ → 直接 curl 下载（OA论文）
2. arXiv 有预印本？      → curl arxiv.org/pdf/{id}
3. Wiley 期刊？          → pdfdirect 绕过 Cloudflare（见上）
4. RSC + CC-BY？         → PMC OA 下载
5. 都不行                → ❌ 标记手动下载，给用户直接链接
```

---

## 文件管理

- 下载到 `Downloads` 后立即 `copy_file` 到项目目录
- 文件命名格式：`<FirstAuthor>_<Year>_<JournalAbbrev>_<Keywords>.pdf`
- 示例：`Sala_2024_AdvFunctMater_HfO2-BGO.pdf`
