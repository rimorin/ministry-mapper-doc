# 前端设置指南

!!! warning "不建议自托管"
    本指南适用于想要自托管 Ministry Mapper 的高级用户。由于涉及技术复杂性、维护负担和安全责任，我们不鼓励自托管。

    **大多数用户应该**：使用我们在 **[ministry-mapper.com](https://ministry-mapper.com)** 上的托管服务

!!! info "需要用户文档？"
    如果您是普通用户，想了解如何使用 Ministry Mapper，请参阅[入门指南](getting-started.md)或[用户指南](user-guide.md)。

## 概述

Ministry Mapper 前端 (ministry-mapper-v2) 是一个基于 React 的 Web 应用程序，提供：

- 用户身份验证和账户管理
- 交互式区域管理界面
- 交互式地图功能
- 实时数据同步
- 移动端响应式设计
- 多语言支持（7 种语言）
- 渐进式 Web 应用功能

**本指南假设您具备：**
- Node.js 和 npm 经验
- 对环境变量的理解
- 熟悉静态网站部署
- Web 应用安全知识

有关完整的自托管说明，请参阅[自托管指南](self-hosting.md)。

## 快速参考

本页面提供前端配置的快速参考。**有关完整的设置说明，请参阅[自托管指南](self-hosting.md)。**

## 快速参考

本页面提供前端配置的快速参考。**有关完整的设置说明，请参阅[自托管指南](self-hosting.md)。**

### 技术栈

- **React 19**：现代 UI 库
- **TypeScript**：类型安全的 JavaScript
- **Vite 7**：快速构建工具和开发服务器
- **Bootstrap 5**：响应式 CSS 框架
- **Leaflet**：交互式地图
- **PocketBase SDK**：后端连接
- **Sentry**：错误跟踪
- **i18next**：多语言支持
- **Vite PWA**：渐进式 Web 应用功能

---

!!! note "完整设置说明"
    以下部分提供前端配置的技术参考。有关包含部署选项和故障排除的分步设置说明，请参阅 **[自托管指南](self-hosting.md)**。

---

## 环境变量

在根目录中创建一个 `.env` 文件，包含以下设置：

### 必需变量

```bash
# 系统环境 - 指定部署环境
VITE_SYSTEM_ENVIRONMENT=production

# 版本 - 自动使用 package.json 版本
VITE_VERSION=$npm_package_version

# PocketBase 后端 URL - 末尾无斜杠
VITE_POCKETBASE_URL=https://your-backend-url.com

# 法律页面 - 生产环境必需
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about

# 地图和路线
VITE_OPENROUTE_API_KEY=your_openrouteservice_api_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_api_key
```

### 可选变量

```bash
# 错误跟踪 (Sentry) - 建议用于生产环境
VITE_SENTRY_DSN=https://your_sentry_dsn@sentry.io/123456
# 构建时 Sentry 令牌用于源码映射上传
SENTRY_AUTH_TOKEN=your_sentry_auth_token
SENTRY_ORG=your_sentry_org_slug
SENTRY_PROJECT=your_sentry_project_slug

# 维护模式 - 向用户显示维护横幅
VITE_MAINTENANCE_MODE=false
```

### 变量详情

#### VITE_SYSTEM_ENVIRONMENT

- **用途**：确定应用运行的环境
- **值**：
  - `local` - 在本地机器上开发
  - `production` - 生产部署
- **效果**：影响 Sentry 错误跟踪配置和日志记录

#### VITE_VERSION

- **用途**：在错误报告中跟踪应用版本
- **值**：`$npm_package_version`（自动从 package.json 读取）
- **当前版本**：1.9.1（根据 package.json）
- **效果**：显示在 Sentry 报告中，帮助跟踪哪个版本有问题

#### VITE_POCKETBASE_URL

- **用途**：PocketBase 后端服务器的 URL
- **格式**：完整 URL，末尾无斜杠
- **示例**：`https://api.ministry-mapper.com` 或本地的 `http://localhost:8090`
- **重要**：必须从前端部署的位置可访问

#### VITE_PRIVACY_URL

- **用途**：链接到您的隐私政策页面
- **必需**：是的，用于法律合规（GDPR、CCPA 等）
- **示例**：`https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **用途**：链接到您的服务条款页面
- **必需**：是的，用于法律合规
- **示例**：`https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **用途**：链接到关于您的 Ministry Mapper 实例的信息
- **必需**：否，但建议
- **示例**：`https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **用途**：将 JavaScript 错误发送到 Sentry 进行监控
- **获取方式**：[sentry.io](https://sentry.io)（有免费层）
- **必需**：否，但强烈建议用于生产环境
- **优势**：跟踪错误、监控性能、获取问题通知
- **注意**：仅当 VITE_SYSTEM_ENVIRONMENT 为 "production" 时才激活

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **用途**：在 `npm run build` 期间用于将源码映射上传到 Sentry，以便将压缩的堆栈跟踪解析回原始源代码
- **必需**：否 — 如果这些缺失，源码映射会在本地生成，但上传到 Sentry 可以大大改善生产环境中的错误调试
- **获取方式**：在您的 Sentry 组织设置中的 **设置 → 认证令牌** 下创建认证令牌

#### VITE_OPENROUTE_API_KEY

- **用途**：在交互式地图上提供逐向导航和路线指引（驾车、步行、骑行）
- **获取方式**：[openrouteservice.org](https://openrouteservice.org)（有免费层）
- **必需**：是

#### VITE_LOCATIONIQ_API_KEY

- **用途**：地理编码（将地址转换为坐标）和反向地理编码（坐标转换为地址）
- **获取方式**：[locationiq.com](https://locationiq.com)（有免费层）
- **必需**：是

#### VITE_MAINTENANCE_MODE

- **用途**：设置为 `true` 时，向用户显示维护横幅
- **值**：`true` 或 `false`
- **默认**：`false`
- **使用场景**：在后端升级或计划停机期间临时设置为 `true`

## 本地开发设置

### 前提条件

您需要 Node.js **>=24.0.0**：

```bash
# 检查您的 Node.js 版本
node --version
```

如果您没有 Node.js 24+，请从以下位置下载：[nodejs.org](https://nodejs.org)

### 设置步骤

#### 1. 克隆仓库

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. 安装依赖

```bash
npm install
```

这会下载所有必需的包。根据您的网络连接，可能需要几分钟。

#### 3. 创建环境文件

```bash
# 复制示例环境文件
cp .env.example .env
```

使用您的设置编辑 `.env`：

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_OPENROUTE_API_KEY=
VITE_LOCATIONIQ_API_KEY=
VITE_MAINTENANCE_MODE=false
```

**注意**：您需要在端口 8090 上运行 PocketBase 后端。有关后端设置，请参阅 ministry-mapper-be 仓库。

#### 4. 启动开发服务器

```bash
npm start
```

应用将在 `http://localhost:3000` 打开（端口 3000 在 vite.config.js 中配置）。

### 开发功能

- **热模块替换 (HMR)**：更改会立即出现，无需完全重新加载页面
- **TypeScript 检查**：错误显示在终端和浏览器控制台中
- **源码映射**：使用原始源代码引用更容易调试
- **快速刷新**：React 组件更新而不丢失状态

### 开发命令

```bash
# 启动开发服务器（端口 3000）
npm start

# 使用 Vitest 运行测试
npm test

# 检查代码格式
npm run prettier

# 自动修复格式问题
npm run prettier:fix

# 构建生产版本
npm run build

# 本地预览生产构建
npm run serve
```

### 代码质量工具

**Prettier**（代码格式化）：

- 在 `.prettierrc` 中配置
- 通过 Husky 在提交时自动格式化
- 使用 `npm run prettier:fix` 手动运行

**ESLint**（代码检查）：

- 在 `eslint.config.mjs` 中配置
- 启用 React 和 TypeScript 规则
- 检查常见错误和代码质量问题

**Husky**（git 钩子）：

- 在提交前对暂存的文件运行 Prettier
- 确保一致的代码格式
- 使用 commitlint 验证提交消息

## 生产部署

### 构建生产版本

```bash
# 构建优化的生产文件
npm run build
```

**构建过程中发生的事情：**

- TypeScript 编译为 JavaScript
- React 代码被优化和压缩
- CSS 被处理和压缩
- 生成源码映射
- 代码被分割成块以加快加载
- 输出到 `build/` 目录
- 为 PWA 支持生成 service worker

**构建输出：**

- `build/index.html` - 主 HTML 文件
- `build/assets/` - JavaScript、CSS 和其他资源
- 文件名包含内容哈希以用于缓存
- 总大小通常低于 1MB（不包括外部依赖）

### 部署选项

Ministry Mapper 前端是一个静态 Web 应用程序。您可以将其部署到任何静态托管服务。

#### 选项 1：Vercel（推荐）

快速、免费层、自动部署。

**设置：**

1. 在 [vercel.com](https://vercel.com) 创建账户
2. 点击"新建项目"
3. 导入您的 GitHub 仓库
4. 配置：
   - 框架预设：Vite
   - 构建命令：`npm run build`
   - 输出目录：`build`
   - 安装命令：`npm install`
5. 在 Vercel 仪表板中添加环境变量
6. 部署

**Vercel 功能：**

- git 推送时自动部署
- Pull Request 的预览部署
- 自定义域名
- 默认 HTTPS
- 全球 CDN
- 个人项目免费

#### 选项 2：Netlify

简单部署，功能强大。

**设置：**

1. 在 [netlify.com](https://netlify.com) 创建账户
2. "添加新站点" → "导入现有项目"
3. 连接 GitHub 仓库
4. 构建设置：
   - 构建命令：`npm run build`
   - 发布目录：`build`
5. 添加环境变量
6. 部署

**Netlify 功能：**

- 自动部署
- 表单处理
- 无服务器函数（如需要）
- 自定义域名
- 默认 HTTPS

#### 选项 3：Cloudflare Pages

快速 CDN，免费层慷慨。

**设置：**

1. 在 [cloudflare.com](https://cloudflare.com) 创建账户
2. 转到 Pages → "创建项目"
3. 连接 GitHub 仓库
4. 构建设置：
   - 框架预设：Vite
   - 构建命令：`npm run build`
   - 构建输出目录：`build`
5. 添加环境变量
6. 部署

**Cloudflare 功能：**

- 全球 CDN 网络
- DDoS 防护
- 分析
- 无客户端跟踪的 Web 分析

#### 选项 4：AWS S3 + CloudFront

更多控制，适合大型部署。

**设置：**

1. 创建 S3 存储桶
2. 启用静态网站托管
3. 上传 `build/` 内容
4. 创建 CloudFront 分发
5. 配置自定义域名
6. 设置 SSL 证书

**AWS 注意事项：**

- 设置更复杂
- 按使用量付费（通常非常便宜）
- 更多配置选项
- 适合企业部署

#### 选项 5：自托管

在您自己的服务器上托管。

**使用 Nginx：**

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 启用 gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # 缓存静态资源
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**使用 Apache：**

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # 启用 React Router
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**自托管重要事项：**

- 通过 HTTPS 提供服务（PWA 功能所需）
- 如果后端在不同域上，配置正确的 CORS 标头
- 为静态资源设置正确的缓存标头
- 确保 SPA 路由有效（所有路由都提供 index.html）

### 部署中的环境变量

**重要**：环境变量必须在构建时设置，而不是运行时，因为 Vite 在构建时注入它们。

**对于 Vercel/Netlify/Cloudflare：**

- 在平台的仪表板中添加环境变量
- 它们将在构建过程中可用

**对于自托管：**

- 在运行 `npm run build` 之前设置环境变量
- 或创建包含您设置的 `.env.production` 文件

## 设置 Sentry（可选但推荐）

Sentry 监控 JavaScript 错误和性能。

### 1. 创建 Sentry 账户

1. 转到 [sentry.io](https://sentry.io)
2. 注册（有慷慨的免费层）
3. 创建新项目
4. 选择平台："React"

### 2. 获取您的 DSN

1. 创建项目后，您将看到 DSN
2. 或转到设置 → 项目 → [您的项目] → 客户端密钥 (DSN)
3. 复制 DSN URL

### 3. 添加到环境

```bash
VITE_SENTRY_DSN=https://your_key@your_org.sentry.io/your_project_id
```

**注意**：Sentry 仅在 `VITE_SYSTEM_ENVIRONMENT` 设置为 `production` 时才激活。

### 4. 配置 Sentry（可选）

**源码映射**：构建过程会自动生成源码映射，这有助于调试压缩的生产代码。

**警报**：在 Sentry 仪表板中设置：

- 新问题的电子邮件通知
- Slack/Discord 集成
- 关键错误的自定义警报规则
- 版本跟踪

## 多语言支持

Ministry Mapper 开箱即用支持 7 种语言。

### 支持的语言

- 英语 (`en`)
- 西班牙语 (`es` - Español)
- 日语 (`ja` - 日本語)
- 韩语 (`ko` - 한국어)
- 中文 (`zh` - 中文)
- 印度尼西亚语 (`id` - Bahasa Indonesia)
- 马来语 (`ms` - Bahasa Melayu)

### 语言检测工作原理

- 使用 `i18next-browser-languagedetector`
- 首次访问时自动从浏览器设置检测
- 用户可以通过导航栏中的语言选择器手动切换语言
- 选择持久化在 `localStorage` 中，优先于浏览器设置
- 如果不支持所选语言，则回退到英语

### 添加新语言

1. **创建翻译文件**

   - 转到 `src/i18n/locales/`
   - 创建新文件：`[language-code].json`（例如，法语的 `fr.json`）
   - 从 `en.json` 复制结构

2. **翻译所有字符串**

   - 翻译所有键值对
   - 保持相同的 JSON 结构
   - 保留像 `{{variable}}` 这样的占位符变量

3. **注册语言**
   - 编辑 `src/i18n/index.ts`
   - 导入您的翻译文件
   - 添加到资源对象

法语示例：

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... 其他语言
}
```

## 渐进式 Web 应用 (PWA)

Ministry Mapper 通过 Vite PWA 插件支持 PWA。

### PWA 功能

- **可安装**：可以安装到移动/桌面主屏幕
- **离线资源**：静态文件缓存以加快加载
- **Service Worker**：自动生成和注册
- **缓存策略**：外部资源使用 StaleWhileRevalidate，字体使用 CacheFirst

### PWA 配置

`vite.config.js` 中的配置：

```javascript
VitePWA({
  registerType: "autoUpdate", // 自动更新 service worker
  manifest: false, // 无清单（如需要可添加）
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // 立即激活新的 service worker
    clientsClaim: true, // 立即控制客户端
    cleanupOutdatedCaches: true, // 删除旧缓存
  },
});
```

### 缓存策略

**外部资源**（例如，来自 assets.ministry-mapper.com）：

- 策略：StaleWhileRevalidate
- 缓存名称："external-assets"
- 过期：7 天，最多 50 条目

**字体**：

- 策略：CacheFirst
- 缓存名称："fonts"
- 过期：30 天，最多 30 条目

**注意**：应用需要互联网进行数据操作（PocketBase 连接）。只有静态资源离线缓存。

## 测试您的部署

### 功能检查清单

- [ ] 主页正确加载
- [ ] 注册页面有效
- [ ] 电子邮件验证有效
- [ ] 登录页面有效
- [ ] OTP 身份验证有效（如已启用）
- [ ] 密码重置有效
- [ ] 区域选择器显示（对于负责人/管理员）
- [ ] 地图正确加载和显示
- [ ] 可以查看区域
- [ ] 可以更新地址状态
- [ ] 分配链接有效
- [ ] 链接正确过期
- [ ] 移动视图响应式
- [ ] 语言检测有效
- [ ] 所有模态框正确打开和关闭

### 性能检查

- [ ] 初始页面加载低于 3 秒
- [ ] Lighthouse 分数 > 90
- [ ] 无控制台错误
- [ ] 图像快速加载
- [ ] 地图响应式
- [ ] 滚动和导航流畅

### 安全检查

- [ ] 强制 HTTPS（无 HTTP 访问）
- [ ] 隐私政策链接有效
- [ ] 服务条款链接有效
- [ ] API 密钥未在浏览器代码中暴露
- [ ] 后端 CORS 正确配置
- [ ] PocketBase 连接安全

### 浏览器测试

在多个浏览器上测试：

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] 移动浏览器

## 故障排除

### 后端连接失败

**问题**："无法连接到服务器"或"网络错误"

**解决方案：**

- 验证 `VITE_POCKETBASE_URL` 正确（末尾无斜杠）
- 检查 PocketBase 后端是否正在运行
- 直接在浏览器中测试后端 URL
- 检查 PocketBase 中的 CORS 设置（必须允许您的前端域）
- 打开浏览器开发者工具 → 网络选项卡查看确切错误
- 确保后端从您的部署位置可访问

### 构建错误

**问题**：`npm run build` 失败

**解决方案：**

- 确保所有环境变量正确设置
- 运行 `npm install` 刷新依赖
- 删除 `node_modules` 并重新安装：`rm -rf node_modules && npm install`
- 检查 Node.js 版本：`node --version`（需要 24+）
- 检查 TypeScript 错误：查看具体错误消息
- 尝试 `npm run prettier:fix` 修复格式问题
- 清除 Vite 缓存：`rm -rf node_modules/.vite`

### TypeScript 错误

**问题**：构建期间类型错误

**解决方案：**

- 检查 `src/utils/interface.ts` 中的类型定义
- 确保导入正确
- 运行 `npx tsc --noEmit` 查看所有类型错误
- 验证所有依赖已安装

### 性能缓慢

**问题**：应用缓慢或卡顿

**解决方案：**

- 检查网络连接速度
- 清除浏览器缓存并重新加载
- 确保 PocketBase 后端响应快速
- 检查浏览器控制台是否有 JavaScript 错误
- 在不同设备/浏览器上测试以隔离问题
- 检查 Lighthouse 性能分数
- 验证 service worker 正在工作：开发者工具 → 应用程序 → Service Workers

### PWA 安装问题

**问题**："添加到主屏幕"不显示

**解决方案：**

- 必须通过 HTTPS 提供服务
- 在开发者工具中检查 service worker 注册
- 验证满足 PWA 要求（清单、service worker 等）
- 尝试不同的浏览器（Safari、Chrome 对 PWA 的支持不同）

### 实时更新不工作

**问题**：更改不会显示给其他用户

**解决方案：**

- 验证 PocketBase 实时订阅正在工作
- 检查浏览器控制台是否有 SSE（服务器发送事件）错误
- 确保后端 SSE 端点可访问
- 检查多个用户是否实际上正在查看同一区域
- 刷新页面以强制重新连接

## 维护

### 保持依赖更新

```bash
# 从仓库拉取最新更改
git pull origin main

# 安装更新的依赖
npm install

# 检查安全漏洞
npm audit

# 自动修复安全问题（如果可能）
npm audit fix

# 检查过时的包
npm outdated

# 更新特定包
npm update package-name
```

### 监控

**Sentry 仪表板：**

- 每周检查新错误
- 审查错误趋势
- 及时修复关键问题
- 为高优先级错误设置警报

**Google Cloud 控制台：**

- 监控 API 使用量以保持在配额内
- 检查使用量的异常峰值
- 审查计费警报
- 确保 API 保持启用

**性能：**

- 定期运行 Lighthouse 审计
- 监控页面加载时间
- 检查 Core Web Vitals
- 在慢速连接上测试

**用户反馈：**

- 听取会众成员的意见
- 跟踪常见问题
- 记录解决方案
- 根据需要更新文档

### 版本更新

Ministry Mapper 使用语义版本控制：

- **主版本** (x.0.0)：破坏性更改
- **次版本** (1.x.0)：新功能
- **补丁版本** (1.9.x)：错误修复

## 后续步骤

**对于用户：**

- 阅读[用户指南](user-guide.md)了解如何使用应用程序

**对于管理员：**

- 设置 PocketBase 后端（参见 ministry-mapper-be 仓库）
- 配置会众设置
- 邀请用户并分配角色
- 创建区域并添加地址
