---
name: frontend-learning-reflection-writer
description: 当用户需要撰写前端技术学习心得、框架实践总结、性能优化复盘时使用。支持：前端框架/库学习、构建工具探索、性能优化实践、设计模式/架构思考。
argument-hint: "[学习主题] [心得类型] [可选字数]"
user-invokable: true
version: "1.0.0"
author: GitHub Copilot Agent
tags:
  - frontend
  - learning
  - reflection
  - documentation
supported-types:
  - framework-learning     # 框架/库学习心得
  - build-tool-exploration # 构建工具探索记录
  - performance-optimization # 性能优化实践复盘
  - design-pattern         # 设计模式/架构思考
  - general                # 通用学习心得
---

# frontend-learning-reflection-writer

## 技能概述

本技能专门用于生成高质量的**前端开发学习心得体会**文档。无论是框架入门、工程化探索、性能调优还是架构思考，都能生成结构清晰、内容充实的学习总结。

---

## 调用方式

```
/skill frontend-learning-reflection-writer [学习主题] [心得类型] [可选字数]
```

### 参数说明

| 参数 | 是否必填 | 说明 | 示例 |
|------|----------|------|------|
| `学习主题` | 必填 | 本次学习的技术主题 | `React Hooks`、`Vite 构建优化` |
| `心得类型` | 必填 | 对应 supported-types 中的类型 | `framework-learning` |
| `可选字数` | 选填 | 目标字数，默认 800 字 | `1200` |

### 调用示例

```bash
# 生成 React Hooks 学习心得（默认800字）
/skill frontend-learning-reflection-writer "React Hooks" framework-learning

# 生成 Webpack 性能优化复盘（1500字）
/skill frontend-learning-reflection-writer "Webpack 性能优化" performance-optimization 1500

# 生成 Vue3 组合式 API 框架学习心得
/skill frontend-learning-reflection-writer "Vue3 Composition API" framework-learning 1000
```

---

## 生成规则

### 1. 通用结构要求

所有类型的心得均应包含以下模块：

```
1. 学习背景与动机（为什么学、学习前状态）
2. 核心知识点梳理（重点概念/API/原理）
3. 实践过程记录（动手做了什么）
4. 踩坑与解决方案（遇到了哪些问题）
5. 收获与思考（学到了什么、改变了什么认知）
6. 后续计划（下一步怎么深入）
```

### 2. 各类型特化要求

#### framework-learning（框架/库学习）
- 必须包含：核心概念对比（对比已知技术）
- 必须包含：代码示例（before/after 或关键 API 演示）
- 风格：由浅入深，适合分享给团队

#### build-tool-exploration（构建工具探索）
- 必须包含：配置关键节点截图说明
- 必须包含：构建速度/体积数据对比（表格形式）
- 风格：偏工程化、量化结果

#### performance-optimization（性能优化实践）
- 必须包含：优化前后性能指标数据（LCP/FID/CLS 等）
- 必须包含：优化手段清单（可复用 checklist）
- 风格：问题驱动，结果导向

#### design-pattern（设计模式/架构思考）
- 必须包含：模式适用场景分析
- 必须包含：在实际项目中的落地代码
- 风格：偏思考深度，适合技术博客

### 3. 语言与风格规范

- 语言：中文为主，技术术语保留英文原词
- 语气：真实、有个人感受，避免官方文档式堆砌
- 代码块：使用 Markdown 代码块，注明语言类型
- 字数控制：±10% 浮动均可接受

---

## 输出格式

输出为标准 Markdown 格式，可直接：
- 粘贴到掘金/博客园/知乎等技术社区
- 提交到团队 Wiki / Confluence
- 作为 PR 的技术说明文档

---

## 参考模板

根据 `心得类型` 参数，自动加载对应模板：

| 类型 | 模板文件 |
|------|---------|
| `framework-learning` | `templates/framework-learning.md` |
| `build-tool-exploration` | `templates/build-tool-exploration.md` |
| `performance-optimization` | `templates/performance-optimization.md` |
| `design-pattern` | `templates/design-pattern-architecture.md` |
| `general` | `templates/general.md` |

高质量范文参见 `examples/` 文件夹。

---

## 注意事项

1. 生成内容应基于用户提供的实际学习背景，避免纯模板填充
2. 代码示例应简洁，聚焦说明要点，非完整项目代码
3. 踩坑部分应具体，避免"遇到了一些问题"这类模糊表述
4. 数据/指标类内容若用户未提供，应给出占位符提示用户填写
