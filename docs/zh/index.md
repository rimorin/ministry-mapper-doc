# 欢迎使用 Ministry Mapper

## 什么是 Ministry Mapper？

Ministry Mapper 是一款现代化的开源 Web 应用程序，帮助会众高效管理传道地区。它以数字化方式替代纸质地区条，提供完整的解决方案，可从任何联网设备组织、跟踪和管理外勤服务工作。

## 我们的故事

我们于 2022 年启动 Ministry Mapper，最初只是一个帮助本地会众更有效管理地区的小项目。随着越来越多的会众发现数字化地区管理的好处，我们持续完善该平台。如今，Ministry Mapper 已被新加坡和马来西亚的多个会众使用，通过实时同步、互动地图和多语言支持等功能，帮助传道员有序开展传道工作。

## Ministry Mapper 的功能

Ministry Mapper 提供全面的地区管理能力：

### 核心功能

- **🗺️ 地区管理**：将地理区域整理为地区，自动跟踪完成进度
- **🏢 互动地图**：基于 Leaflet 的地图，集成 OpenStreetMap，支持路线导航和定位
- **📱 实时协作**：通过 SSE（服务器发送事件）实现所有设备间的实时数据同步
- **🌍 多语言支持**：8 种语言（英语、西班牙语、印度尼西亚语、日语、韩语、马来语、中文、泰米尔语）
- **👥 基于角色的访问控制**：传道员、组长、管理员和只读角色，具有精细权限
- **🔗 分配链接**：安全的限时共享链接，无需用户账户即可访问地区
- **📊 进度跟踪**：自动完成状态，具有视觉指示器和统计数据
- **✉️ 邮件通知**：消息、备注和月度报告的 HTML 邮件模板
- **📈 Excel 报告**：包含详细统计数据的月度会众报告
- **🔐 多因素身份验证**：支持 OTP、MFA 和 OAuth2（Google）
- **🌙 深色模式**：系统感知主题，支持浅色/深色/系统选项
- **📲 渐进式 Web 应用**：可安装在移动设备和桌面，具有离线资源缓存

### 适用于不同用户

- **地区仆人**：通过全面的统计数据和报告，以数字方式管理所有地区记录
- **外勤服务组长**：使用智能算法快速分配地区，跟踪传道员活动
- **传道员**：通过简单链接访问指定地图，实时更新地址状态，高效协作
- **管理员**：完整的系统控制，包括用户管理、会众设置和审计记录

## Ministry Mapper 解决的问题

### 无纸化管理

传统纸质地区条容易丢失、损坏或随时间变得难以辨认。Ministry Mapper 将所有内容数字化保存在云端，可随时从手机、平板或电脑访问地区。

### 更好的组织

当多名传道员共用地区时，很容易重复拜访同一地址。Ministry Mapper 让所有人实时了解情况，避免重复工作。

### 即时更新

当有人更新地区时，所有人立即看到变更。无需等待纸条传递或开会分享更新。

### 更便捷的记录

无需在纸条上手写每次变更，Ministry Mapper 自动跟踪所有内容，节省大量时间并防止错误。

## Ministry Mapper 的工作原理

### 现代技术栈

Ministry Mapper 采用前沿 Web 技术以实现最佳性能：

**前端（ministry-mapper-v2）：**

- **React 19.2.4**：最新 UI 库，具有自动编译器优化
- **TypeScript 5.9.3**：类型安全开发，减少错误
- **Vite 8.0.2**：极速构建工具和开发服务器
- **Leaflet 1.9.4**：基于 OpenStreetMap 的互动地图（无 API 限制）
- **Bootstrap 5.3.8**：现代响应式 UI 框架
- **Wouter 3.9.0**：轻量级路由（3KB，而非 40KB 的替代方案）

**后端（ministry-mapper-be）：**

- **Go 1.25.0**：高性能编译语言
- **PocketBase 0.36.7**：自托管后端即服务，使用 SQLite
- **SQLite 1.40.2+**：可靠的自包含数据库（纯 Go 实现）
- **Echo v5**：极简高性能 Web 框架
- **Sentry 0.43.0**：实时错误跟踪和监控
- **MailerSend 1.6.3**：事务性电子邮件服务
- **LaunchDarkly 7.14.6**：功能标志管理
- **Excelize 2.10.1**：Excel 报告生成
- **OpenAI GPT**：AI 驱动的报告和消息摘要

### 架构亮点

- **自托管**：无供应商锁定，完全控制您的数据
- **多租户**：会众级数据隔离以保障安全
- **实时同步**：基于 SSE 的所有客户端实时更新
- **渐进式 Web 应用**：可在所有设备上安装，支持离线资源
- **Docker 容器化**：便于部署和扩展
- **API 优先设计**：RESTful API，全面支持 PocketBase SDK
- **AI 驱动洞察**：可选的 OpenAI GPT 集成，用于智能报告摘要和消息概要

## 重要注意事项

### 隐私优先

Ministry Mapper 跟踪住宅地址，因此您需要遵守当地的隐私法律。不同国家和地区有不同规定（如欧洲的 GDPR 或加利福尼亚的 CCPA）。使用 Ministry Mapper 前，请务必了解并遵守当地法规。

### 需要互联网连接

Ministry Mapper 通过互联网运行，因此需要连接才能使用。如果您所在地区网络不稳定，可能会遇到困难。该应用在移动数据网络下同样运行良好。

### 学习曲线

从纸质到数字化是一种改变，对不熟悉技术的人尤其如此。我们提供帮助和支持，使会众中的每个人都能顺利过渡。

## 快速入门

### 对于用户

使用 Ministry Mapper 最简单的方式是通过我们的托管服务：

**[ministry-mapper.com](https://ministry-mapper.com)**

只需：

1. 访问 [ministry-mapper.com](https://ministry-mapper.com)
2. 使用注册页面创建账户
3. 验证您的电子邮件地址
4. 联系您会众的管理员获取适当权限

**快速链接：**

- **[快速入门 →](getting-started.md)**
- **[功能概览 →](features.md)**
- **[用户指南 →](user-guide.md)**
- **[常见问题 →](faq.md)**

### 对于管理员

如果您正在为会众考虑 Ministry Mapper：

- **推荐**：使用我们在 [ministry-mapper.com](https://ministry-mapper.com) 的托管服务

  - 无需技术设置
  - 提供专业支持
  - 自动更新和维护
  - 更好的可靠性和安全性

- **替代方案**：查阅[部署指南](deployment.md)（仅限高级用户）

由于技术复杂性和持续维护要求，我们不鼓励自托管。ministry-mapper.com 的托管服务提供更简单、更可靠的解决方案。

**管理员资源：**

- **[快速入门指南 →](getting-started.md)**
- **[部署选项 →](deployment.md)**
- **[自托管指南 →](self-hosting.md)**

### 对于开发者

如果您对技术方面感兴趣或希望贡献：

**技术文档：**

- **[架构概览 →](architecture.md)** - 系统设计和技术决策
- **[数据模型和架构 →](data-models.md)** - 数据库结构和关系
- **[后端设置 →](backend-setup.md)** - PocketBase 配置
- **[前端设置 →](frontend-setup.md)** - React 应用设置

**开发：**

- **前端仓库：** [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
- **后端仓库：** [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
- **问题反馈：** 各仓库的 GitHub Issues
- **贡献：** 参见各仓库的 CONTRIBUTING.md 文件
