# 部署指南

## 概述

本指南涵盖 Ministry Mapper 的各种部署选项，从使用托管服务到高级自托管配置。

## 快速部署决策树

```
┌─────────────────────────────────────┐
│ 您是否具备技术专业知识              │
│ 和特定的自托管需求？                │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      是               否
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ 自托管       │              │ 使用托管服务     │
│              │              │                  │
│ 见下文       │              │ ministry-mapper  │
└──────────────┘              │ .com             │
                              └──────────────────┘
```

## 方案一：托管服务（推荐）

**最适合：** 大多数会众、中小型组织

**网址：** [ministry-mapper.com](https://ministry-mapper.com)

### 优势

✅ **零设置** - 立即开始使用
✅ **专业支持** - 需要时提供帮助
✅ **自动更新** - 始终使用最新版本
✅ **维护的基础设施** - 无需服务器管理
✅ **更好的安全性** - 专业安全实践
✅ **合规协助** - 帮助满足 GDPR、CCPA 等要求
✅ **包含备份** - 自动数据备份
✅ **可扩展性** - 自动处理增长
✅ **性价比高** - 通常比自托管便宜

### 入门步骤

1. 访问 [ministry-mapper.com](https://ministry-mapper.com)
2. 创建账户
3. 验证您的电子邮件
4. 联系管理员获取会众访问权限
5. 立即开始使用

**无需技术知识！**

---

## 方案二：自托管

**最适合：** 具有技术专业知识和特定要求的组织

**要求：**
- 经验丰富的系统管理员或开发者
- 了解 Docker、数据库和 Web 服务器
- 维护和更新系统的能力
- 了解安全最佳实践
- 隐私法律合规专业知识

### 自托管概述

Ministry Mapper 包含两个组件：

1. **后端** - 带 SQLite 的 PocketBase Go 应用程序
2. **前端** - React 静态 Web 应用程序

两者都必须部署并连接。

---

## 后端部署选项

### 方案一：Railway（推荐用于自托管）

**最适合：** 配置最少的简单自托管

**设置步骤：**

1. **创建 Railway 账户**
   ```
   访问：https://railway.app
   使用 GitHub 注册
   ```

2. **部署后端**
   ```bash
   # Fork 仓库
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be
   
   # 连接到 Railway
   railway init
   railway link
   ```

3. **配置环境**
   ```bash
   # 在 Railway 仪表板中添加环境变量
   PB_APP_URL=https://your-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password_here
   SENTRY_DSN=your_sentry_dsn
   ```

4. **部署**
   ```bash
   railway up
   ```

**特性：**
- 有免费套餐
- 自动 HTTPS
- 易于扩展
- 内置监控
- 一键部署

---

### 方案二：Render

**设置步骤：**

1. **创建 Render 账户**
   ```
   访问：https://render.com
   注册
   ```

2. **创建新 Web 服务**
   - 连接 GitHub 仓库
   - 选择 `ministry-mapper-be`
   - 选择 Docker 部署

3. **配置环境**
   ```
   PB_APP_URL=https://your-frontend-url.vercel.app
   PB_ADMIN_EMAIL=admin@example.com
   PB_ADMIN_PASSWORD=secure_password
   PB_ALLOW_ORIGINS=https://your-frontend-url.vercel.app
   ```

4. **部署**
   - Render 自动构建和部署
   - 首次部署需要 5-10 分钟

**特性：**
- 有限制的免费套餐
- 自动 HTTPS
- 易于扩展
- GitHub 集成

---

### 方案三：DigitalOcean Droplet

**最适合：** 完全控制、生产部署

**设置步骤：**

1. **创建 Droplet**
   ```bash
   # 推荐 Ubuntu 22.04 LTS
   # 最低配置：1GB 内存、1 CPU、25GB SSD
   # 推荐配置：2GB 内存、2 CPU、50GB SSD
   ```

2. **安装 Docker**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **克隆仓库**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **配置环境**
   ```bash
   cp .env.sample .env
   nano .env  # 使用您的设置进行编辑
   ```

5. **构建并运行**
   ```bash
   docker-compose up -d
   ```

6. **设置 Nginx 反向代理**
   ```nginx
   server {
       listen 80;
       server_name api.your-domain.com;
   
       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

7. **使用 Certbot 设置 SSL**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.your-domain.com
   ```

**特性：**
- 完全控制
- 可预测的定价（每月 $6-12）
- 高性能
- 自定义配置

---

### 方案四：AWS（高级）

**最适合：** 企业部署、高可扩展性需求

**架构：**
```
Route 53（DNS）
    ↓
CloudFront（CDN）
    ↓
应用程序负载均衡器
    ↓
ECS Fargate（容器）
    ↓
RDS PostgreSQL（如果从 SQLite 迁移）
    ↓
S3（文件存储）
```

**设置概述：**

1. 创建 ECS 集群
2. 构建 Docker 镜像并推送到 ECR
3. 创建任务定义
4. 配置负载均衡器
5. 设置 CloudFront CDN
6. 配置 Route 53 DNS

**成本：** 每月约 $50-200，取决于使用量

**复杂性：** 高 - 需要 AWS 专业知识

---

## 前端部署选项

### 方案一：Vercel（推荐）

**最适合：** 具有出色开发体验的快速简单部署

**设置步骤：**

1. **创建 Vercel 账户**
   ```
   访问：https://vercel.com
   使用 GitHub 注册
   ```

2. **导入仓库**
   ```
   - 点击"新建项目"
   - 导入 ministry-mapper-v2
   - 框架：Vite
   - 构建命令：npm run build
   - 输出目录：build
   ```

3. **配置环境变量**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_VERSION=$npm_package_version
   VITE_OPENROUTE_API_KEY=your_key
   VITE_LOCATIONIQ_API_KEY=your_key
   VITE_POCKETBASE_URL=https://your-backend-url.com
   VITE_PRIVACY_URL=https://your-site.com/privacy
   VITE_TERMS_URL=https://your-site.com/terms
   VITE_SENTRY_DSN=your_sentry_dsn
   ```

4. **部署**
   - Vercel 在推送时自动部署
   - 需要 2-3 分钟

**特性：**
- 个人项目免费
- 自动 HTTPS
- 边缘网络（全球快速）
- PR 的预览部署
- 自定义域名

---

### 方案二：Netlify

**设置步骤：**

1. 创建 Netlify 账户
2. 导入仓库
   - 连接 GitHub
   - 选择 `ministry-mapper-v2`
3. 构建设置
   ```
   构建命令：npm run build
   发布目录：build
   ```
4. 添加环境变量
5. 部署

---

### 方案三：Cloudflare Pages

**设置步骤：**

1. 创建 Cloudflare 账户
2. 创建 Pages 项目
   - 连接 GitHub
   - 选择仓库
3. 构建配置
   ```
   框架：Vite
   构建命令：npm run build
   输出目录：build
   ```
4. 设置环境变量
5. 部署

**特性：**
- 无限带宽
- 全球 CDN
- DDoS 防护
- Web 分析

---

### 方案四：AWS S3 + CloudFront

**设置步骤：**

1. **创建 S3 存储桶**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **配置静态托管**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **上传构建**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. 创建 CloudFront 分发
5. 配置自定义域名

**成本：** 小型站点每月约 $1-5

---

## 环境配置

### 后端环境变量

**必填：**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://your-frontend-url.com
PB_ADMIN_EMAIL=admin@example.com
PB_ADMIN_PASSWORD=secure_password
PB_ALLOW_ORIGINS=https://your-frontend-url.com
```

**SMTP（邮件）：**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=your_email@gmail.com
PB_SMTP_PASSWORD=app_password
PB_SMTP_SENDER_ADDRESS=noreply@your-domain.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**安全：**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**监控：**
```bash
SENTRY_DSN=your_sentry_dsn
SENTRY_ENV=production
```

**可选服务：**
```bash
MAILERSEND_API_KEY=your_key
LAUNCHDARKLY_SDK_KEY=your_key
```

### 前端环境变量

**必填：**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://your-backend.com
```

**法律页面：**
```bash
VITE_PRIVACY_URL=https://your-site.com/privacy
VITE_TERMS_URL=https://your-site.com/terms
VITE_ABOUT_URL=https://your-site.com/about
```

**可选 API：**
```bash
VITE_OPENROUTE_API_KEY=your_openrouteservice_key
VITE_LOCATIONIQ_API_KEY=your_locationiq_key
```

**监控：**
```bash
VITE_SENTRY_DSN=your_sentry_dsn
```

---

## SSL/TLS 证书设置

### 方案一：Let's Encrypt（免费）

**使用 Certbot：**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

**自动续期：**
```bash
sudo certbot renew --dry-run
```

### 方案二：Cloudflare SSL

- 包含 Cloudflare 的免费 SSL
- 自动续期
- 通用 SSL

### 方案三：云提供商 SSL

大多数云提供商（Vercel、Netlify、Railway）包含自动 SSL

---

## 数据库备份策略

### 自动备份

**每日备份脚本：**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# 创建备份
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# 压缩
gzip $BACKUP_DIR/backup_$DATE.db

# 保留最近 30 天
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Cron 调度：**
```bash
0 2 * * * /path/to/backup-script.sh
```

### 云备份

**AWS S3：**
```bash
aws s3 sync /backups/ s3://your-bucket/ministry-mapper-backups/
```

---

## 监控与日志

### Sentry 设置

1. 在 [sentry.io](https://sentry.io) 创建账户
2. 创建新项目（React + Go）
3. 复制 DSN
4. 添加到环境变量
5. 配置警报

**好处：**
- 实时错误跟踪
- 性能监控
- 版本跟踪
- 用户反馈

---

## 安全最佳实践

### 后端安全

1. **使用强密码**
   - 最少 12 个字符
   - 包含字母、数字和符号

2. **启用速率限制**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **配置 CORS**
   ```bash
   PB_ALLOW_ORIGINS=https://your-frontend-url.com
   ```

4. **定期更新**
   ```bash
   # 每月检查更新
   docker pull your-image:latest
   ```

5. **防火墙规则**
   ```bash
   # 只允许端口 80、443、22（SSH）
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

---

## 成本估算

### 托管服务
- 联系 ministry-mapper.com 了解价格
- 包含支持、更新、备份

### 自托管成本

**最简设置：**
- Railway/Render：每月 $0-5（免费套餐）
- Vercel/Netlify：每月 $0（免费套餐）
- **合计：每月 $0-5**

**推荐设置：**
- 后端（Railway）：每月 $5
- 前端（Vercel）：每月 $0
- Sentry：每月 $0（免费套餐）
- **合计：每月 $5**

**生产设置：**
- DigitalOcean Droplet：每月 $12
- CloudFront CDN：每月 $2
- Sentry Pro：每月 $26
- **合计：每月 $40**

**企业设置：**
- AWS 基础设施：每月 $100-500
- 支持合同：不定
- **合计：每月 $100-500+**

**时间投入：**
- 初始设置：4-8 小时
- 每月维护：2-4 小时
- 紧急支持：视需要而定

---

## 支持资源

### 文档
- [架构概览](architecture.md)
- [后端设置](backend-setup.md)
- [前端设置](frontend-setup.md)

### 社区
- GitHub Issues
- 文档网站
- 社区论坛

### 专业支持
- 托管服务支持
- 咨询服务
- 自定义开发
