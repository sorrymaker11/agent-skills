# 前端工作日报模板

> 适用场景：每日工作结束后填写，发送给主管或团队

---

## 工作日报 · {{date}}

### ✅ 今日完成

- **[{{module}}] {{task_title}}**：{{task_description}}
  - 技术方案：{{tech_detail}}
  - 影响范围：{{scope}}

- **[{{module}}] {{task_title}}**：{{task_description}}

### 🔧 技术攻坚（今日遇到的难点）

> 若无难点可省略此节

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| {{problem}} | {{root_cause}} | {{solution}} |

### 📌 明日计划

- [ ] {{plan_1}}（预计耗时：{{estimate}}）
- [ ] {{plan_2}}
- [ ] {{plan_3}}

### ⚠️ 风险与依赖（可选）

- {{risk_or_dependency}}

---

## 填写说明

| 字段 | 说明 |
|------|------|
| `{{module}}` | 所属模块或页面，如：用户中心、订单列表 |
| `{{task_title}}` | 任务简短标题，如：Bug修复、功能开发、代码重构 |
| `{{task_description}}` | 具体做了什么，结果如何，尽量量化 |
| `{{tech_detail}}` | 用到了哪些技术手段，如：虚拟滚动、memo优化、CSS变量 |
| `{{scope}}` | 影响了哪些页面/用户/功能 |

## 写作技巧

**❌ 不好的写法：**
> 今天修了几个 bug，写了个组件

**✅ 好的写法：**
> 修复商品列表页在 Safari 浏览器下 `position: sticky` 失效问题，通过为父容器添加 `overflow: clip` 解决，影响约 15% iOS 用户
> 封装通用 `DateRangePicker` 组件，支持快捷选项（今天/本周/本月）及自定义区间，已在订单、报表两个模块接入
