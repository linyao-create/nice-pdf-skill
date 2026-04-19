---
name: nice-pdf
description: 将 Markdown 或文字内容转换为精美的、可打印的 HTML 文件，用户可直接用浏览器导出 PDF。自动应用专业排版、配色、字体，并智能规划分页避免内容截断。适用于产品方案、技术文档、报价单等需要精美呈现的文档。
---

# Nice PDF - 文档转精美 HTML 工作流

用户提供 Markdown 文件路径或文字内容，要求生成可打印的 HTML。你需要生成专业排版的 HTML 文件，用户可直接在浏览器中打开并导出为 PDF。

## 核心原则

1. **先规划再动手**：生成 HTML 前必须先列出所有内容块、估算高度、规划分页
2. **遵循 frontend-design 设计原则**：避免 Inter/Roboto/Arial，使用有特色的字体组合
3. **确保 PDF 导出质量**：颜色不变灰、内容不截断、留白合理

## 工作流程

### Step 1: 读取并分析内容

- 读取用户提供的 MD 文件或文字内容
- 识别文档结构：标题层级、章节数量、表格、列表、流程图等
- 确定文档类型（产品方案、技术文档、报价单等）

### Step 2: 确定设计方向

参考 frontend-design skill 原则，选择一个**明确的设计方向**：

- **字体组合**：避免 Inter、Roboto、Arial、Space Grotesk
  - 推荐：Playfair Display（标题）+ DM Sans（正文）+ Crimson Pro（副标题）
  - 或其他有特色的组合

- **配色方案**：选择符合文档主题的配色
  - 暖色系（已验证）：`#FFF8F0, #E07A5F, #81B29A, #3D5A80, #2B2D42, #F4ACB7`
  - 冷色系/商务风/科技感等根据内容选择

- **视觉风格**：现代简约 / 杂志风 / 商务专业 / 温暖亲和等

### Step 3: 内容块高度估算（必做）

**关键：不要靠猜来分页，必须先算清楚每块高度。**

列出所有内容块并估算高度（参考下表）：

| 组件类型 | 估算高度 |
|---------|---------|
| h1 标题 + section-label | ~60px |
| h2 标题 | ~45px |
| callout（提示框） | ~55px |
| divider（分隔线） | ~42px |
| 6格模块卡片（2列×3行） | ~380px |
| 横向流程图（单行8步） | ~90px |
| info-table 表头 | ~50px |
| info-table 每行 | ~50px |
| price-table（6行） | ~320px |
| 2列卡片（如费用说明） | ~180px |
| 段落文字（每行） | ~24px |

**分页规则**：
- A4 页面可用高度：**1022px**（297mm - 上下 padding 各 45px）
- 每页内容总高度应在 900–1022px 之间
- 带 `page-break-inside: avoid` 的块（表格、卡片、流程图）是整体跳页的，必须确保它们能放进当前页
- 如果某块放不下，提前换页，不要让它被截断

**输出示例**：
```
第 01 页（封面）：固定 297mm

第 02 页内容规划：
- h1 + section-label: 60px
- callout: 55px
- divider: 42px
- 6格模块卡片: 380px
- divider: 42px
- h1 + section-label: 60px
- 3条流程图: 90px × 3 = 270px
总计: ~909px ✓

第 03 页内容规划：
- h1 + section-label: 60px
- info-table (表头 + 6行): 50 + 300 = 350px
- divider: 42px
- h1 + section-label: 60px
- price-table: 320px
总计: ~832px ✓
```

### Step 4: 生成 HTML

基于分页规划生成 HTML，必须包含以下关键 CSS：

#### 必备 CSS 规则

```css
/* 字体导入 */
@import url('https://fonts.googleapis.com/css2?family=Crimson+Pro:wght@400;600;700&family=DM+Sans:wght@400;500;700&family=Playfair+Display:wght@700;900&display=swap');

/* CSS 变量 */
:root {
    --warm-cream: #FFF8F0;
    --terracotta: #E07A5F;
    --sage: #81B29A;
    --navy: #3D5A80;
    --charcoal: #2B2D42;
    --soft-peach: #F4ACB7;
}

/* 页面基础 */
.page {
    width: 210mm;
    min-height: 297mm;
    margin: 0 auto;
    background: var(--warm-cream);
    position: relative;
    page-break-after: always;
    padding: 45px 50px;  /* 不要超过 50px */
}

/* PDF 导出关键 CSS（必须加，否则颜色变灰）*/
@media print {
    * {
        -webkit-print-color-adjust: exact !important;
        print-color-adjust: exact !important;
        color-adjust: exact !important;
    }
    .page { margin: 0; page-break-after: always; }
    h1, h2, h4 { page-break-after: avoid; }
    .card, .callout, .flow-row, .info-table, .price-table, .price-highlight {
        page-break-inside: avoid;
    }
    ul { page-break-inside: avoid; }
}

@page { size: A4; margin: 0; }
```

#### 紧凑间距（适合内容多的文档）

```css
h1 { font-size: 36px; margin-bottom: 20px; }
h2 { font-size: 24px; margin: 20px 0 12px 0; }
.divider { margin: 22px 0; }
.callout { padding: 15px 20px; margin: 15px 0; }
.card { padding: 18px; }
.info-table th, .info-table td { padding: 14px 18px; }
```

#### 常用组件模板

**6格模块卡片（2列）**：
```html
<div class="module-grid">  <!-- grid-template-columns: repeat(2, 1fr); gap: 16px -->
    <div class="card">
        <h4>模块名称</h4>
        <span class="tag">角色定位</span>
        <ul><li>功能点</li></ul>
    </div>
</div>
```

**横向流程图（8步单行）**：
```html
<div class="flow-row">  <!-- display: flex; align-items: center -->
    <div class="flow-step">
        <div class="flow-step-number">1</div>
        <div class="flow-step-text">步骤名</div>
    </div>
    <div class="flow-arrow">→</div>
    <!-- 重复 -->
</div>
```

**信息表格**：
```html
<table class="info-table">
    <thead><tr><th>列1</th><th>列2</th></tr></thead>
    <tbody>
        <tr><td><strong>标签</strong></td><td>内容</td></tr>
    </tbody>
</table>
```

### Step 5: 输出说明

生成 HTML 后，告诉用户：

1. 文件路径
2. 如何导出 PDF：
   - 用浏览器打开 HTML 文件
   - Cmd+P 打印
   - 勾选「背景图形 / Background graphics」
   - 保存为 PDF
3. 如果需要长图，可以用 Playwright 截图（提供命令）

## 设计规范速查

### 封面设计

```html
<div class="page cover">
    <div class="cover-header">
        <div class="cover-label">产品开发方案 · 2026</div>
    </div>
    <div class="cover-main">
        <div class="cover-title">标题</div>
        <div class="cover-subtitle">副标题</div>
    </div>
    <div class="cover-footer">
        <div class="cover-footer-text">底部文字</div>
    </div>
</div>
```

### 内容页结构

```html
<div class="page">
    <div class="page-number">01</div>
    <div class="section-label">SECTION NAME</div>
    <h1>一、章节标题</h1>
    <!-- 内容 -->
</div>
```

## 避免踩坑

1. **不要用 `height: 297mm`**，用 `min-height`，让页面自然撑开
2. **所有表格、卡片、流程图都要加 `page-break-inside: avoid`**
3. **标题要加 `page-break-after: avoid`**，避免标题单独留在页尾
4. **分页前必须先估算高度**，不要靠感觉移来移去
5. **`page-break-inside: avoid` 的块是整体跳页的**，分页时要把它们当作整体计算

## 导出长图（可选）

如果用户需要长图（如发朋友圈），提供以下命令：

```bash
# 1. 启动本地服务器
cd "文件所在目录" && python3 -m http.server 8765 &
sleep 2

# 2. 用 Playwright 截图（fullPage: true）
# 保存到同目录下的 xxx_长图.png

# 3. 关闭服务器
lsof -ti:8765 | xargs kill -9
```

注意：Playwright 不能访问 file:// 协议，必须通过 http 服务器。

## 总结

这个 skill 的核心价值是**避免反复返工**：

- ✅ 先规划再动手（内容块 + 高度估算 + 分页方案）
- ✅ 遵循设计规范（字体、配色、间距）
- ✅ 确保 PDF 质量（颜色、分页、留白）

生成的 HTML 应该是**一次到位、开箱即用**的，用户直接打开浏览器就能导出完美的 PDF。
