# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

2026届四川省高考志愿参考工具，聚焦**师范类院校**近三年（2023–2025）录取分数线排名，部署在 GitHub Pages：https://github.com/anlen123/2026_gaokao_zhiyuan

## 开发与部署

本项目是**纯静态 HTML**，无构建工具、无框架、无 npm/package.json。

```bash
# 本地预览（任选一种）
open index.html                     # 直接用浏览器打开
python3 -m http.server 8080         # 本地起 HTTP 服务

# 部署：push 到 main 分支即自动触发 GitHub Pages 发布
git push origin main
```

`.nojekyll` 文件确保 GitHub Pages 不启用 Jekyll 处理。

## 页面结构与导航关系

共 4 个 HTML 文件，两条轴互相交叉：

| 文件 | 科类 | 范围 |
|---|---|---|
| `index.html` | 文科（历史类） | 完整排名（公办 7 所 + 省外民办 5 所）+ 2026年个人成绩分析 + 预测模型 |
| `gongban.html` | 文科（历史类） | 省内公办 7 所详情 + 文科可选专业一览 + 各校招生专业详情 + 2026预测 |
| `like.html` | 理科（物理类） | 完整排名（公办 7 所 + 省外民办 5 所） |
| `like_gongban.html` | 理科（物理类） | 省内公办 7 所详情 |
| `quanguo.html` | 文科（历史类） | 全国公办师范院校总览（约45所），含双一流9所、省属重点约20所、一般公办约15所，数据含估算 |

**页面间导航（sticky 顶部导航栏）**：
- 文科完整 ↔ 文科公办 ↔ 理科切换
- 理科完整 ↔ 理科公办 ↔ 文科切换

每个页面顶部均有 `position:sticky; z-index:100` 的导航条，修改导航时需在 **4 个文件中同步更新**。

## 代码架构约定

### CSS 组织
- 所有样式**内联在各 HTML 文件的 `<style>` 块**中，无独立 CSS 文件。
- 颜色通过 CSS 变量统一，定义在 `:root`：
  - 公办用**蓝色**系：`#2563eb`（`--pub-accent` / `--primary`）
  - 民办用**红色**系：`#dc2626`（`--pri-accent`）
  - 警示/本科线用**金色**：`#f59e0b`（`--gold`）
- `index.html` 样式较展开（便于阅读），`like.html` 样式经过压缩，两者逻辑等效。

### 数据编码
- **所有分数线数据硬编码在 HTML `<table>` 标签内**，无 JS 数据层。
- 数据年份覆盖：2023、2024、2025 年。
- 2023–2024 为旧高考（文科/理科），2025 为新高考（历史类/物理类）。
- 每行数据包含：学校名、批次/校区、2025分、2024分、2023分、与省控线差值。

### 关键 CSS 类名约定
- `.pub-row` / `.row-pub` — 公办院校行，左侧蓝色边框
- `.pri-row` / `.row-pri` — 民办院校行，左侧红色边框
- `.sep-row` — 公办/民办分隔行（黄色背景虚线边框）
- `.rank-badge` — 排名圆形徽章（1/2/3名特殊配色）
- `.tag-key` — 绿色"重点"标签，`.tag-pub`/`.tag-pri` — 蓝/红属性标签
- `.summary-card` — 顶部统计卡片
- `.detail-card` — 各校专业详情卡片（仅 `gongban.html`）
- `.highlight-box` — 黄色提示框，`.info-box` — 蓝色信息框

### 数据来源标注
表格内以 `<span class="source-official">✅ 官方数据</span>` 或 `<span class="source-third">⚠️ 第三方</span>` 标注数据来源。

## 数据更新指引

新增/修改分数线数据时注意：
1. 文理两套页面的**同一所学校需同步更新**（`index`+`gongban` 为文科，`like`+`like_gongban` 为理科）。
2. 完整排名页（`index.html`、`like.html`）同时含公办和民办；公办专题页（`gongban.html`、`like_gongban.html`）仅含公办，并额外有专业详情。
3. 页眉统计卡片（`.summary-card`）中的"最高分"数值在修改表格后需手动同步。
4. 省控本科线单独一列，历年数据：2025年文科438/理科438、2024年文科452（二本）/理科459（二本）、2023年历史类同步更新。
