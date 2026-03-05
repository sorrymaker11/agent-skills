# Vite 迁移实践：把 Webpack 项目提速 8 倍的经历

> 探索时间：2025年10月  
> 项目背景：Vue 2 + Webpack 4，约 800 个模块，中台管理系统  
> 结论速览：冷启动从 47s → 6s，HMR 从 3s → 150ms，值得迁移

---

## 一、为什么要换

### 当前痛点

| 问题 | 具体表现 |
|------|---------|
| 构建速度 | 冷启动 47 秒，热更新平均 3 秒 |
| 配置复杂度 | webpack.config.js 超过 400 行，新人几乎无法维护 |
| 构建失败排查 | 报错信息不友好，定位问题平均要 20 分钟 |

开始调研时对比了 Vite、esbuild、Rspack 三个方案。Rspack 与 Webpack API 兼容度高，迁移成本最低；Vite 生态最成熟，开发体验最好。最终选 Vite，因为团队成员已有 Vite 经验。

---

## 二、安装与基础配置

```bash
npm install vite @vitejs/plugin-vue2 vite-plugin-legacy --save-dev
```

**最小可用配置：**

```javascript
// vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue2'
import legacy from '@vitejs/plugin-legacy'
import path from 'path'

export default defineConfig({
  plugins: [
    vue(),
    legacy({ targets: ['> 0.5%', 'IE 11'] })
  ],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': 'http://localhost:8080'
    }
  }
})
```

> ⚠️ 注意：Vite 默认只支持 ES 模块，`require()` 语法需要处理（见迁移过程第 3.2 节）

---

## 三、迁移过程

### 改动清单

- [x] 新增 `vite.config.js`，删除 `webpack.config.js`
- [x] 修改 `package.json` scripts
- [x] 修改 `index.html`（从 dist 移到根目录，手动引入入口）
- [x] 处理环境变量（`process.env.VUE_APP_*` → `import.meta.env.VITE_*`）
- [x] 替换 CommonJS `require()` 为 ESM `import`
- [x] 处理 CSS Modules 与全局样式注入

### 兼容性处理

| 原配置/插件 | 迁移方案 | 是否完全等价 |
|-------------|---------|------------|
| `webpack-bundle-analyzer` | `rollup-plugin-visualizer` | ✅ 功能相同 |
| `babel-loader` | Vite 内置 esbuild 转译 | ⚠️ 少数实验性语法需额外配置 |
| `sass-loader` | `npm install sass` 即可，无需 loader | ✅ |
| `copy-webpack-plugin` | Vite `public/` 目录约定 | ✅ 更简单 |
| SVG sprite loader | `vite-plugin-svg-icons` | ✅ |
| `process.env.NODE_ENV` | `import.meta.env.MODE` | ⚠️ 需全局替换 |

### 最棘手的问题：CommonJS 动态 require

项目里有大量基于 Webpack 的动态加载写法：

```javascript
// ❌ Vite 不支持
const icon = require(`@/assets/icons/${name}.svg`)
const component = require(`./modules/${moduleName}`)
```

**解决方案：使用 Vite 的 `import.meta.glob`**

```javascript
// ✅ Vite 方式
const icons = import.meta.glob('@/assets/icons/*.svg', { eager: true })
const icon = icons[`/src/assets/icons/${name}.svg`]?.default

// 动态路由组件
const modules = import.meta.glob('./modules/*.vue')
const component = () => modules[`./modules/${moduleName}.vue`]()
```

---

## 四、性能对比数据

> 测试环境：MacBook Pro M1 / 16GB / macOS Sonoma / 项目：约 800 模块 / 300+ 页面

### 构建速度

| 指标 | Webpack 4 | Vite 5 | 提升 |
|------|-----------|--------|------|
| 冷启动时间 | 47s | 6s | **7.8 倍** |
| 热更新（HMR）| 2~4s | 80~200ms | **约 20 倍** |
| 生产构建 | 128s | 89s | **1.4 倍** |

### 产物体积

| 资源类型 | Webpack | Vite | 变化 |
|---------|---------|------|------|
| JS 总体积 | 4.2MB | 3.8MB | -9.5% |
| CSS 总体积 | 890KB | 820KB | -7.9% |

> 生产构建提升幅度较小，是因为 Vite 生产模式用的是 Rollup（与 esbuild 不同），这是 Vite 已知的取舍。

---

## 五、遇到的问题

### 问题一：第三方库使用 CommonJS 导致打包失败

**错误信息：**
```
[vite]: Rollup failed to resolve import "process" from "xxx/node_modules/some-lib/index.js"
```

**解决：** 在 `vite.config.js` 中声明 `optimizeDeps`

```javascript
export default defineConfig({
  optimizeDeps: {
    include: ['some-lib'],    // 强制预构建
    exclude: ['other-lib']   // 排除不需要预构建的
  }
})
```

### 问题二：生产环境样式与开发环境不一致

**原因：** 我们的全局样式用了 `style-resources-loader` 注入变量，Vite 需要改用 `preprocessorOptions`

```javascript
// vite.config.js
css: {
  preprocessorOptions: {
    scss: {
      additionalData: `@import "@/styles/variables.scss";`
    }
  }
}
```

---

## 六、总结与建议

**适合迁移的项目类型：**
- ✅ Vue 2/3 或 React 项目
- ✅ 技术债较少、第三方依赖相对规范的项目
- ✅ 开发体验已成为团队效率瓶颈的项目

**暂不建议迁移的情况：**
- ⚠️ 大量 CommonJS 动态 require 且无法改造（改造成本超过收益）
- ⚠️ 依赖 Webpack 特有功能（如 Module Federation 多应用架构）

**综合评分：** ⭐⭐⭐⭐⭐（5/5）

**最终建议：** 如果你的项目冷启动超过 30 秒，强烈建议迁移 Vite。迁移成本约 1~3 天，此后每天节省的等待时间会快速收回成本。

---

*本文写于 2025年10月*
