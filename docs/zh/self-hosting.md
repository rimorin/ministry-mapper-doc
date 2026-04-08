# 自托管 Ministry Mapper

!!! warning "不推荐自托管"
    自托管 Ministry Mapper 需要大量技术专业知识和持续维护。**我们建议改用 [ministry-mapper.com](https://ministry-mapper.com) 的托管服务。** 只有在以下情况下才应继续自托管：
    
    - 拥有经验丰富的系统管理员或开发者
    - 有持续维护和更新的资源
    - 了解安全最佳实践
    - 能够遵守隐私法规
    - 有无法通过托管服务满足的特定需求

## 为什么我们不鼓励自托管

虽然 Ministry Mapper 是开源的，但自托管存在挑战：

- **技术复杂性**：需要了解 Docker、数据库、Web 服务器和云基础设施
- **安全责任**：您负责保持一切安全和更新
- **维护负担**：定期更新、备份和监控是必不可少的
- **隐私合规**：您必须确保 GDPR、CCPA 和其他隐私法律合规
- **成本**：服务器成本、API 成本和时间投入通常超过托管服务费用
- **支持**：自托管问题的社区支持有限

## 自托管先决条件

如果您在我们的建议下仍决定自托管，请确保您具备：

### 技术要求
- Linux 服务器管理经验
- 了解 Docker 和容器化
- 熟悉环境变量和配置
- 了解 Web 服务器配置（Nginx/Apache）
- 具有 SSL/TLS 证书经验
- 了解 DNS 和域名配置

### 基础设施要求
- 云托管服务（Railway、Render、DigitalOcean、AWS 等）
- 您实例的域名
- 用于通知的邮件服务（可选）
- 可选 API 成本（Sentry 等）的预算

### 时间承诺
- 初始设置：2-4 小时
- 持续维护：每月 2-4 小时
- 出现问题时的紧急支持

## 架构概述

Ministry Mapper 由两个独立组件组成：

1. **后端（ministry-mapper-be）**：带 Go 扩展的 PocketBase 服务器
   - 仓库：[github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - 技术：Go、PocketBase、SQLite
   - 在端口 8080（Docker）或 8090（本地）上运行

2. **前端（ministry-mapper-v2）**：React Web 应用程序
   - 仓库：[github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - 技术：React、TypeScript、Vite
   - 静态文件部署到托管服务

两者都必须部署并配置好才能协同工作。

## 后端自托管指南

### 环境配置

创建包含以下设置的 `.env` 文件：

```bash
# 应用程序设置
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-domain.com

# 初始管理员账户（首次登录后立即更改密码！）
PB_ADMIN_EMAIL=admin@your-domain.com
PB_ADMIN_PASSWORD=change_this_secure_password

# 邮件配置（SMTP）
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=your_app_password
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# 安全设置
PB_ALLOW_ORIGINS=https://your-frontend-domain.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# 身份验证（可选）
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# 错误跟踪（可选但推荐）
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# 服务集成（可选）
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Docker 部署

1. **克隆仓库**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **构建 Docker 镜像**
```bash
docker build -t ministry-mapper-backend .
```

3. **运行容器**
```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**重要说明：**
- 容器在内部端口 8080 上运行
- 将持久化卷挂载到 `/app/pb_data` 以保存数据
- 您的数据库位于 `pb_data` 文件夹中

4. **访问管理员面板**
导航到 `https://your-backend-domain.com/_/` 并使用管理员凭据登录。

### 本地开发

在您的本地机器上进行测试：

```bash
# 克隆仓库
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# 安装依赖
./scripts/install.sh

# 配置环境
cp .env.sample .env
# 使用您的设置编辑 .env

# 启动应用程序
./scripts/start.sh
```

后端默认在 `http://localhost:8090` 运行。

### 数据库备份

您的数据至关重要。设置自动备份：

```bash
# 手动备份
tar -czf backup-$(date +%Y%m%d).tar.gz /path/to/pb_data/

# 自动每日备份（添加到 crontab）
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /path/to/pb_data/
```

### 安全清单

- [ ] 首次登录后立即更改默认管理员密码
- [ ] 仅使用 HTTPS（永远不使用 HTTP）
- [ ] 将 `PB_ALLOW_ORIGINS` 设置为特定域名（不是 `*`）
- [ ] 启用 `PB_HIDE_CONTROLS=true`
- [ ] 启用 `PB_ENABLE_RATE_LIMITING=true`
- [ ] 配置防火墙以限制访问
- [ ] 设置自动备份
- [ ] 配置 Sentry 进行错误监控
- [ ] 使用强且唯一的密码
- [ ] 保持依赖项更新
- [ ] 查阅 PocketBase 安全最佳实践

## 前端自托管指南

### 环境配置

创建 `.env` 文件：

```bash
# 系统环境
VITE_SYSTEM_ENVIRONMENT=production

# 版本
VITE_VERSION=$npm_package_version

# PocketBase 后端 URL（无尾随斜杠）
VITE_POCKETBASE_URL=https://your-backend-domain.com

# 法律页面（必填）
VITE_PRIVACY_URL=https://your-domain.com/privacy
VITE_TERMS_URL=https://your-domain.com/terms
VITE_ABOUT_URL=https://your-domain.com/about

# 错误跟踪（可选但推荐）
VITE_SENTRY_DSN=your_sentry_dsn
```

### 生产构建

```bash
# 克隆仓库
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# 安装依赖
npm install

# 构建
npm run build
```

输出将在 `build/` 目录中。

### 部署选项

#### 静态托管服务（最简单）

**Vercel：**
1. 导入 GitHub 仓库
2. 框架：Vite
3. 构建命令：`npm run build`
4. 输出目录：`build`
5. 添加环境变量

**Netlify：**
1. 从 Git 创建新站点
2. 构建命令：`npm run build`
3. 发布目录：`build`
4. 添加环境变量

**Cloudflare Pages：**
1. 创建 Pages 项目
2. 框架：Vite
3. 构建命令：`npm run build`
4. 输出目录：`build`
5. 添加环境变量

#### 自托管 Web 服务器

**Nginx 配置：**

```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # 安全头
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**Apache 配置：**

```apache
<VirtualHost *:443>
    ServerName your-domain.com
    DocumentRoot /var/www/ministry-mapper/build
    
    SSLEngine on
    SSLCertificateFile /path/to/cert.pem
    SSLCertificateKeyFile /path/to/key.pem

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

## 维护和更新

### 定期维护任务

**每周：**
- 检查 Sentry 是否有错误
- 查看应用程序日志
- 验证备份是否正在工作

**每月：**
- 更新依赖项：`npm install` 和 `go get -u`
- 查阅安全公告
- 测试灾难恢复程序
- 检查磁盘空间使用情况
- 查看用户反馈

**每季度：**
- 安全审计
- 性能审查
- 更新文档
- 审查和更新隐私政策
- 彻底测试所有功能

### 更新到新版本

**后端更新：**
```bash
cd ministry-mapper-be
git pull origin main
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  -p 8080:8080 \
  -v /path/to/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**前端更新：**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# 将 build/ 文件夹部署到您的托管服务
```

**更新前始终备份！**

## 隐私和法律合规

### 自托管者的责任

自托管时，您是数据控制者，必须：

- **遵守隐私法律**：GDPR（欧盟）、CCPA（加利福尼亚）等
- **创建隐私政策**：说明数据收集和使用
- **创建服务条款**：定义用户责任
- **实施数据安全**：加密、访问控制、备份
- **处理数据请求**：用户访问、删除、携带权
- **报告数据泄露**：在法律规定的时间范围内
- **维护记录**：数据处理活动
- **任命 DPO**：如法规要求

### 所需法律文件

您必须提供：

1. **隐私政策** - 法律要求，必须说明：
   - 您收集哪些数据
   - 您如何使用
   - 您保留多长时间
   - 用户权利
   - 如何联系您

2. **服务条款** - 定义：
   - 可接受的使用
   - 用户责任
   - 责任限制
   - 终止政策

3. **Cookie 政策** - 如果您使用 Cookie/追踪

## 成本考虑

### 基础设施成本

**后端托管：**
- VPS：每月 $10-50（DigitalOcean、Linode）
- PaaS：每月 $10-30（Railway、Render）
- 云：不定（AWS、GCP）

**前端托管：**
- 静态托管：每月 $0-10（Vercel、Netlify、Cloudflare）

**域名：**
- 每年 $10-20

**SSL 证书：**
- 免费（Let's Encrypt）或每年 $0-100

### 时间投入

**初始设置：**
- 后端：2-3 小时
- 前端：1-2 小时
- 配置：1-2 小时
- 测试：2-3 小时
- **总计：6-10 小时**

**每月维护：**
- 监控：1-2 小时
- 更新：1-2 小时
- 支持：1-3 小时
- **总计：3-7 小时**

在做出承诺之前，与托管服务订阅进行比较。

## 获取帮助

### 社区支持

- GitHub Issues：[ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) 和 [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- 创建新 Issue 前先阅读现有 Issue
- 报告问题时提供详细信息

!!! info "支持限制"
    自托管支持基于社区且有限。Ministry Mapper 团队优先考虑托管服务。如需专业支持，请考虑改用托管服务。

## 自托管的替代方案

在自托管之前，请考虑：

1. **使用 [ministry-mapper.com](https://ministry-mapper.com) 的官方托管服务**
   - 无维护负担
   - 专业支持
   - 自动更新
   - 更好的可靠性
   - 合规为您处理
   - 现在即可使用！

2. **请求功能**
   - 如果您需要定制，请在主项目中请求功能
   - 惠及所有人
   - 由团队维护

3. **为项目做贡献**
   - 改进主代码库
   - 帮助他人
   - 获得社区支持

## 结论

自托管 Ministry Mapper 是可能的，但需要大量专业知识、时间和资源。**我们强烈建议使用 [ministry-mapper.com](https://ministry-mapper.com) 的托管服务**，除非您有需要额外复杂性的特定要求。

如果您仍要自托管：
- 遵循所有安全最佳实践
- 保持系统更新
- 维护定期备份
- 确保法律合规
- 监控系统健康
- 为持续成本做好预算
- 分配维护时间

祝您一切顺利！
