# Deloitte-diagram-generator
# Deloitte 标准流程图生成器 — 开发记录

## 项目概述

一套基于 **德勤（Deloitte）流程图标准** 的 Draw.io 水平泳道流程图生成工具。用户通过左侧表单配置流程结构（泳道→活动→文档），右侧实时生成 Draw.io XML 代码并嵌入预览。

**核心特性：**
- 🏊 水平泳道布局，角色在左侧，流程从左到右
- 🎨 德勤暗色主题（黑色背景 `#000000`，深灰泳道 `#2A2A2A`）
- 🟢 系统标签绿色 `#D1EDA0`，文档标签黄色 `#FFC000`
- 🔢 顺序编号 01, 02, 03...，正交连线无交叉
- 📐 Start 和 End 都在第一个泳道内
- 🤖 接入 MiMo v2.5 大模型进行 AI 优化

---

## 文件清单

| 文件 | 说明 |
|------|------|
| `plantuml-generator.html` | PlantUML 版本（代码 + 在线渲染图） |
| `drawio-generator.html` | Draw.io 初版（XML 代码 + 在线编辑器预览） |
| `drawio-generator-mimo.html` | Draw.io + MiMo AI 优化版本 |
| `drawio-generator-v13.html` | v13：里程碑版本（左侧表单 + 右侧代码/预览） |
| `drawio-generator-v15.html` | v15：硬编码模板生成 |
| `drawio-generator-v16.html` | v16：可编辑提示词模板 + 确认生成 |
| `drawio-generator-v17.html` | v17：结构化预设 → 确认 → 生成 |
| `drawio-generator-v18.html` | v18：一步到位重构（下拉选单 + 完整规范） |
| `drawio-generator-v19.html` | v19：完整 XML 预设填充 |
| `drawio-generator-v20.html` | v20：流程信息同步 + UI 优化 |
| `drawio-generator-v21.html` | v21：表单解析修复（进行中） |
| `drawio-generator-multi-api.html` | 多 API 版本（MiMo/智谱/DeepSeek/OpenAI） |
| `deloitte-full-constraints-template.md` | Deloitte 规范模板（蒸馏后的 skills） |
| `api-debug-tool.html` | API 调试工具 |
| `supplier-evaluation-v1.2.drawio` | 示例：供应商评价管理流程图 |
| `supplier-access-flow.drawio` | 示例：供应商准入流程图 |

## 版本迭代记录

### v1 — PlantUML 初版
- 左侧表单（模板选择 + 泳道/活动/文档层级配置）
- 右侧 PlantUML 代码编辑器 + Mermaid 渲染预览
- 预置断奶仔猪转群流程（6 泳道、8 步骤、7 文档）

**问题：** 泳道、活动、文档之间的层级对应关系不够清晰

### v2 — 层级关系重构
- 将表单重构为 **泳道（角色）→ 活动步骤 → 文档** 三级层级结构
- 每个泳道可展开/折叠，每个活动可配置输入文档（绿色）和输出文档（橙色）
- 层级指示器显示 `S → A → D` 的流向关系

### v3 — PlantUML 在线渲染
- 集成 `plantuml-encoder` 库，使用 Deflate 编码
- 拼接 `http://www.plantuml.com/plantuml/png/{encoded}` URL
- 用 `<img>` 标签展示真正的 PlantUML 渲染图

**问题：** 预览区域无法滚动，只能看到露出一点点

### v4 — 滚动修复
- 给 `.editor-container` 添加 `min-height: 0`
- `.preview-area` 添加 `min-height: 0` + `overflow: auto`
- 移除 flex 居中避免撑开容器

### v5 — Draw.io 版本
- 新建 `drawio-generator.html`
- 右侧上方显示 Draw.io XML 代码
- 右侧下方嵌入 `https://app.diagrams.net/` 在线编辑器

**问题：** Draw.io 在线编辑器无法正常加载（X-Frame-Options 限制）

### v6 — MiMo AI 接入
- 接入 MiMo API（`https://api.xiaomimimo.com/v1/chat/completions`）
- 模型：`mimo-v2.5-pro` → 后改为 `mimo-v2.5`（pro 反而笨）
- 认证方式：`api-key` header（不是 `Authorization: Bearer`）

**问题：** `Failed to fetch` — Endpoint 错误
- ❌ 原来：`https://api.mimo.mi.com/v1/chat/completions`
- ✅ 修正：`https://api.xiaomimimo.com/v1/chat/completions`

### v7 — 多 API 版本（已弃用）
- 支持 MiMo、智谱 AI、DeepSeek、OpenAI 四个提供商
- 用户反馈：只要 MiMo，不需要其他

### v8-v12 — 持续迭代
- 逐步将德勤规范完整嵌入系统提示词
- 颜色编码、泳道结构、文档标注等规则逐步完善

### v13 — 里程碑版本 ✅
- 左侧：模板选择 + 泳道/活动/文档层级表单
- 右侧：XML 代码编辑器 + Draw.io 预览
- 完整的 Deloitte 规范嵌入
- **Draw.io 嵌入成功**：使用 `embed.diagrams.net/?embed=1&proto=json` 实现实时预览
- **从左侧表单生成 XML**：`genFromLeft()` 函数将表单数据转为 Draw.io XML
- **AI 生成**：`genWithAI()` 调用 MiMo v2.5 生成符合德勤标准的 XML
- 4 个预设模板（断奶仔猪转群、采购执行、付款审批、库存管理）
- **作为后续版本的基础**

### v14-v17 — 优化迭代
- v14: Draw.io 预览优化
- v15: 硬编码模板生成
- v16: 可编辑提示词模板 + 确认生成
- v17: 结构化预设 → 确认 → 生成

### v18 — 一步到位重构
- **设计变更**：不再逐步迭代，一步到位构建完整版本
- 左侧下拉选单选择流程（只需关注流程信息）
- 中间区域展示完整蒸馏后的 Deloitte 规范（用户可见）
- 选择模板 → 自动填充流程编号、名称、画布尺寸、流程信息
- 5 个预设流程（采购订单、付款审批、库存管理、供应商准入、断奶仔猪）
- 点击生成：流程信息 + Deloitte 规范一起发给大模型
- 右侧：XML 代码编辑器 + Draw.io 实时预览

### v19 — 完整 XML 预设填充
- 将 4 个完整的 Draw.io XML 文件作为预设模板
- 选择预设后同时填充三部分：
  - 右侧 XML 代码 — 直接显示完整的 Deloitte 标准 XML
  - Draw.io 预览 — 右侧下方实时渲染流程图
  - 左侧表单 — 泳道、活动、系统、文档信息全部自动填充
  - 流程信息 — 给 AI 的提示词也自动填充
- 4 个模板：断奶仔猪转群（6泳道·8步骤·2决策）、采购执行（4·7·1）、付款审批（3·6·2）、库存管理（5·8·2）
- 每个 XML 文件均可直接导入 Draw.io 使用

### v20 — 流程信息同步 + UI 优化
- **修复**：流程信息随预设改变同步更新
  - 选择预设时，下方"流程信息"文本框自动填充对应的提示词
- **新增**：左下方 Draw.io 编辑器入口
  - 标题："Draw.io 在线编辑器"
  - 说明："复制右侧 XML 代码，粘贴到 Draw.io 进行可视化编辑"
  - 链接：`https://app.diagrams.net/`
- **修改**：按钮文字 "AI 优化 XML" → "AI 优化并生成 XML"

### v21 — 表单解析修复 ✅
- **修复**：新增完整的 `genFromForm()` 函数
  - 解析表单数据 → 从 FD（泳道、活动、系统、文档）中提取信息
  - 生成结构化文本 → 自动创建格式化的流程信息
  - 填充到提示词框 → 更新 `#processInfo` 文本框
- **修复**：文档图标形状统一
  - D（文档）从圆形（`border-radius: 50%`）改为方形（`border-radius: 2px`）
  - 现在 S（泳道）→ A（活动）→ D（文档）都在同一行，整齐对齐
- **状态**：已完成

### v22 — 流程信息实时同步 ✅
- **修复**：表单修改时流程信息不同步
  - 新增/删除系统、文档时自动同步更新流程信息
  - `addRole()` / `rmS()` / `addA()` / `rmA()` / `updN()` / `addD()` / `rmD()` 全部调用 `syncProcessInfo()`
- **修复**：层级指示器排版
  - "文档"两个字和"泳道"、"活动"在同一行，不再错行
- **修复**：流程编号和名称在流程信息中体现
  - `genFromForm()` 生成的流程信息加入完整标题
  - `pCode` 和 `pName` 输入框添加 `onchange` 事件，修改时自动同步到流程信息
- **状态**：已完成
- **问题 1**：从表单解析到流程信息（给 AI 的提示词）不成功
  - 新增/删减泳道活动后，点击"从表单生成"不可使用
- **问题 2**：文档图标形状不统一
  - D（文档）应该是文档形状（底部波浪线），不是圆形
- **状态**：待修复

---

## 德勤流程图规范要点

### 布局规则
- 水平泳道（`horizontal=0`），角色在左侧
- 背景色 `#000000`（黑色）
- 泳道背景 `#2A2A2A`（深灰）和 `#333333`（中灰）交替
- 泳道边框 `#CCCCCC`，宽度 1570px，高度 90px

### 颜色编码
| 元素 | 填充色 | 边框色 | 文字色 |
|------|--------|--------|--------|
| 泳道背景 | `#2A2A2A` / `#333333` | `#CCCCCC` | `#FFFFFF` |
| 活动节点 | `#000000` | `#FFFFFF` 2px | `#FFFFFF` 16pt |
| 系统标签 | `#D1EDA0` | `#D1EDA0` | `#000000` 12pt |
| 文档标签 | `#FFC000` | `#FFC000` | `#000000` 12pt |
| 判断节点 | `#000000` | `#FFFFFF` 2px | `#FFFFFF` |
| 开始/结束 | `#000000` | `#FFFFFF` 2px | `#FFFFFF` |

### 文档位置规则
- 📥 **输入文档**（流入本活动）→ 放在活动**上方**
- 📄 **输出文档**（归档/存档）→ 放在活动**下方**
- 📤 **流转文档**（传递给下游）→ 放在活动**右侧**

### 连线规则
- 正交路由（`edgeStyle=orthogonalEdgeStyle`）
- 90 度转折，无交叉
- 白色 `#FFFFFF`，2px 宽度
- 显式指定 `exitX/Y` 和 `entryX/Y`

### 关键约束
- **Start 和 End 都在第一个泳道**
- 活动编号顺序排列，不跳号
- 判断节点必须有 Yes/No 标签
- 标签换行用 `&#xa;`

---

### 参考：deloitte-full-constraints-template.md

完整的 Deloitte 规范模板（蒸馏后的 skills）已保存为独立文件：

- **文件**：[deloitte-full-constraints-template.md](deloitte-full-constraints-template.md)
- **来源**：从 `deloitte-process-map` skill 中蒸馏提取
- **内容**：包含完整的布局规则、颜色编码、连线规则、文档位置规则、XML 结构模板
- **用途**：作为 AI 生成的系统提示词基础，选择预设时自动附加到用户输入

---

## 预设流程图模板

以下四个流程图模板均由 MiMo v2.5 根据德勤标准生成，经过验证可直接在 app.diagrams.net 中打开和编辑。

### 模板总览

| # | 流程名称 | 泳道 | 步骤 | 文档 | 系统 | 判断 | 连线 | 特点 |
|---|---------|------|------|------|------|------|------|------|
| 1 | 断奶仔猪转群 | 6 | 8 | 6 | 2 | 2 | 13 | 完整养殖转群流程 |
| 2 | 采购执行 | 4 | 7 | 5 | 4 | 1 | 10 | 采购订单到入库 |
| 3 | 付款审批 | 3 | 6 | 2 | 3 | 2 | 11 | 付款申请到凭证归档 |
| 4 | 库存管理 | 5 | 8 | 6 | 5 | 2 | 13 | 盘点、预警、补货、报表 |

### 1. 断奶仔猪转群（6泳道 · 8步骤）

**角色：** 流程驱动 | 产房 | 保育舍 | 兽医 | 饲养员 | 统计员

| 编号 | 步骤名称 | 系统 | 输出文档 |
|------|---------|------|---------|
| 01 | 断奶计划制定 | 养殖管理系统 | 《断奶计划表》 |
| 02 | 仔猪健康评估 | — | 《健康评估报告》 |
| 03 | 转群批次确认 | — | 《转群批次单》 |
| 04 | 转群操作执行 | — | 《转群记录表》 |
| 05 | 转入保育舍 | 数据分析系统 | — |
| 06 | 转群记录 | — | — |
| 07 | 隔离观察处理 | — | 《隔离观察记录》 |
| 08 | 转群数据统计 | — | 《转群统计报表》 |

**判断节点：** 2 个（健康评估决策、转群条件判断）

### 2. 采购执行（4泳道 · 7步骤）

**角色：** 流程驱动 | 采购部门 | 供应商 | 仓储部门

| 编号 | 步骤名称 | 系统 | 输出文档 |
|------|---------|------|---------|
| 01 | 采购订单确认 | ERP系统 | 《采购订单》 |
| 02 | 价格审核 | 财务系统 | — |
| 03 | 订单执行 | — | — |
| 04 | 发货通知 | — | 《发货通知单》 |
| 05 | 收货验收 | WMS系统 | 《验收单》 |
| 06 | 质量检验 | — | 《质检报告》 |
| 07 | 入库上架 | WMS系统 | 《入库单》 |

**判断节点：** 1 个（价格审核判断）

### 3. 付款审批（3泳道 · 6步骤）

**角色：** 流程驱动 | 财务部门 | 管理层

| 编号 | 步骤名称 | 系统 | 输出文档 |
|------|---------|------|---------|
| 01 | 付款申请提交 | 财务系统 | 《付款申请单》 |
| 02 | 发票审核 | 财务系统 | — |
| 03 | 部门审批 | — | — |
| 04 | 管理层审批 | 银行系统 | — |
| 05 | 付款执行 | — | — |
| 06 | 付款凭证归档 | — | 《付款凭证》 |

**判断节点：** 2 个（发票审核判断、管理层审批判断）

### 4. 库存管理（5泳道 · 8步骤）

**角色：** 流程驱动 | 仓储部门 | 采购部门 | 销售部门 | 财务部门

| 编号 | 步骤名称 | 系统 | 输出文档 |
|------|---------|------|---------|
| 01 | 库存盘点 | WMS系统 | 《盘点表》 |
| 02 | 差异分析 | 财务系统 | 《差异分析报告》 |
| 03 | 库存调整 | — | 《库存调整单》 |
| 04 | 安全库存预警 | 预警系统 | — |
| 05 | 采购补货 | ERP系统 | 《采购订单》 |
| 06 | 出入库管理 | WMS系统 | 《出入库单》 |
| 07 | 数据同步 | — | — |
| 08 | 库存报表 | — | 《库存报表》 |

**判断节点：** 2 个（差异分析判断、安全库存判断）

---

## Draw.io 渲染调试记录（详细）

Draw.io 在线编辑器嵌入是本项目中最棘手的部分。经过多次尝试，最终在 v13 中成功实现。

### 背景

项目需要实现：左侧表单配置流程 → 右侧上方显示 Draw.io XML 代码 → 右侧下方实时预览 Draw.io 图表。核心挑战在于如何在网页中嵌入 Draw.io 编辑器并实现双向通信。

### 尝试 1：直接 iframe 嵌入 app.diagrams.net ❌

**方案：** 用 `<iframe src="https://app.diagrams.net/">` 直接嵌入 Draw.io 主站。

**结果：** 完全无法加载。

**原因：** Draw.io 主站设置了 `X-Frame-Options: sameorigin`，禁止被 iframe 嵌入。

### 尝试 2：使用 embed.diagrams.net URL ✅（v13 成功方案）

**方案：** 使用 Draw.io 的嵌入 URL：
```
https://embed.diagrams.net/?embed=1&proto=json&ui=atlas&spin=1
```

**关键参数说明：**
- `embed=1`：启用嵌入模式
- `proto=json`：使用 JSON 协议进行通信（这是关键！）
- `ui=atlas`：使用 Atlas 主题 UI
- `spin=1`：显示加载动画

**通信协议（proto=json）：**

Draw.io 嵌入模式通过 `postMessage` 和 `message` 事件实现双向通信：

**→ 发送消息到 Draw.io 编辑器：**
```javascript
iframe.contentWindow.postMessage(JSON.stringify({
  action: "load",
  xml: "<mxfile>...</mxfile>"
}), "*");
```

**← 接收 Draw.io 编辑器的事件：**
```javascript
window.addEventListener("message", (e) => {
  const msg = typeof e.data === 'string' ? JSON.parse(e.data) : e.data;
  if (msg.event === "init") {
    // 编辑器初始化完成，可以发送 XML
    drawioReady = true;
  }
  if (msg.event === "load") {
    // XML 加载完成
  }
});
```

**完整初始化流程（v13 实现）：**
```javascript
let drawioReady = false;
let pendingXML = null;

// 1. 监听消息
window.addEventListener("message", (e) => {
  try {
    const m = typeof e.data === 'string' ? JSON.parse(e.data) : e.data;
    if (m.event === "init") {
      drawioReady = true;
      if (pendingXML) { sendXML(pendingXML); pendingXML = null; }
    }
    if (m.event === "load") {
      // 加载完成
    }
  } catch(x) {}
});

// 2. 初始化 Draw.io（设置 iframe src）
function initDrawIO() {
  document.getElementById('drawioFrame').src =
    "https://embed.diagrams.net/?embed=1&proto=json&ui=atlas&spin=1";
}

// 3. 发送 XML 到编辑器
function sendXML(xml) {
  if (!drawioReady) { pendingXML = xml; return; }
  document.getElementById('drawioFrame').contentWindow.postMessage(
    JSON.stringify({ action: "load", xml: xml }), "*"
  );
}

// 4. 页面加载完成后初始化
document.addEventListener('DOMContentLoaded', () => {
  init();
  render();
  setTimeout(initDrawIO, 500);  // 延迟初始化确保 DOM 就绪
});
```

**iframe HTML 结构：**
```html
<div class="dc">
  <div class="dc-h">
    <span>Draw.io 预览</span>
    <span id="pvSt">加载中...</span>
  </div>
  <div class="dc-a">
    <iframe id="drawioFrame" src="about:blank"></iframe>
  </div>
</div>
```

**CSS 样式：**
```css
.dc { background: #1a1a1a; display: flex; flex-direction: column; overflow: hidden; }
.dc-h { background: #2d2d2d; padding: 6px 12px; font-size: 10px; color: #aaa;
         border-bottom: 1px solid #404040; display: flex; justify-content: space-between;
         align-items: center; flex-shrink: 0; }
.dc-a { flex: 1; overflow: hidden; }
.dc-a iframe { width: 100%; height: 100%; border: none; }
```

### 尝试 3：使用 Draw.io 导出服务 ❌

**方案：** 利用 `https://convert.diagrams.net/api/v1/png/{encoded}` 将 XML 转为图片。

**结果：** 导出服务需要特定认证和参数格式，且触发 CORS 限制。

### 尝试 4：使用 mxGraph 直接渲染 ❌

**方案：** 引入 mxGraph 库，解析 Draw.io XML 并在 canvas 上渲染。

**结果：** mxGraph API 复杂，需要处理 XML 解析、样式映射、交互事件等，工作量过大。

### 最终方案：embed.diagrams.net + proto=json ✅

v13 最终采用方案 2，成功实现了：
- ✅ Draw.io 编辑器正常加载
- ✅ XML 代码实时发送到编辑器
- ✅ 编辑器实时渲染流程图
- ✅ 支持在编辑器中直接编辑和拖拽
- ✅ 支持导出为 PNG/SVG/PDF（通过编辑器 UI）

### 错误日志

```
# 错误 1：X-Frame-Options 阻止（尝试 1）
Refused to display 'https://app.diagrams.net/' in a frame because it set 'X-Frame-Options' to 'sameorigin'.

# 错误 2：embed URL 404（尝试 2 早期）
GET https://embed.diagrams.net/?... 404 (Not Found)
→ 原因：缺少 proto=json 参数

# 错误 3：CORS 跨域（尝试 3）
Access to fetch at 'https://convert.diagrams.net/...' from origin 'http://localhost:8080'
has been blocked by CORS policy.
```

### 关键经验总结

1. **必须使用 `proto=json` 参数**：没有这个参数，embed.diagrams.net 无法正确初始化
2. **通信是异步的**：需要先等 `init` 事件，才能发送 XML。如果 XML 先生成，需要缓存为 `pendingXML`
3. **iframe 初始 src 设为 about:blank**：避免在初始化前加载错误内容
4. **延迟初始化**：使用 `setTimeout(initDrawIO, 500)` 确保 DOM 完全就绪
5. **postMessage 需要 JSON.stringify**：Draw.io 的 JSON 协议要求消息是字符串化的 JSON

### 相关文件

| 文件 | 说明 |
|------|------|
| `drawio-v13.html` | ✅ 成功实现 Draw.io 嵌入的版本 |
| `drawio-debug.html` | Draw.io XML 验证工具（带初始化/加载按钮） |
| `drawio-debug2.html` | Draw.io XML 验证工具 v2（带详细日志面板） |

---

## 遇到的问题及解决方案

### 1. Draw.io 在线编辑器无法加载
**问题：** 嵌入 `https://app.diagrams.net/` 的 iframe 无法正常显示
**原因：** Draw.io 的 iframe 嵌入需要特定的 URL 参数和初始化方式，直接引用主站 URL 不行
**状态：** 待解决，考虑使用 Draw.io 的导出 URL 或静态渲染方案

### 2. MiMo API 调用失败
**问题：** `Failed to fetch` 错误
**原因：** API Endpoint URL 错误
- ❌ 原来：`https://api.mimo.mi.com/v1/chat/completions`
- ✅ 修正：`https://api.xiaomimimo.com/v1/chat/completions`
**解决：** 参照 MiMo 官方文档修正 BASE_URL

### 3. MiMo 模型选择
**问题：** 最初使用 `mimo-v2.5-pro`，但效果反而比 `mimo-v2.5` 差
**解决：** 改为 `mimo-v2.5`，更稳定且响应更快

### 4. API 认证方式
**问题：** 不确定用哪种认证方式
**解决：** MiMo 使用 `api-key` header，不是 `Authorization: Bearer`

### 5. PlantUML 预览区域无法滚动
**问题：** 预览区域只能显示部分内容
**原因：** Flex 布局没有正确设置 `min-height: 0`
**解决：** 给 editor-container 和 preview-area 添加 `min-height: 0`

### 6. 泳道/活动/文档层级关系不清晰
**问题：** 初版表单中三者是平铺的，看不出关联
**解决：** 重构为三级嵌套结构，泳道包含活动，活动包含文档

---

## 当前状态

正在基于 **v13** 构建 **v21**，v18→v20 已完成，v21 进行中。

### 已完成（v18-v20）
- ✅ 下拉选单选择流程 → 自动填充流程信息
- ✅ 完整 Deloitte 规范展示（蒸馏后的 skills）
- ✅ 4 个完整 Draw.io XML 预设模板
- ✅ 选择预设同时填充三部分（XML + 预览 + 表单）
- ✅ Draw.io 在线编辑器入口
- ✅ 流程信息随预设同步更新
- ✅ MiMo v2.5 AI 生成集成

### 已完成（v21-v22）
- ✅ v21：表单解析修复（genFromForm 函数）+ 文档图标形状统一
- ✅ v22：流程信息实时同步（增删系统/文档自动更新）+ 层级指示器排版 + 流程编号/名称体现
- ✅ 完整的 Deloitte 规范模板（蒸馏后的 skills）作为 AI 提示词基础

### 进行中（v23）
- 🔧 待确认下一步需求

### MiMo API 配置
- Endpoint: `https://api.xiaomimimo.com/v1/chat/completions`
- Model: `mimo-v2.5`
- 认证: `api-key` header
- API Key: `sk-****`（按量计费）

---

## 使用方式

```bash
# 进入 outputs 目录
cd "/Users/linzhili/Documents/Codex/2026-06-23/users-linzhili-library-application-support-claude/outputs/"

# 启动本地服务器（解决 CORS 问题）
python3 -m http.server 8080

# 浏览器访问
open http://localhost:8080/drawio-generator-v13.html
```

---

## 下一步计划

- [ ] 完成 v17：结构化预设 → 确认 → 生成 XML
- [ ] 接入 MiMo v2.5 进行 AI 优化
- [ ] 修复 Draw.io 在线编辑器嵌入问题
- [ ] 添加更多预设模板
- [ ] 支持导出 PNG/SVG/PDF
