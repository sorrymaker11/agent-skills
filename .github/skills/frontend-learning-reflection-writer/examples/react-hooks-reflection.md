# React Hooks 学习心得：从"排斥"到"真香"

> 学习时间：2025年11月  
> 学习方式：官方文档 + Dan Abramov 博客 + 项目重构实践  
> 当前掌握程度：熟练

---

## 一、为什么要学它

说实话，我对 Hooks 有过一段时间的排斥。

第一次看到 `useState`、`useEffect` 时，觉得还不如 Class 组件直观——生命周期函数一目了然，`componentDidMount` 就是挂载，`componentDidUpdate` 就是更新，语义清楚。

但随着项目里 Class 组件越写越多，我开始体会到一些真实的痛苦：一个组件里的逻辑越来越多，`componentDidMount` 里又订阅事件、又发请求、又初始化第三方库，完全不相关的逻辑挤在同一个生命周期里。这才让我真正开始认真看 Hooks。

---

## 二、它到底解决了什么问题

在学 Hooks 之前，Class 组件的核心问题是：**逻辑复用很难做**。

- ❌ **Mixin 已废弃**：命名冲突、来源不清晰
- ❌ **HOC 嵌套地狱**：多层 HOC 包裹后，props 来源难以追踪
- ❌ **Render Props**：解决了复用，但带来了嵌套层级问题

Hooks 的核心思路是：

> 把**状态逻辑**从组件中抽离出来，封装成可复用的函数（Custom Hook），让多个组件共享逻辑而不共享状态。

---

## 三、核心概念速览

### 3.1 useState：状态的最小单元

与 Class 组件的 `this.state` 不同，`useState` 鼓励你把状态拆分成多个独立单元：

```javascript
// Class 组件：所有状态集中在一个对象里
this.state = { count: 0, name: '', loading: false }

// Hooks：按关注点分离
const [count, setCount] = useState(0)
const [name, setName] = useState('')
const [loading, setLoading] = useState(false)
```

好处是：每个状态的更新互不干扰，不需要 `this.setState({ ...this.state, count: 1 })` 这种繁琐写法。

### 3.2 useEffect：副作用的统一出口

`useEffect` 取代了 `componentDidMount`、`componentDidUpdate`、`componentWillUnmount` 三个生命周期，但它的思想完全不同——**它按"关注点"而非"时机"来组织代码**。

```javascript
// Before（Class）：按生命周期组织，不相关逻辑混在一起
componentDidMount() {
  // 订阅事件
  window.addEventListener('resize', this.handleResize)
  // 发请求
  this.fetchUserInfo()
  // 初始化第三方库
  this.chart = new Chart(this.chartRef)
}

// After（Hooks）：按关注点组织，每个 useEffect 只做一件事
useEffect(() => {
  window.addEventListener('resize', handleResize)
  return () => window.removeEventListener('resize', handleResize)
}, [])

useEffect(() => {
  fetchUserInfo()
}, [userId])

useEffect(() => {
  const chart = new Chart(chartRef.current)
  return () => chart.destroy()
}, [])
```

### 3.3 Custom Hook：真正的逻辑复用

这是 Hooks 最强大的地方。把相关的状态和副作用封装成一个自定义 Hook：

```javascript
// useWindowSize.js
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  })

  useEffect(() => {
    const handler = () => setSize({
      width: window.innerWidth,
      height: window.innerHeight
    })
    window.addEventListener('resize', handler)
    return () => window.removeEventListener('resize', handler)
  }, [])

  return size
}

// 任意组件直接使用，不需要 HOC 包裹
function Header() {
  const { width } = useWindowSize()
  return <div>{width > 768 ? '桌面端' : '移动端'}</div>
}
```

### 3.4 与 Class 组件的对比

| 维度 | Class 组件 | Hooks |
|------|-----------|-------|
| 状态管理 | `this.state` 集中管理 | `useState` 按关注点分散 |
| 逻辑复用 | HOC / Render Props | Custom Hook |
| 生命周期 | 按时机（mount/update/unmount）| 按关注点（useEffect） |
| `this` 指向 | 需要手动绑定，容易出 bug | 无 `this`，闭包变量 |
| 代码量 | 较多（constructor、bind 等样板代码）| 较少 |

---

## 四、上手实践：我做了什么

### 实践项目：将老项目的数据请求层迁移到 Custom Hook

**目标：** 验证 Custom Hook 是否真能简化数据请求逻辑，并统一处理 loading / error 状态

**封装的 `useFetch` Hook：**

```javascript
function useFetch(url, options = {}) {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  useEffect(() => {
    let cancelled = false
    setLoading(true)

    fetch(url, options)
      .then(res => res.json())
      .then(json => {
        if (!cancelled) {
          setData(json)
          setLoading(false)
        }
      })
      .catch(err => {
        if (!cancelled) {
          setError(err)
          setLoading(false)
        }
      })

    // 清理函数：组件卸载时取消请求副作用
    return () => { cancelled = true }
  }, [url])

  return { data, loading, error }
}

// 使用
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`)

  if (loading) return <Spinner />
  if (error) return <ErrorMessage message={error.message} />
  return <div>{user.name}</div>
}
```

迁移之后，10 多个页面的数据请求逻辑从平均 40 行缩减到了 5 行。

---

## 五、踩过的坑

### 坑一：useEffect 依赖数组写漏导致无限请求

**现象：** 页面一直在发请求，Network 面板刷不停

**原因：** 把对象作为 `useEffect` 的依赖，每次渲染都会生成新的对象引用，导致依赖判断为"改变了"

```javascript
// ❌ 错误：options 每次渲染都是新对象
useEffect(() => {
  fetchData(options)
}, [options]) // 无限循环！

// ✅ 正确：解构出基础类型值，或使用 useMemo
const memoOptions = useMemo(() => ({ page, size }), [page, size])
useEffect(() => {
  fetchData(memoOptions)
}, [memoOptions])
```

### 坑二：闭包陷阱导致 state 读到旧值

**现象：** 在定时器回调里读取 `count`，值永远是初始值 0

**现象代码：**
```javascript
const [count, setCount] = useState(0)

useEffect(() => {
  const timer = setInterval(() => {
    console.log(count) // 永远打印 0！
    setCount(count + 1) // 也因此一直是 0+1=1
  }, 1000)
  return () => clearInterval(timer)
}, []) // 空依赖数组，闭包捕获的 count 永远是初始值
```

**解决：使用函数式更新**

```javascript
setCount(prev => prev + 1) // 不依赖闭包里的 count，直接用上一次的值
```

---

## 六、最大的收获

1. **认知改变**：以前认为"生命周期"是组织代码的最佳维度，现在理解了"关注点"才是——同一个功能的初始化、更新、清理应该写在一起，而不是被拆散到三个生命周期里。

2. **思维迁移**：Custom Hook 本质是把逻辑和 UI 解耦，这个思想跟后端的"服务层"职责分离是一样的。以后遇到"组件里逻辑太多"的问题，第一反应就是抽 Custom Hook。

3. **实用收获**：`useFetch`、`useLocalStorage`、`useDebounce` 这些 Hook 已经沉淀到公司内部工具库，被多个项目复用。

---

## 七、还没搞清楚的

- [ ] `useReducer` 和 Redux 的边界在哪里，什么时候该用哪个
- [ ] `useTransition` 和 `useDeferredValue` 的 Concurrent Mode 场景
- [ ] Hooks 规则的底层实现（为什么不能在条件语句里调用 Hook）

---

## 八、后续计划

- [ ] 阅读 Dan Abramov 的《useEffect 完全指南》
- [ ] 研究 React Query / SWR 是如何基于 Hooks 封装数据请求的
- [ ] 将团队老项目的剩余 Class 组件逐步迁移

---

*本文写于 2025年11月，水平有限，欢迎交流指正。*
