---
name: wiley-download
description: Wiley论文自动下载三步法：abstract建session→点PDF→fetch pdfdirect，已验证2024-2026全部Wiley论文
---

# Wiley 论文自动下载技法

> **核心发现**: Wiley HTML 页有 Cloudflare，但 PDF 没有。需要先建 session 再取 PDF。

---

## 三步法（已验证 2024-2026 全部 Wiley 论文）

### Step 1: 导航到 abstract 页面建立 session
```
playwright_browser_navigate → https://onlinelibrary.wiley.com/doi/{DOI}
```
- 等待页面加载完成
- 确认看到学校名称（如 "China University Of Geosciences"）= IP 认证通过
- 确认看到 "Full Access" 标签

### Step 2: 点击 PDF 按钮进入 ePDF 视图
```js
page.getByRole('link', { name: 'PDF', description: 'ePDF', exact: true }).click()
```
- 等待 3-4 秒让 ePDF viewer 完全加载

### Step 3: 通过 fetch pdfdirect 下载
```js
const pdfUrl = page.url().replace('/epdf/', '/pdfdirect/');
// 在浏览器中 fetch 并触发下载
const blob = await fetch(pdfUrl).then(r => r.blob());
const a = document.createElement('a');
a.href = URL.createObjectURL(blob);
a.download = '<filename>.pdf';
a.click();
```
- 文件保存到 `C:\Users\Admin\Downloads\`
- 然后用 `copy_file` 移到项目 `literature/papers/`

---

## 为什么必须三步
- **不能跳过 abstract 页**: 直接访问 epdf/pdfdirect 会被重定向到 abstract 或被 403
- **不能用 curl**: Reasonix shell 在 AWS 云端，不在校园网
- **extension 模式是核心**: 浏览器的网络出口 = 你的本机 = 校园网 IP

---

## 适用期刊
所有 Wiley 旗下：
- Advanced Materials / Advanced Functional Materials
- Advanced Science
- Laser & Photonics Reviews
- Angewandte Chemie
- Small / Advanced Optical Materials 等

---

## ⚠️ 已知限制
- ACS (JACS, Nano Lett.) → 用两步法可下载，参见 `acs-download` skill（已验证 Dong 2026 JACS）
- RSC → 优先查 PMC OA，否则手动
- Nature/Science → 查 ResearchSquare/arXiv 预印本，否则手动
- 论文太新 (<1周) → epdf 可能只有封面，等待正式上线

---

## 完整代码模板
```js
async (page) => {
  // Step 1: navigate to abstract (caller does this)
  // Step 2: click PDF
  const pdfLink = page.getByRole('link', { name: 'PDF', description: 'ePDF', exact: true });
  await pdfLink.click();
  await page.waitForTimeout(3000);

  // Step 3: fetch pdfdirect and trigger download
  const pdfUrl = page.url().replace('/epdf/', '/pdfdirect/');
  const size = await page.evaluate(async (url) => {
    const r = await fetch(url);
    const blob = await r.blob();
    const objUrl = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = objUrl;
    a.download = 'FILENAME.pdf';  // ← 改这里
    a.click();
    return blob.size;
  }, pdfUrl);

  return size + ' bytes';
}
```
