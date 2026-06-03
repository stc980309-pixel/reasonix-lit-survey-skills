---
name: docx-chinese
description: python-docx中文Word生成避坑：element.rPr为None的正确处理、中文字体设置模板
---

# python-docx 中文 Word 报告生成 — 避坑指南

> 经验来源: 生成文献调研报告时踩坑后总结

---

## 核心坑: `element.rPr` 为 None

### 问题
```python
run = p.add_run('中文文本')
run.font.name = 'Times New Roman'
run.element.rPr.rFonts.set(qn('w:eastAsia'), '宋体')  # ❌ AttributeError: 'NoneType'
```

**原因**: 新创建的 `run` 的 `element.rPr` 尚未初始化。虽然设置了 `font.name`，但 `element.rPr` 仍可能为 None。

### 正确写法

**方法一（推荐）**: 用 `get_or_add_rPr()` 确保 rPr 存在
```python
run = p.add_run('中文文本')
run.font.name = 'Times New Roman'
run._element.get_or_add_rPr().rFonts.set(qn('w:eastAsia'), '宋体')
run.font.size = Pt(11)
run.bold = True
```

**方法二**: 用 `rPr` 属性（python-docx 内部 API）
```python
run = p.add_run('中文文本')
run.font.name = 'Times New Roman'
rPr = run._element.get_or_add_rPr()
rPr_rFonts = rPr.find(qn('w:rFonts'))
if rPr_rFonts is None:
    from lxml import etree
    rPr_rFonts = etree.SubElement(rPr, qn('w:rFonts'))
rPr_rFonts.set(qn('w:eastAsia'), '宋体')
```

### ⚠️ 注意：`style.element.rPr` 不同
```python
style = doc.styles['Normal']
style.font.name = 'Times New Roman'
style.element.rPr.rFonts.set(qn('w:eastAsia'), '宋体')  # ✅ 这个通常没问题
```
`style` 对象的 `element.rPr` 在设置字体后几乎总是已存在的。

---

## 完整模板

```python
# -*- coding: utf-8 -*-
from docx import Document
from docx.shared import Pt, Cm, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.oxml.ns import qn

doc = Document()

# 页面设置
for section in doc.sections:
    section.top_margin = Cm(2.0)
    section.bottom_margin = Cm(2.0)
    section.left_margin = Cm(2.5)
    section.right_margin = Cm(2.5)

# 默认样式 — style.element.rPr OK
style = doc.styles['Normal']
style.font.name = 'Times New Roman'
style.font.size = Pt(11)
style.element.rPr.rFonts.set(qn('w:eastAsia'), '宋体')
style.paragraph_format.line_spacing = 1.3

# 中文段落 — 用 get_or_add_rPr() 安全
def add_para(text, bold=False, size=11, indent=True):
    p = doc.add_paragraph()
    if indent:
        p.paragraph_format.first_line_indent = Pt(22)
    run = p.add_run(text)
    run.font.name = 'Times New Roman'
    run._element.get_or_add_rPr().rFonts.set(qn('w:eastAsia'), '宋体')
    run.font.size = Pt(size)
    run.bold = bold
    return p

# 标题 — heading 的 runs 也需要设置
def add_heading_styled(text, level=1):
    h = doc.add_heading(text, level=level)
    for run in h.runs:
        run.font.name = 'Times New Roman'
        run._element.get_or_add_rPr().rFonts.set(qn('w:eastAsia'), '黑体')
    return h

# 保存
doc.save('output.docx')
```

---

## 通用避坑原则

1. **新 run 用 `get_or_add_rPr()`**: 永远不要假设 `element.rPr` 已存在
2. **全局替换需谨慎**: `element.rPr` → `_element.get_or_add_rPr()` 的替换会破坏 `style.element.rPr.rFonts` 的访问模式
3. **先设置 font.name**: 这确保字体会被正确渲染
4. **提前测试**: 生成前先跑一遍脚本，AttributeError 是最常见的错误

---

## 改 vs 重写：按改动幅度决定

| 改动幅度 | 策略 | 示例 |
|----------|------|------|
| 小改（<30%内容） | `edit_file` 精确修改 | 更新下载状态、修正DOI、替换个别段落 |
| 大改（>50%内容） | 重写脚本，但**复用已验证的框架代码** | 全部10篇论文摘要重写、章节重组 |

**关键**: 无论改动多大，已跑通的 helper 函数和样式设置代码不要动，只改数据/内容部分。本次 v1→v3 的教训是：v2 全量重写连 helper 都改了导致 AttributeError，v3 回退到 v1 框架 + 16 次精确编辑才成功。
