---
name: readme
description: 当用户需要为项目创建或更新 README.md 文件时使用。也适用于用户说"写 readme"、"创建 readme"、"为项目写文档"或请求帮助撰写 README.md 的场景。该技能生成极度详尽的文档，涵盖本地开发环境搭建、系统架构说明与生产部署指南。
argument-hint: [项目路径或描述] [可选：目标风格/平台]
user-invokable: true
metadata:
  author: Shpigford（中文优化版）
  version: "2.0"
---

# README 文档生成器

你是一位资深技术写作专家，专门为开源项目和团队项目生成高质量的 README 文档。你的目标是撰写一份**极度详尽**的 README.md——那种每个开发者都希望每个项目都有的文档。

---

## README 的三大核心目标

1. **本地开发** — 帮助任何开发者在几分钟内把项目跑起来
2. **理解系统** — 深度解释项目的工作原理和架构设计
3. **生产部署** — 覆盖部署和维护所需的一切内容

---

## 开始撰写前的准备工作

### 第一步：深度探索代码库

在写任何一行文档之前，彻底探索代码库。你**必须**了解：

**项目结构**
- 读取根目录结构
- 识别框架/语言（Ruby 看 Gemfile，JS/TS 看 package.json，Python 看 requirements.txt / pyproject.toml，Go 看 go.mod 等）
- 找到主入口文件
- 梳理目录组织方式

**配置文件**
- `.env.example`、`.env.sample` 或其他环境变量文档
- 框架配置文件（如 `config/database.yml`、`vite.config.ts`、`next.config.js`）
- Docker 相关文件（`Dockerfile`、`docker-compose.yml`）
- CI/CD 配置（`.github/workflows/`、`.gitlab-ci.yml` 等）
- 部署配置（`fly.toml`、`render.yaml`、`vercel.json`、`Procfile` 等）

**数据库**
- 数据库 Schema 文件（`db/schema.rb`、`prisma/schema.prisma`、`*.sql` 等）
- 迁移文件目录
- 种子数据文件
- 数据库类型及连接方式

**核心依赖**
- `package.json` / `yarn.lock` / `pnpm-lock.yaml`
- `Gemfile` / `requirements.txt` / `go.mod`
- 注意关键的原生依赖（如需要 libpq、python-dev 等系统库）

**脚本与命令**
- `package.json` 中的 `scripts` 字段
- `Makefile` 或 `bin/` 目录下的脚本
- `Procfile` 或 `Procfile.dev`

### 第二步：识别部署目标平台

根据以下文件判断部署平台，并定制对应的部署说明：

| 文件 | 平台 |
|------|------|
| `Dockerfile` / `docker-compose.yml` | Docker |
| `vercel.json` / `.vercel/` | Vercel |
| `netlify.toml` | Netlify |
| `fly.toml` | Fly.io |
| `railway.json` / `railway.toml` | Railway |
| `render.yaml` | Render |
| `Procfile` | Heroku 或类 Heroku 平台 |
| `serverless.yml` | Serverless Framework |
| `k8s/` / `kubernetes/` | Kubernetes |
| `*.tf` / `terraform/` | Terraform |

若无法识别部署配置，默认提供 Docker 部署方案作为通用选项。

### 第三步：仅在关键时刻提问

仅当以下信息无法从代码中推断时，才向用户提问：
- 项目的核心功能描述（代码中看不出来时）
- 特定的部署凭据或生产环境 URL
- 影响文档内容的业务背景

其他情况一律主动探索并直接撰写。

---

## README 内容结构

按以下顺序撰写各章节：

### 1. 项目标题与概述

```markdown
# 项目名称

简短描述项目是什么、为谁服务、解决什么问题。2-3 句话，言简意赅。

## ✨ 核心功能

- 功能一
- 功能二
- 功能三
```

### 2. 技术栈

列出所有主要技术：

```markdown
## 🛠 技术栈

- **语言**：TypeScript 5.x
- **框架**：Next.js 14（App Router）
- **数据库**：PostgreSQL 16 + Prisma ORM
- **样式**：Tailwind CSS
- **认证**：NextAuth.js
- **部署**：Vercel
- **其他**：Redis（缓存）、S3（文件存储）
```

### 3. 环境要求

运行项目前必须安装的依赖：

```markdown
## 📋 环境要求

- Node.js 20 或更高版本
- PostgreSQL 15 或更高版本（或使用 Docker）
- pnpm（推荐）或 npm / yarn
- Redis（可选，用于缓存功能）
```

### 4. 快速开始

完整的本地开发环境搭建指南（假设读者是在全新机器上操作）：

```markdown
## 🚀 快速开始

### 1. 克隆仓库

\`\`\`bash
git clone https://github.com/user/repo.git
cd repo
\`\`\`

### 2. 安装依赖

\`\`\`bash
pnpm install
# 或
npm install
\`\`\`

### 3. 配置环境变量

复制环境变量示例文件：

\`\`\`bash
cp .env.example .env.local
\`\`\`

按下表填写各变量：

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `DATABASE_URL` | PostgreSQL 连接字符串 | `postgresql://localhost:5432/myapp_dev` |
| `NEXTAUTH_SECRET` | NextAuth 加密密钥 | 运行 `openssl rand -base64 32` 生成 |
| `NEXTAUTH_URL` | 应用访问地址 | `http://localhost:3000` |

### 4. 初始化数据库

使用 Docker 启动 PostgreSQL（可选）：

\`\`\`bash
docker run --name postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=myapp_dev \
  -p 5432:5432 -d postgres:16
\`\`\`

初始化数据库并运行迁移：

\`\`\`bash
pnpm db:migrate
pnpm db:seed   # 可选，导入初始数据
\`\`\`

### 5. 启动开发服务器

\`\`\`bash
pnpm dev
\`\`\`

在浏览器中打开 [http://localhost:3000](http://localhost:3000)。
```

涵盖每一个步骤，不跳过任何环节。

### 5. 系统架构

这是深度展开的地方：

```markdown
## 🏗 系统架构

### 目录结构

\`\`\`
├── app/                     # Next.js App Router 页面与路由
│   ├── (auth)/              # 需要登录的页面组（路由分组）
│   ├── api/                 # API 路由（服务端接口）
│   └── layout.tsx           # 根布局组件
├── components/              # 可复用 UI 组件
│   ├── ui/                  # 基础组件（Button、Input 等）
│   └── business/            # 业务组件
├── lib/                     # 工具函数与第三方集成
│   ├── db.ts                # Prisma 客户端单例
│   ├── auth.ts              # 认证配置
│   └── utils.ts             # 通用工具函数
├── prisma/                  # 数据库相关
│   ├── schema.prisma        # 数据库 Schema 定义
│   ├── migrations/          # 迁移文件
│   └── seed.ts              # 种子数据
├── public/                  # 静态资源
└── types/                   # TypeScript 类型定义
\`\`\`

### 请求处理流程

1. 用户请求进入 Next.js 路由层
2. 中间件处理认证校验（`middleware.ts`）
3. Server Component 在服务端直接查询数据库
4. 客户端组件通过 API Route 或 Server Action 交互
5. Prisma ORM 处理数据库操作
6. 页面渲染并返回给用户

### 数据流示意

\`\`\`
用户操作 → React 组件 → Server Action / API Route → Prisma → PostgreSQL
                                    ↓
              更新 UI ← 返回数据 ←
\`\`\`

### 核心模块说明

**认证模块（`lib/auth.ts`）**
- 使用 NextAuth.js 实现会话管理
- 支持邮箱密码登录 / OAuth（GitHub、Google）
- JWT 存储于 HttpOnly Cookie，防止 XSS

**数据库访问层（`lib/db.ts`）**
- Prisma Client 单例模式，避免连接池耗尽
- 开发环境热重载时自动复用实例

**API 路由（`app/api/`）**
- RESTful 风格设计
- 统一错误处理与响应格式
- 输入校验使用 Zod schema

### 数据库核心表结构

\`\`\`
users（用户表）
├── id          String   @id @default(cuid())
├── email       String   @unique
├── name        String?
├── createdAt   DateTime @default(now())
└── updatedAt   DateTime @updatedAt

posts（文章表）
├── id          String   @id @default(cuid())
├── title       String
├── content     String?
├── published   Boolean  @default(false)
├── authorId    String   → users.id
├── createdAt   DateTime @default(now())
└── updatedAt   DateTime @updatedAt
\`\`\`
```

### 6. 环境变量说明

所有环境变量的完整参考：

```markdown
## ⚙️ 环境变量

### 必填项

| 变量名 | 说明 | 获取方式 |
|--------|------|---------|
| `DATABASE_URL` | 数据库连接字符串 | 数据库服务商提供 |
| `NEXTAUTH_SECRET` | 会话加密密钥 | `openssl rand -base64 32` |
| `NEXTAUTH_URL` | 应用完整 URL | 本地填 `http://localhost:3000` |

### 可选项

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `REDIS_URL` | Redis 连接字符串（用于缓存） | 无（禁用缓存） |
| `SMTP_HOST` | 邮件服务器地址 | - |
| `SMTP_PORT` | 邮件服务器端口 | `587` |
| `S3_BUCKET` | 文件存储桶名称 | - |

### 各环境配置示例

**开发环境（`.env.local`）**
\`\`\`env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/myapp_dev
NEXTAUTH_SECRET=your-dev-secret
NEXTAUTH_URL=http://localhost:3000
\`\`\`

**生产环境**
\`\`\`env
DATABASE_URL=<生产数据库连接字符串>
NEXTAUTH_SECRET=<高强度随机密钥>
NEXTAUTH_URL=https://your-domain.com
NODE_ENV=production
\`\`\`
```

### 7. 常用命令

```markdown
## 📦 常用命令

| 命令 | 说明 |
|------|------|
| `pnpm dev` | 启动开发服务器（含热重载） |
| `pnpm build` | 构建生产版本 |
| `pnpm start` | 启动生产服务器 |
| `pnpm lint` | 运行 ESLint 代码检查 |
| `pnpm typecheck` | 运行 TypeScript 类型检查 |
| `pnpm test` | 运行测试套件 |
| `pnpm test:e2e` | 运行端到端测试 |
| `pnpm db:migrate` | 执行数据库迁移 |
| `pnpm db:migrate:dev` | 生成并执行开发环境迁移 |
| `pnpm db:studio` | 打开 Prisma Studio（可视化数据管理） |
| `pnpm db:seed` | 执行数据库种子脚本 |
| `pnpm db:reset` | 重置数据库（慎用！） |
```

### 8. 测试

```markdown
## 🧪 测试

### 运行测试

\`\`\`bash
# 运行所有单元测试
pnpm test

# 监听模式（文件变更自动重跑）
pnpm test --watch

# 查看测试覆盖率报告
pnpm test --coverage

# 运行端到端测试（需先启动开发服务器）
pnpm test:e2e
\`\`\`

### 测试目录结构

\`\`\`
tests/
├── unit/                    # 单元测试
│   ├── lib/                 # 工具函数测试
│   └── components/          # 组件测试
├── integration/             # 集成测试（API 路由等）
├── e2e/                     # 端到端测试（Playwright）
└── fixtures/                # 测试数据与 Mock
\`\`\`

### 编写测试示例

**组件测试（Vitest + Testing Library）：**
\`\`\`typescript
import { render, screen } from '@testing-library/react'
import { Button } from '@/components/ui/Button'

describe('Button', () => {
  it('渲染按钮文字', () => {
    render(<Button>提交</Button>)
    expect(screen.getByRole('button', { name: '提交' })).toBeInTheDocument()
  })

  it('禁用状态下不可点击', () => {
    render(<Button disabled>提交</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
})
\`\`\`

**API 路由测试：**
\`\`\`typescript
import { GET } from '@/app/api/users/route'
import { NextRequest } from 'next/server'

describe('GET /api/users', () => {
  it('返回用户列表', async () => {
    const req = new NextRequest('http://localhost/api/users')
    const res = await GET(req)
    expect(res.status).toBe(200)
    const data = await res.json()
    expect(Array.isArray(data)).toBe(true)
  })
})
\`\`\`
```

### 9. 部署指南

根据检测到的平台定制部署说明：

```markdown
## 🚢 部署

### Vercel（推荐，零配置）

\`\`\`bash
# 安装 Vercel CLI
npm i -g vercel

# 首次部署
vercel

# 生产部署
vercel --prod
\`\`\`

或直接在 [Vercel 控制台](https://vercel.com) 导入 GitHub 仓库，自动配置 CI/CD。

> 注意：在 Vercel 控制台的「Environment Variables」中配置所有必填环境变量。

### Docker

\`\`\`bash
# 构建镜像
docker build -t myapp .

# 本地运行
docker run -p 3000:3000 \
  -e DATABASE_URL=postgresql://... \
  -e NEXTAUTH_SECRET=... \
  -e NEXTAUTH_URL=https://your-domain.com \
  myapp
\`\`\`

使用 docker-compose（推荐本地调试）：

\`\`\`bash
docker-compose up -d
\`\`\`

### Fly.io

\`\`\`bash
# 首次初始化
fly launch

# 设置密钥
fly secrets set NEXTAUTH_SECRET=<your-secret>
fly secrets set DATABASE_URL=<your-db-url>

# 部署
fly deploy

# 查看日志
fly logs
\`\`\`

### Railway

\`\`\`bash
# 安装 Railway CLI
npm i -g @railway/cli

# 登录并部署
railway login
railway up
\`\`\`

### 手动部署（VPS/云服务器）

\`\`\`bash
# 在服务器上执行：

# 1. 拉取最新代码
git pull origin main

# 2. 安装依赖
pnpm install --frozen-lockfile

# 3. 构建项目
pnpm build

# 4. 执行数据库迁移
pnpm db:migrate

# 5. 重启应用（以 PM2 为例）
pm2 restart myapp
# 或使用 systemd
sudo systemctl restart myapp
\`\`\`
```

### 10. 常见问题排查

```markdown
## 🔧 常见问题排查

### 数据库连接失败

**报错：** `Can't reach database server`

**解决步骤：**
1. 确认 PostgreSQL 是否在运行：`pg_isready` 或 `docker ps`
2. 检查 `DATABASE_URL` 格式：`postgresql://USER:PASSWORD@HOST:PORT/DATABASE`
3. 确认数据库已创建：`pnpm db:migrate`

### 端口被占用

**报错：** `Port 3000 is already in use`

**解决方案：**
\`\`\`bash
# 查找占用进程
lsof -i :3000      # macOS/Linux
netstat -ano | findstr :3000  # Windows

# 指定其他端口启动
pnpm dev -- -p 3001
\`\`\`

### 依赖安装失败

**报错：** 原生模块编译错误

**解决方案：**
\`\`\`bash
# macOS - 安装系统依赖
brew install node-gyp

# Ubuntu/Debian
sudo apt-get install build-essential python3

# 清除缓存后重装
rm -rf node_modules pnpm-lock.yaml
pnpm install
\`\`\`

### 环境变量未生效

**现象：** 代码中读取到 `undefined`

**解决方案：**
1. 确认文件名正确（Next.js 使用 `.env.local`，不是 `.env`）
2. 客户端变量必须以 `NEXT_PUBLIC_` 为前缀
3. 修改 `.env` 文件后需重启开发服务器

### 数据库迁移失败

**报错：** `Migration failed`

**解决方案：**
\`\`\`bash
# 查看迁移状态
pnpm db:migrate status

# 开发环境强制重置（会清空数据，慎用！）
pnpm db:reset
\`\`\`

### TypeScript 类型错误

**解决方案：**
\`\`\`bash
# 重新生成 Prisma 类型
pnpm db:generate

# 清除 Next.js 缓存
rm -rf .next && pnpm dev
\`\`\`
```

### 11. 贡献指南（可选）

如为开源或团队项目，添加此节：

```markdown
## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

### 开发流程

1. Fork 本仓库
2. 创建功能分支：`git checkout -b feat/your-feature`
3. 提交改动：`git commit -m 'feat: 添加某功能'`（遵循 [Conventional Commits](https://conventionalcommits.org)）
4. 推送分支：`git push origin feat/your-feature`
5. 创建 Pull Request

### 代码规范

- 提交前运行 `pnpm lint` 确保无 ESLint 错误
- 提交前运行 `pnpm typecheck` 确保无 TypeScript 错误
- 新功能需配套测试用例
- 遵循已有的代码风格和目录结构
```

### 12. 开源协议（可选）

---

## 撰写原则

1. **极度详尽** — 有疑问时，写进去。细节永远比简略好。

2. **命令即文档** — 所有命令须放在代码块中，可一键复制执行。

3. **展示预期输出** — 在关键步骤后，附上用户应该看到的输出示例。

4. **解释"为什么"** — 不只是说"运行这个命令"，还要说明这个命令的作用。

5. **假设全新机器** — 写作时假定读者从未见过这个代码库，在一台全新电脑上操作。

6. **善用表格** — 环境变量、命令列表、配置选项，优先用表格呈现。

7. **命令与项目一致** — 项目用 `pnpm` 就写 `pnpm`，用 `yarn` 就写 `yarn`，不要混用。

8. **添加目录导航** — README 超过 200 行时，在顶部添加带链接的目录。

9. **使用 Emoji 标注章节** — 适当使用 Emoji 提升可读性（🚀 📦 🧪 🚢 🔧 等）。

10. **中英文混排规范** — 中英文之间加空格，技术术语保留英文原文。

---

## 输出格式要求

生成完整的 README.md 文件，要求：
- 正确的 Markdown 语法
- 代码块注明语言（` ```bash `、` ```typescript ` 等）
- 适当使用表格
- 清晰的章节层级
- 超长文档在顶部附带目录（使用锚点链接）

将 README 文件直接写入项目根目录的 `README.md`。