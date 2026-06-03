---
name: pdf-extract
description: PDF智能提取工作流：运行提取器→1次阅读→删除中间txt，省95%token
---

# PDF 智能提取 → 深度阅读工作流

> 核心脚本: `pdf_smart_extract.py`（项目 tools 目录）

---

## 使用流程

### Step 1: 确保 PDF 已下载
```
papers/ 目录下有 .pdf 文件
```

### Step 2: 运行提取器
```bash
python pdf_smart_extract.py <pdf_dir> <output_file>
```
默认输出: `literature/smart_extracts.txt`

### Step 3: 阅读提取结果
仅需 **1 次** `read_file`，~7K tokens（vs 原始全量 ~150K tokens）

提取内容包括:
- 每篇论文的 ABSTRACT（完整摘要）
- INTRODUCTION 核心段落
- CONCLUSION 主要结论
- 自动检测的关键数字（光产额、CTR、衰减时间、PLQY、检测限等）
- Wiley/RSC 导航文本、参考文献、方法细节已自动过滤

### Step 4: 基于提取内容写分析报告
直接基于 smart_extracts.txt 中的关键段落和数据撰写深度分析

### Step 5: ⚠️ 删除中间文件
```
看完即删: smart_extracts.txt
```
这是中间文件，对用户无意义，阅读完立即删除。

---

## Token 节省

| 对比 | 原始方案 | 智能提取 |
|------|:--:|:--:|
| read_file 调用 | 10次 | **1次** |
| Token 消耗 | ~150K | **~7K** |
| 节省 | — | **95%** |

---

## 脚本位置
```
<project>/pdf_smart_extract.py
```
核心依赖: `fitz` (PyMuPDF)。如未安装: `pip install PyMuPDF`

## 提取原理
1. PyMuPDF 读取 PDF 全量文本
2. 关键词定位: 找到 Abstract / Introduction / Conclusion 段落边界
3. 行级过滤: 跳过 Wiley/ACS/RSC 导航菜单、参考文献编号、页码等噪声
4. 正则提取: 自动检测光产额(ph/MeV)、CTR(ps)、衰减(ns/ps/μs)、PLQY(%)等关键数字
5. 输出结构化 txt: 每篇一个区块，摘要→引言→结论→数字
