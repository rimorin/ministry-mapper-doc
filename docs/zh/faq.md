# 常见问题解答（FAQ）

## 常规问题

### 什么是 Ministry Mapper？

Ministry Mapper 是一款帮助会众以数字化方式管理外勤服务地区的现代 Web 应用程序。它用 React 构建前端，以 PocketBase 作为后端，使用 Leaflet 进行互动地图展示，让您无需使用纸质地区条，即可在任何联网设备上数字化地组织和跟踪一切。

### 如何访问 Ministry Mapper？

**推荐方式**：使用我们的托管服务 **[ministry-mapper.com](https://ministry-mapper.com)**

只需：
1. 访问 [ministry-mapper.com](https://ministry-mapper.com)
2. 使用注册页面创建账户
3. 验证您的电子邮件地址
4. 联系您会众的管理员获取适当权限

**替代方式**：自托管（不推荐）- 详情请参阅[自托管指南](self-hosting.md)。自托管需要大量技术专业知识和持续维护。

### Ministry Mapper 是免费的吗？

**托管服务**：访问 [ministry-mapper.com](https://ministry-mapper.com) 了解价格和方案。

**自托管**：源代码是免费且开源的，但您需要自行提供：
- 前端托管（如 Vercel、Netlify、AWS）
- PocketBase 后端部署
- 可选：用于错误监控的 Sentry 账户

注意：自托管费用（服务器、API 费用、维护时间）通常超过托管服务费用。

### Ministry Mapper 支持哪些语言？

目前支持的语言：

- 英语
- 日语（日本語）
- 韩语（한국語）
- 中文（中文）
- 印度尼西亚语（Bahasa Indonesia）
- 马来语（Bahasa Melayu）

界面会自动检测您的浏览器语言。您也可以在应用中手动切换语言。

### 使用 Ministry Mapper 需要技术知识吗？

**作为传道员/普通用户**：不需要技术知识！如果您会使用智能手机或电脑，就可以通过 [ministry-mapper.com](https://ministry-mapper.com) 的托管服务使用 Ministry Mapper。

**作为自托管管理员**：需要大量技术知识：

- Linux 服务器管理经验
- 了解 Docker 和容器化
- 熟悉环境变量和配置
- 了解 SSL/TLS 证书
- Web 服务器配置（Nginx/Apache）
- DNS 和域名配置

**我们强烈建议使用托管服务而非自托管。** 如果您仍想自托管，请参阅[自托管指南](self-hosting.md)。

### Ministry Mapper 可以离线使用吗？

不，应用正常运行需要互联网连接，因为：

- 它需要连接到 PocketBase 后端
- 用户之间的实时更新需要网络连接

如果没有 WiFi，应用在移动数据网络下也能正常运行。

## 隐私与法律

### 我需要考虑哪些隐私法律？

**重要提示**：Ministry Mapper 跟踪住宅地址，可能受数据隐私法律约束。这些法律因国家和地区而异：

- **欧洲**：GDPR（通用数据保护条例）
- **加利福尼亚**：CCPA（加利福尼亚消费者隐私法）
- **巴西**：LGPD（巴西通用数据保护法）
- **其他地区**：各地区法规

**请在使用 Ministry Mapper 之前，彻底了解您所在地的法规并确保合规。** 如需法律建议，请咨询法律专业人士。

**托管服务**：[ministry-mapper.com](https://ministry-mapper.com) 的托管服务可能提供合规协助，但您仍有责任遵守当地法律。

**自托管**：您对所有隐私法律合规负全责。

### Ministry Mapper 存储哪些数据？

**用户数据（由 PocketBase 管理）：**

- 电子邮件地址
- 姓名
- 密码（已加密）
- 用户角色和权限

**地区数据：**

- 地区边界和名称
- 地址
- 状态信息
- 住户备注
- 分配历史
- 更新时间戳

**技术数据（如启用了 Sentry）：**

- 错误日志
- 性能指标

所有数据存储均符合安全最佳实践。有关托管服务的数据政策，请查看 [ministry-mapper.com](https://ministry-mapper.com)。

## 技术设置（自托管）

!!! warning "不推荐自托管"
    以下部分适用于想要自托管 Ministry Mapper 的高级用户。**我们强烈建议改用 [ministry-mapper.com](https://ministry-mapper.com) 的托管服务。** 自托管需要大量技术专业知识、持续维护和安全责任。
    
    完整的自托管说明，请参阅[自托管指南](self-hosting.md)。

### 系统要求是什么？

**使用托管服务：**
- 现代 Web 浏览器（Chrome、Firefox、Safari、Edge）
- 互联网连接
- 电子邮件地址
- 移动设备或电脑

**自托管开发：**

- Node.js >= 22.0.0
- npm 包管理器
- Git
- Docker（用于后端）

**自托管生产：**

- 云托管服务（Railway、Render、DigitalOcean、AWS 等）
- 域名
- SSL/TLS 证书
- PocketBase 后端（单独部署）
- （可选）Sentry 账户用于监控
- （可选）用于通知的邮件服务

### 需要哪些环境变量？

**仅适用于自托管** - 如果您使用托管服务，本节不适用。

**前端（.env 文件）：**

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=your_pocketbase_backend_url
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
VITE_SENTRY_DSN=your_sentry_dsn（可选）
VITE_PRIVACY_URL=your_privacy_policy_url
VITE_TERMS_URL=your_terms_url
VITE_ABOUT_URL=your_about_page_url
```

**生产环境：** 与上面相同，但 `VITE_SYSTEM_ENVIRONMENT=production`

详情请参阅[前端设置指南](frontend-setup.md)。

### 如何部署前端？

**仅适用于自托管** - 如果您使用托管服务，请跳过此步骤。

1. **构建应用程序：**

   ```bash
   npm run build
   ```

2. 按上述说明**配置环境变量**

3. 将 `build/` 文件夹**部署**到您的托管提供商：

   - Vercel：连接您的 GitHub 仓库并配置环境变量
   - Netlify：拖放 build 文件夹或通过 Git 连接
   - AWS S3：上传 build 文件夹并配置静态网站托管

4. 确保 PocketBase 后端**已部署**，并可通过 `VITE_POCKETBASE_URL` 指定的 URL 访问

详细说明请参阅[前端设置指南](frontend-setup.md)和[自托管指南](self-hosting.md)。

### 如何设置 PocketBase 后端？

**仅适用于自托管** - 如果您使用托管服务，请跳过此步骤。

后端在单独的仓库中管理：[ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**快速概述：**

1. 克隆后端仓库
2. 配置环境变量（参见[后端设置指南](backend-setup.md)）
3. 使用 Docker 或 Railway/Render 部署
4. 用 schema 初始化数据库
5. 配置 SMTP 用于邮件通知（可选）

**详细说明：** 参见[后端设置指南](backend-setup.md)和[自托管指南](self-hosting.md)。

**重要**：部署前端之前，后端必须先部署并可访问。

### 如何设置 Sentry 监控？

**仅适用于自托管** - 如果您使用托管服务，请跳过此步骤。

1. 创建 [Sentry](https://sentry.io/) 账户
2. 创建 React 项目
3. 进入设置并获取 DSN 密钥
4. 配置以下环境变量：
   - `VITE_SENTRY_DSN`：您的 Sentry 项目 DSN
   - `VITE_SYSTEM_ENVIRONMENT`：生产环境设置为 "production"（影响追踪采样率）
   - `VITE_VERSION`：用于 Sentry 中的版本跟踪

注意：Sentry 是可选的，但强烈推荐用于生产部署。

## 开发

### 如何在本地运行应用？

**仅适用于开发者** - 本节适用于为 Ministry Mapper 做贡献的开发者。

1. **克隆仓库：**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **安装依赖：**

   ```bash
   npm install
   ```

3. 使用所需环境变量**配置 .env 文件**（参见 `.env.example`）

4. **启动开发服务器：**
   ```bash
   npm start
   ```

应用将在 `http://localhost:3000` 运行

注意：应用需要运行中的 PocketBase 后端才能正常工作。参见[后端设置指南](backend-setup.md)。

### 如何运行测试？

```bash
npm test
```

测试使用 Vitest 和 Testing Library 编写。测试文件与源文件放在一起，扩展名为 `.test.ts`。

### 如何格式化代码？

**检查格式化：**

```bash
npm run prettier
```

**应用格式化修复：**

```bash
npm run prettier:fix
```

项目使用 Prettier 进行代码格式化，并通过 Husky 配置了提交前钩子。

### 技术栈是什么？

- **前端框架**：带 TypeScript 的 React 19
- **构建工具**：Vite
- **UI 库**：React Bootstrap（Bootstrap 5）
- **地图**：带 OpenStreetMap 的 Leaflet
- **状态管理**：React Context + 自定义钩子
- **路由**：Wouter
- **样式**：SCSS
- **后端**：PocketBase（单独仓库）
- **监控**：Sentry
- **测试**：Vitest + Testing Library

详细架构信息请参阅 CLAUDE.md。

## 故障排除

### 应用显示"无法连接到后端"

**托管服务用户**：如果您看到此错误，服务可能正在遇到问题。检查状态页面或联系支持。

**自托管用户**：

**可能原因：**

1. PocketBase 后端未运行或无法访问
2. `VITE_POCKETBASE_URL` 环境变量不正确
3. CORS 问题（后端未允许前端域名）

**解决方案：**

- 验证后端是否正在运行且可访问
- 检查环境变量中的 URL
- 配置 PocketBase 中的 CORS 设置以允许您的前端域名

### 重新构建后更改未反映

**仅适用于自托管**：

**可能原因：**

1. 浏览器缓存
2. CDN 缓存（如果使用）
3. 构建产物未正确部署

**解决方案：**

- 清除浏览器缓存或使用无痕模式
- 如适用，使 CDN 缓存失效
- 验证 build 文件夹是否在托管服务上完全替换
- 检查浏览器控制台是否有错误

### 用户身份验证不起作用

**托管服务用户**：如果您遇到身份验证问题，请联系支持。

**自托管用户**：

**可能原因：**

1. PocketBase 后端用户管理未配置
2. 网络连接问题
3. PocketBase URL 不正确
4. CORS 配置问题

**解决方案：**

- 验证 PocketBase 后端是否正确配置
- 检查浏览器控制台的错误消息
- 直接测试后端 API 端点
- 检查 PocketBase 中的 CORS 设置

### 应用缓慢或无响应

**可能原因：**

1. 大型地区数据集
2. 网络延迟
3. 服务器资源不足（自托管）
4. 浏览器性能问题

**解决方案：**

- 检查您的网络连接
- 尝试使用不同的浏览器
- 自托管：优化地区数据（减少不必要的字段）
- 自托管：使用更近的托管区域
- 自托管：如需要，升级服务器资源
- 清除浏览器缓存和数据
- 检查浏览器控制台的性能警告

## 贡献

### 如何报告错误？

1. 在 [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues) 中检查是否已有相关报告
2. 如没有，创建新 Issue 并包含：
   - 问题的清晰描述
   - 重现步骤
   - 期望行为与实际行为
   - 浏览器和设备信息
   - 截图或错误消息
   - 您的环境配置（不含敏感数据）

### 我可以请求功能吗？

可以！在 GitHub 上创建功能请求，包含：

- 功能的清晰描述
- 使用场景和好处
- 功能如何工作
- 谁会从中受益

注意：这是一个由志愿者维护的项目，实施取决于可用时间和资源。

### 我可以贡献代码吗？

当然！欢迎贡献：

**如何贡献：**

1. Fork 仓库
2. 创建功能分支
3. 按照现有代码风格进行更改
4. 为新功能编写测试
5. 运行 `npm test` 和 `npm run prettier` 确保质量
6. 提交拉取请求并附上清晰描述

**贡献准则：**

- 遵循 TypeScript 和 React 最佳实践
- 使用代码库中现有的模式
- 如需要，更新文档
- 保持更改专注且精简
- 提交前彻底测试

架构详情和编码规范请参阅 CLAUDE.md。

### 我可以将应用翻译成新语言吗？

可以！应用使用国际化（i18n）进行翻译：

1. 查看[前端仓库](https://github.com/rimorin/ministry-mapper-v2)中的 `src/i18n/` 目录获取现有翻译
2. 按照现有结构创建新语言文件
3. 添加您的翻译
4. 更新语言选择器组件
5. 提交拉取请求

### 如何升级到新版本？

**托管服务用户**：更新是自动的，您无需做任何操作！

**自托管用户**：

**前端：**

```bash
git pull origin main
npm install
npm run build
# 将新 build 部署到您的托管服务
```

**后端：**
参考 [Ministry Mapper BE 仓库](https://github.com/rimorin/ministry-mapper-be)获取后端升级说明。

**重要：**

- 始终先备份数据
- 阅读 CHANGELOG.md 了解重大变更
- 如可能，在测试环境中先测试
- 如需要，更新环境变量

## 支持

### 在哪里获取帮助？

**托管服务用户**：通过 [ministry-mapper.com](https://ministry-mapper.com) 网站联系支持。

**所有用户**：

1. **文档**：查阅此文档网站，包括[快速入门](getting-started.md)、[用户指南](user-guide.md)和其他指南
2. **GitHub Issues**：在 [ministry-mapper-v2 仓库](https://github.com/rimorin/ministry-mapper-v2/issues)搜索现有 Issue 或创建新 Issue
3. **GitHub Discussions**：提问并分享想法
4. **代码审查**：阅读代码和注释了解实现细节（开发者）

### 是否有商业支持？

**托管服务**：通过 [ministry-mapper.com](https://ministry-mapper.com) 提供专业支持。

**自托管**：自托管实例没有官方商业支持。这是一个由志愿者维护的开源项目。但是：

- 通过 GitHub 的社区支持
- 全面的文档
- 您可以聘请独立开发者进行定制

### 应用多久更新一次？

**托管服务**：更新在可用时自动应用。

**自托管**：更新取决于志愿者的可用时间。请查看：

- **CHANGELOG.md** 获取版本历史
- **GitHub Releases** 获取发布说明
- **提交历史** 获取最近更改

### 我可以为我的需求定制 Ministry Mapper 吗？

可以！它是开源的，所以您可以：

- 修改 UI（颜色、布局、品牌）
- 添加自定义功能
- 更改工作流程
- 与其他系统集成

**要求：**

- 了解 React 19、TypeScript 和 PocketBase
- 理解代码库（参见仓库中的 CLAUDE.md）
- 彻底测试您的更改
- 考虑将改进贡献回项目

**注意**：定制需要自托管。托管服务使用标准版本。

---

## 还有问题？

1. **阅读文档**：
   - [快速入门指南](getting-started.md)
   - [用户指南](user-guide.md)
   - [自托管指南](self-hosting.md)（如果自托管）
   - [后端设置指南](backend-setup.md)（如果自托管）
   - [前端设置指南](frontend-setup.md)（如果自托管）
2. **搜索** [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
3. 在 **GitHub Discussions** 中提问
4. **联系支持**（托管服务用户）
5. 提交新 **Issue** 并附上您的问题

请记住：Ministry Mapper 是开源的，由志愿者维护。寻求帮助时请保持耐心和尊重！
