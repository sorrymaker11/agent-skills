# {{优化主题}} 性能优化实践复盘

> 优化时间：{{YYYY年MM月}}  
> 项目背景：{{项目类型 / 用户规模 / 技术栈}}  
> 核心成果：LCP **{{X → X}}s** | FID **{{X → X}}ms** | CLS **{{X → X}}

---

## 一、问题发现：哪里出了问题

### 用户反馈 / 监控告警

{{描述问题是如何被发现的：用户投诉、性能监控报警、Core Web Vitals 不达标等}}

### 初步诊断

使用以下工具进行分析：
- [ ] Chrome DevTools Performance 面板
- [ ] Lighthouse 报告
- [ ] WebPageTest
- [ ] {{其他工具}}

**Lighthouse 初始评分：**

| 指标 | 优化前得分 |
|------|-----------|
| Performance | {{X}}/100 |
| LCP | {{Xs}} |
| FID / INP | {{Xms}} |
| CLS | {{X}} |
| TTFB | {{Xms}} |

---

## 二、问题根因分析

### 2.1 资源加载层面

```
Network 瀑布图分析：
- 关键路径阻塞：{{描述，例如"3 个同步 script 标签阻塞渲染"}}
- 资源体积过大：{{描述，例如"未压缩的图片占页面资源 70%"}}
- 缓存命中率：{{描述}}
```

### 2.2 渲染/执行层面

```
Performance 面板发现：
- 长任务（>50ms）：{{数量}} 个，最长 {{Xms}}
- Layout Thrashing：{{是否存在，位置}}
- 内存泄漏：{{是否存在}}
```

### 2.3 根本原因总结

> 🔍 核心瓶颈：{{一句话描述最主要的问题所在}}

---

## 三、优化措施清单

### 3.1 资源优化

- [ ] **图片优化**：WebP 格式转换 + 懒加载

  ```html
  <!-- Before -->
  <img src="banner.png" />

  <!-- After -->
  <img src="banner.webp" loading="lazy" decoding="async" />
  ```

- [ ] **JS 体积优化**：Tree-shaking + 按需引入

  ```javascript
  // Before：全量引入
  import _ from 'lodash'

  // After：按需引入
  import debounce from 'lodash/debounce'
  ```

- [ ] **代码分割**：路由级懒加载

  ```javascript
  // React
  const Dashboard = React.lazy(() => import('./pages/Dashboard'))
  ```

### 3.2 加载策略优化

- [ ] **预加载关键资源**

  ```html
  <link rel="preload" href="/fonts/main.woff2" as="font" crossorigin />
  ```

- [ ] **资源预连接**

  ```html
  <link rel="preconnect" href="https://cdn.example.com" />
  ```

- [ ] **HTTP 缓存策略调整**：{{描述 Cache-Control 配置变更}}

### 3.3 渲染优化

- [ ] **减少重排/重绘**

  ```javascript
  // Before：多次读写 DOM 触发多次 Layout
  el.style.left = el.offsetLeft + 10 + 'px'
  el.style.top  = el.offsetTop  + 10 + 'px'

  // After：批量读、批量写
  const left = el.offsetLeft
  const top  = el.offsetTop
  el.style.cssText = `left:${left+10}px;top:${top+10}px`
  ```

- [ ] **长列表虚拟滚动**：{{使用的库及配置}}

### 3.4 {{其他专项优化}}

{{具体措施描述}}

---

## 四、优化效果

### 量化数据

| 指标 | 优化前 | 优化后 | 提升幅度 |
|------|-------|-------|---------|
| LCP | {{Xs}} | {{Xs}} | {{-X%}} |
| FID / INP | {{Xms}} | {{Xms}} | {{-X%}} |
| CLS | {{X}} | {{X}} | {{-X%}} |
| JS Bundle | {{XKB}} | {{XKB}} | {{-X%}} |
| 首屏时间 | {{Xs}} | {{Xs}} | {{-X%}} |

### Lighthouse 最终评分

| 指标 | 优化前 | 优化后 |
|------|-------|-------|
| Performance | {{X}}/100 | {{X}}/100 |

---

## 五、可复用的优化 Checklist

以下是从本次优化提炼的通用 Checklist，可用于其他项目自查：

**资源层**
- [ ] 所有图片使用 WebP/AVIF，并设置 `loading="lazy"`
- [ ] 第三方库按需引入，禁止全量 import
- [ ] 开启 Gzip/Brotli 压缩

**加载策略层**
- [ ] 关键 CSS 内联，非关键 CSS 异步加载
- [ ] 字体文件 preload，设置 font-display: swap
- [ ] API 接口合并，减少请求数

**渲染层**
- [ ] 长列表使用虚拟滚动
- [ ] 避免在滚动/动画回调中触发 Layout
- [ ] 使用 CSS transform 代替 top/left 做动画

---

## 六、反思与经验

**做得好的地方：**
- {{描述}}

**如果重来会更早做的事：**
- {{描述，例如"更早建立性能监控，而不是等用户投诉才发现"}}

**认知提升：**
- 以前认为 {{旧认知}}，现在理解 {{新认知}}

---

*本文写于 {{日期}}*
