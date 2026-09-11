# Demand Review — Cycle 对比分析 App

**版本:** v1.0 | **最后更新:** 2026-09 | 

---

## 概述

本应用用于跨 Demand Review Cycle 对比 BDCT 各省份及 BDCT HQ 的产品需求变化。每一轮 Cycle 代表一次月度需求审查（如 202601 = 2026年1月审查），应用支持对比多个 Cycle 之间同一产品/省份的需求量差异，帮助团队快速识别需求波动、趋势异常和版本间变化。

---

## 数据来源

| Cycle | 源文件 | Sheet |
|-------|--------|-------|
| 202601 | Demand Review 202601.xlsx | DR 2026 and 2027 |
| 202602 | Demand Review 202602.xlsx | 202602 |
| 202603 | Demand Review 202603.xlsx | 202603 |

### 数据提取规则

* 提取列：`Account`（）、`Product Type`（）、`Product Name`（）、所有格式为 `YYYY-MM` 的月份列
* 纳入账户：7个省份账户


---

## 功能说明

### 筛选器（顶部导航栏）

| 筛选项 | 说明 |
|--------|------|
| 省份 | 选择单个账户或查看全部 |
| 产品类型 | |
| 年份 | 全部 / 仅 2026 / 仅 2027 |

所有筛选项实时联动，变更后所有图表和表格同步刷新。

### 视图标签页

#### 月度趋势
折线图，X 轴为月份，每条线代表一个 Cycle，展示同一月份在不同审查轮次下的需求预测量变化。

#### 季度汇总
分组柱状图，按 Q1–Q4 汇总各 Cycle 的总需求，便于季度级别的横向对比。

#### 账户汇总
* 堆叠柱状图：按账户分色展示每个 Cycle 的需求构成
* 汇总表：每行一个账户，各 Cycle 总量 + 首尾 Cycle 总变化 Δ；末行为加粗 Grand Total 合计行，按第一个 Cycle 需求量降序排列

#### 省份对比
水平分组柱状图，各账户（）并排展示各 Cycle 的总需求，自动按基准 Cycle 降序排列。

#### 产品类型
五大品类（）分组柱状图，对比各 Cycle 的品类需求量。

#### 明细变化
完整产品明细表，字段包括：

| 字段 | 说明 |
|------|------|
| 省份 | Account 账户名称 |
| 产品类型 | Product Type |
| 产品名称 | Product Name |
| YYYYMM（各Cycle） | 该 Cycle 下的月度总需求量 |
| Δ XX→XX | 相邻 Cycle 环比变化（绿色↑ / 红色↓） |
| Δ 总变化 | 第一个 Cycle 到最新 Cycle 的净变化量 |

---

## 加载新 Cycle 数据

点击右上角 **「上传新 Cycle」** 按钮，支持两种加载方式：

### 本地上传

1. 拖拽 `.xlsx` 文件到上传区域，或点击选择文件
2. 系统自动识别 Sheet（优先匹配 `DR XXXX` 或 6位数字格式，否则取第一个 Sheet）
3. 自动从文件名提取 Cycle ID（如 `Demand Review 202604.xlsx` → `202604`）
4. 点击 **「加入对比」** 后，新 Cycle 出现在顶部 Cycle Pills，所有图表实时更新
5. 非内置 Cycle 在 Pills 显示 `×` 按钮，可随时移除

### SharePoint 链接

1. 切换到 **「🔗 SharePoint」** 标签
2. 粘贴 SharePoint 文件链接（文档库直链或共享链接均可）
3. 点击 **「从 SharePoint 加载」**
4. AI 后端调用工具下载并解析 Excel，将新 Cycle 数据自动注入应用

> **注意：** SharePoint 加载需要账号对该文件有读取权限，且须在 Glean 应用内使用。

---

## 数据刷新

本应用为 **Live Data App**，数据通过独立的数据脚本从 Excel 文件读取后嵌入页面。在 Glean 中点击应用右上角的 **Refresh data** 按钮，即可触发重新读取所有源 Excel 文件并重新渲染页面，确保数据与最新文件保持一致。

---

## 文件结构

```
output/
└── demand_review_app/
    ├── demand_review_app.html   # 渲染后的应用（自动生成，勿手动编辑）
    ├── template.html.tmpl       # HTML 模板源文件
    ├── refresh_data.py          # 数据抓取脚本（读取 Excel → 生成 data.json）
    └── data.json                # 当前数据快照（自动生成）
```

源 Excel 文件位置（沙箱）：

```
/home/user/Demand Review 202601.xlsx
/home/user/Demand Review 202602.xlsx
/home/user/Demand Review 202603.xlsx
```

---

## 扩展说明

### 新增更多 Cycle 来源
未来如需支持 `DR 2027 and 2028` 等新 Sheet 命名，在 `refresh_data.py` 的 `EXCEL_FILES` 字典中新增条目即可，格式与现有条目一致。

### 内置 Cycle 颜色映射

| 顺序 | 颜色 |
|------|------|
| 1st Cycle | 蓝色 `#3559e6` |
| 2nd Cycle | 橙色 `#e07c2a` |
| 3rd Cycle | 绿色 `#159a6a` |
| 4th+ Cycle | 紫色、红色、青色… |

颜色自动按 Cycle 顺序分配，上传新 Cycle 后颜色自动延续。

### PDF 导出
点击应用右上角 **Export as PDF** 即可导出当前视图为 PDF，图表会自动转为静态图像以保证打印质量。
