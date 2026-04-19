# 后端设置指南

!!! warning "不推荐自托管"
    本指南适用于想要自托管 Ministry Mapper 的高级用户。由于技术复杂性、维护负担和安全责任，我们不鼓励自托管。
    
    **大多数用户应该**：使用我们在 **[ministry-mapper.com](https://ministry-mapper.com)** 的托管服务

!!! info "需要用户文档？"
    如果您是普通用户，想了解如何使用 Ministry Mapper，请查阅[快速入门](getting-started.md)或[用户指南](user-guide.md)。

## 概述

Ministry Mapper 后端（ministry-mapper-be）使用 Go 和 PocketBase 构建，提供：

- 数据存储和管理（SQLite 数据库）
- 用户身份验证和授权
- 实时数据同步
- 地区管理的自定义 API 端点
- 自动化任务的后台任务
- 通过 SMTP 发送邮件通知

**本指南假设您具备**：
- 服务器管理经验
- 了解 Docker 和容器化
- 熟悉环境变量
- 了解安全最佳实践

完整的自托管说明，请参阅[自托管指南](self-hosting.md)。

## 快速参考

本页面提供后端配置的快速参考。**完整设置说明，请参阅[自托管指南](self-hosting.md)。**

### 技术栈

- **Go**：快速高效的编程语言
- **PocketBase**：带内置管理员面板的后端即服务
- **SQLite**：`pb_data` 文件夹中的轻量级数据库
- **Docker**：基于 Alpine Linux 的容器

### 主要功能

- 数据存储和管理
- 用户身份验证
- 实时订阅（服务器发送事件）
- 自定义 API 端点
- 计划后台任务
- 邮件通知（SMTP）
- 错误跟踪（Sentry）
- 功能标志（LaunchDarkly）

---

## 环境变量说明

### 服务集成密钥（可选）

```bash
# MailerSend API 密钥 - 通过 MailerSend 服务发送邮件
# 如果改用 SMTP，请留空
MAILERSEND_API_KEY=

# MailerSend 发件人的电子邮件地址
MAILERSEND_FROM_EMAIL=

# LaunchDarkly SDK 密钥 - 用于控制后台任务的功能标志
# 如果未配置，任务默认运行
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# OpenAI API 密钥 - 用于月度报告和消息摘要中的 AI 生成摘要
# 可选：报告和摘要在没有此项的情况下也能正常工作；AI 部分简单地被省略
OPENAI_API_KEY=
```

#### 邮件（MailerSend）

| 变量 | 必填 | 描述 |
|------|------|------|
| `MAILERSEND_API_KEY` | 可选 | MailerSend 事务性邮件发送 API 密钥（用于报告和生命周期邮件）。未设置时回退到 SMTP。 |
| `MAILERSEND_FROM_EMAIL` | 可选 | MailerSend 邮件的发件人地址。 |

#### 功能标志（LaunchDarkly）

| 变量 | 必填 | 描述 |
|------|------|------|
| `LAUNCHDARKLY_SDK_KEY` | 可选 | LaunchDarkly 服务端 SDK 密钥。使用功能标志控制的后台任务时必须填写。 |
| `LAUNCHDARKLY_CONTEXT_KEY` | 可选 | 标识当前部署环境的 LaunchDarkly 上下文密钥。 |

#### AI 摘要（OpenAI）

| 变量 | 必填 | 描述 |
|------|------|------|
| `OPENAI_API_KEY` | 可选 | OpenAI API 密钥。仅在通过功能标志启用 AI 生成报告摘要时需要。 |

### 应用程序设置

```bash
# 在邮件和管理员面板中显示的名称
PB_APP_NAME=Ministry Mapper

# 您的前端托管的 URL
PB_APP_URL=http://localhost:3000
```

### 默认管理员账户

```bash
# 首次设置时创建的初始管理员账户
# 首次登录后立即更改密码！
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### 邮件配置（SMTP）

```bash
# SMTP 服务器地址（如 smtp.gmail.com、smtp.sendgrid.net）
PB_SMTP_HOST=

# SMTP 密码（Gmail 使用应用密码，而非您的常规密码）
PB_SMTP_PASSWORD=

# SMTP 用户名（通常是您的电子邮件地址）
PB_SMTP_USERNAME=

# SMTP 端口（TLS 使用 587，SSL 使用 465）
PB_SMTP_PORT=587

# 在"发件人"字段中显示的电子邮件地址
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# 在"发件人"字段中显示的名称
PB_SMTP_SENDER_NAME=MM Support
```

### 安全和访问控制

```bash
# CORS 允许的来源列表（逗号分隔）
# 开发环境使用 *，生产环境使用特定域名
# 示例：https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# 对公众隐藏管理员 UI（true/false）
# 生产环境设置为 true 以确保安全
PB_HIDE_CONTROLS=false
```

### 身份验证设置

```bash
# 启用一次性密码登录（true/false）
PB_OTP_ENABLED=false

# 启用多因素身份验证（true/false）
PB_MFA_ENABLED=false
```

### 速率限制

```bash
# 启用速率限制以防止 API 滥用（true/false）
PB_ENABLE_RATE_LIMITING=false
```

### 错误跟踪（不在 .env.sample 中 - 如需要请手动添加）

```bash
# 用于错误监控和跟踪的 Sentry DSN
SENTRY_DSN=

# Sentry 的环境名称（development、staging、production）
SENTRY_ENV=production

# Git 提交哈希 - 由 Coolify 等托管平台自动设置
SOURCE_COMMIT=
```

## 功能标志

Ministry Mapper 使用 [LaunchDarkly](https://launchdarkly.com) 来控制后台任务的执行。每个任务可以在不重新部署的情况下独立启用或禁用。功能标志需要配置 `LAUNCHDARKLY_SDK_KEY` 和 `LAUNCHDARKLY_CONTEXT_KEY`。

| 标志键 | 控制的任务 | 描述 |
|--------|-----------|------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | 删除过期的地图分配（每 5 分钟运行） |
| `enable-territory-aggregations` | `updateTerritoryAggregates` | 重新计算地区进度统计（每 10 分钟运行） |
| `enable-message-processing` | `processMessages` | 处理待处理的传道员消息（每 30 分钟运行） |
| `enable-instruction-processing` | `processInstructions` | 处理地区分配指令（每 30 分钟运行） |
| `enable-note-processing` | `processNotes` | 处理更新的会众备注（每小时运行） |
| `enable-monthly-report` | `generateMonthlyReport` | 生成并通过邮件发送 Excel 报告（每月 1 日运行） |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | 警告并禁用没有分配角色的用户（每日运行） |
| `enable-inactive-user-processing` | `processInactiveUsers` | 警告并禁用不活跃账户——符合 NIST AC-2(3)（每日运行） |
| `enable-new-addresses-notification` | `processNewAddresses` | 发送应用内新增地址的每日摘要邮件（每日运行） |
| `enable-report-ai-summary` | 报告中的 AI 摘要 | 在会众报告中包含 OpenAI 生成的摘要 |

!!! note
    如果未配置 LaunchDarkly，所有任务默认运行（启用：true）。

## 安装步骤

### 方案一：Docker 部署（推荐）

Docker 使部署在各平台上简单且一致。

#### 1. 构建 Docker 镜像

```bash
docker build -t ministry-mapper-backend .
```

#### 2. 运行容器

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**重要说明：**

- 容器在端口 **8080** 上运行（不是 8090）
- `-p 8080:8080` 将容器的 8080 端口映射到主机的 8080 端口
- `-v /path/to/pb_data:/app/pb_data` 永久保存您的数据库（将 `/path/to/pb_data` 替换为系统上的真实路径）
- `--env-file .env` 从 `.env` 文件加载环境变量
- `-d` 在后台运行容器（分离模式）

### 方案二：本地开发

在您的计算机上进行开发和测试：

#### 1. 安装 Go 依赖

```bash
./scripts/install.sh
```

此脚本运行 `go get ./...` 和 `go mod tidy` 以安装所有依赖项。

#### 2. 设置环境变量

```bash
cp .env.sample .env
# 使用您的设置编辑 .env
```

#### 3. 运行应用程序

```bash
./scripts/start.sh
```

后端默认在 **http://localhost:8090** 启动（PocketBase 默认端口）。

## 访问管理员面板

后端运行后：

1. 导航到 `http://your-backend-url/_/`（注意下划线和尾随斜杠）
2. 使用 `PB_ADMIN_EMAIL` 和 `PB_ADMIN_PASSWORD` 中的管理员凭据登录
3. 您将看到 PocketBase 管理员仪表板

### 管理员面板中可以做什么

- **集合**：查看和编辑所有数据库表（用户、会众、地区、地图、地址等）
- **日志**：查看应用程序和请求日志
- **设置**：配置身份验证、邮件、备份等
- **文件**：管理上传的文件和存储
- **API 规则**：设置每个集合的权限

**安全说明**：在生产环境中设置 `PB_HIDE_CONTROLS=true` 以对公众隐藏管理员 UI。

## 数据库管理

### 备份您的数据

您的数据位于 `pb_data` 文件夹中。定期备份至关重要。

#### 手动备份

```bash
# 创建整个 pb_data 文件夹的备份
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/
```

#### 自动每日备份

设置 cron 任务自动备份：

```bash
# 添加到 crontab（运行：crontab -e）
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

#### 从备份恢复

```bash
# 先停止应用程序
# 然后解压备份
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /path/to/
```

## 安全清单

上线前：

- [ ] 首次登录后立即更改默认管理员密码
- [ ] 将 `PB_ALLOW_ORIGINS` 设置为您的实际域名，而不是 `*`
- [ ] 启用 HTTPS（生产环境中永远不使用 HTTP）
- [ ] 设置 `PB_HIDE_CONTROLS=true` 以对未授权访问隐藏管理员 UI
- [ ] 考虑使用 `PB_ENABLE_RATE_LIMITING=true` 启用速率限制
- [ ] 配置 Sentry（`SENTRY_DSN` 和 `SENTRY_ENV`）进行错误监控
- [ ] 设置自动每日备份
- [ ] 为所有账户使用强且唯一的密码
- [ ] Gmail SMTP 使用应用密码而非常规密码
- [ ] 使用 `./scripts/update.sh` 定期更新 Go 依赖项

## 更新后端

更新到新版本：

### Docker 更新

```bash
# 拉取最新代码
git pull origin main

# 重新构建 Docker 镜像
docker build -t ministry-mapper-backend .

# 停止并删除旧容器
docker stop ministry-mapper
docker rm ministry-mapper

# 启动新容器（使用端口 8080，不是 8090）
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### 本地开发更新

```bash
# 拉取最新代码
git pull origin main

# 更新依赖项
./scripts/update.sh

# 重启应用程序
./scripts/start.sh
```

**注意**：更新期间 `pb_data` 中的数据会被保留。

## 监控和日志

### 查看日志

#### Docker 日志

```bash
# 查看最近日志
docker logs ministry-mapper

# 实时跟踪日志（调试时有用）
docker logs -f ministry-mapper

# 查看最后 100 行
docker logs --tail 100 ministry-mapper
```

#### 应用程序日志

PocketBase 将日志存储在 `pb_data/logs/` 文件夹中。包括：

- 请求日志
- 错误日志
- 自定义应用程序日志

## 后台任务（Cron 调度器）

后端使用 cron 调度器运行自动化后台任务。这些任务由 LaunchDarkly 功能标志控制：

### 计划任务

1. **地区聚合**（`*/10 * * * *` - 每 10 分钟）
   - 更新地区统计数据和聚合数据
   - 功能标志：`enable-territory-aggregations`

2. **分配清理**（`*/5 * * * *` - 每 5 分钟）
   - 删除过期或无效的地区分配
   - 功能标志：`enable-assignments-cleanup`

3. **消息处理**（`*/30 * * * *` - 每 30 分钟）
   - 处理队列中的待处理消息
   - 设置 `OPENAI_API_KEY` 时可选择附加 AI 生成的概述
   - 功能标志：`enable-message-processing`

4. **指令处理**（`*/30 * * * *` - 每 30 分钟）
   - 处理待处理的地区分配指令
   - 功能标志：`enable-instruction-processing`

5. **备注处理**（`0 * * * *` - 每小时）
   - 处理会众的更新备注
   - 设置 `OPENAI_API_KEY` 时可选择附加 AI 生成的摘要
   - 功能标志：`enable-note-processing`

6. **月度报告**（`0 0 1 * *` - 每月 1 日午夜）
   - 生成会众数据的 Excel 报告并通过邮件发送给管理员
   - 功能标志：`enable-monthly-report`
   - AI 摘要标志：`enable-report-ai-summary`（需要 `OPENAI_API_KEY`）

7. **未配置用户处理**（`0 1 * * *` - 每日 01:00 UTC）
   - 对没有角色分配的账户执行用户生命周期：
     第 3 天→首次警告邮件，第 6 天→最终警告邮件，第 7 天→账户禁用，第 37 天+→永久删除
   - 功能标志：`enable-unprovisioned-user-processing`

8. **不活跃用户处理**（`30 1 * * *` - 每日 01:30 UTC）
   - 警告并最终禁用超过配置阈值未活跃的账户
   - 功能标志：`enable-inactive-user-processing`

**注意**：如果未配置 LaunchDarkly，所有任务默认运行（启用：true）。

### AI 报告摘要

当配置了 `OPENAI_API_KEY` **且** `enable-report-ai-summary` LaunchDarkly 标志开启时，月度 Excel 报告会为每个地区工作表包含 AI 生成的摘要。AI 分析活动数据并生成 2-3 句概述及关键观察列表。

此功能完全可选——没有它报告也能完全正常工作。启用方法：

1. 在您的 `.env` 文件中设置 `OPENAI_API_KEY`。
2. 在您的 LaunchDarkly 项目中启用 `enable-report-ai-summary` 标志（或完全省略 LaunchDarkly，标志默认启用）。

## API 端点

后端提供以下自定义已认证 API 端点（所有端点均需用户认证）：

### 地图管理
- `POST /map/codes` - 获取特定地图的所有地图代码
- `POST /map/code/add` - 向地图添加新地址代码
- `POST /map/codes/update` - 更新地图代码的顺序/排序
- `POST /map/code/delete` - 从地图删除地址代码
- `POST /map/add` - 创建新地图
- `POST /map/territory/update` - 更新地图所属的地区
- `POST /map/reset` - 重置地图（清除所有分配和数据）

### 楼层管理
- `POST /map/floor/add` - 向地图添加楼层
- `POST /map/floor/remove` - 从地图删除楼层

### 地区管理
- `POST /territory/reset` - 重置地区（清除分配）
- `POST /territory/link` - 为地区创建快速访问链接

### 配置
- `POST /options/update` - 更新会众选项/设置

## 故障排除

### 端口已被占用

**问题**："端口已被占用"错误

**解决方案**：

```bash
# Docker（端口 8080）
lsof -i :8080

# 本地开发（端口 8090）
lsof -i :8090

# 终止使用该端口的进程
kill -9 <process_id>
```

### 数据库被锁定

**问题**："数据库被锁定"错误

**解决方案**：
- 停止访问数据库的所有容器/进程
- 确保只有一个应用程序实例在运行
- SQLite 不支持多进程并发写入
- 重启应用程序

### 邮件不发送

**问题**：用户没有收到邮件

**解决方案**：
1. **验证 SMTP 设置**：检查 `.env` 中的所有 `PB_SMTP_*` 变量
2. **Gmail 用户**：使用[应用密码](https://support.google.com/accounts/answer/185833)，而非常规密码
3. **检查垃圾邮件文件夹**：邮件可能被过滤
4. **查看日志**：在 `pb_data/logs/` 或 Docker 日志中查找错误消息
5. **端口问题**：确保防火墙没有阻止端口 587（TLS）或 465（SSL）

## 性能提示

### 小型会众（< 100 用户）
- 默认设置运行良好
- 512MB 内存已足够
- SQLite 可以轻松处理此负载
- 不需要特殊配置

### 中型会众（100-500 用户）
- 推荐：1GB 内存
- 启用速率限制：`PB_ENABLE_RATE_LIMITING=true`
- 设置每日自动备份
- 监控 `pb_data/data.db` 中的数据库大小

### 大型会众（> 500 用户）
- 推荐：2GB+ 内存
- 启用速率限制
- 考虑专用托管（非共享）
- 监控日志中的慢查询
- 设置备份保留策略（保留最近 30 天）
- 考虑使用 LaunchDarkly 功能标志控制后台任务频率

## 下一步

- 设置[前端应用程序](frontend-setup.md)
- 了解[用户管理](user-guide.md)
