---
name: lit-review-workflow
description: 文献调研标准流程：搜索→筛选顶刊→下载正文+SI→总结→生成Word报告
---
# 文献调研标准流程

## 步骤
1. **搜索**: 用 scholar-search MCP (Semantic Scholar + arXiv) 搜索目标方向，限定顶刊
2. **筛选**: 只看 Nature/Science/Nature Photonics/Nature Materials/Nature Nanotechnology/Nature Chemistry/Nature Physics/Nature Communications/Science Advances/Advanced Materials/JACS/Angewandte Chemie/AEM/AFM 级别
3. **获取详情**: 对每篇获取完整摘要、作者、引用数、DOI
4. **下载正文+SI**: 尝试获取 PDF 全文和 Supporting Information，下载到 `literature/papers/`
5. **总结**: 每篇 2-3 句话总结做了什么 + 关键性能指标
6. **生成 Word**: 输出标准格式文献调研报告（含下载状态标记）
7. **标记未下载**: 下载失败的在 Word 中明确标注 ❌ 未下载

## 顶刊定义
Nature, Science, Nature Photonics, Nature Materials, Nature Nanotechnology, Nature Chemistry, Nature Physics, Nature Communications, Science Advances, Advanced Materials (AM), JACS, Angewandte Chemie, AEM, AFM

## 文件存放
- 文献 PDF: `{project}/literature/papers/`
- 调研报告: `{project}/literature/`
