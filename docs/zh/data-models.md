# 数据模型与架构

## 概述

Ministry Mapper 通过 PocketBase 使用 SQLite，采用层级数据模型，专为多租户会众管理而设计。本文档提供所有数据模型、关系和业务规则的全面文档。

## 实体关系图

```
会众（根实体 - 多租户隔离）
    │
    ├─── 地区（1:M）
    │      │
    │      └─── 地图（1:M）
    │             │
    │             ├─── 地址（1:M）
    │             │      ├─── addresses_log（1:M）
    │             │      └─── address_options（1:M）
    │             ├─── 分配（1:M）
    │             └─── 消息（1:M）
    │
    ├─── 选项（1:M）
    │      └─── address_options（M:M 桥接表 with 地址）
    │
    ├─── 用户（1:M 通过角色）
    │      │
    │      ├─── 角色（1:M）
    │      └─── 分配（1:M）
    │
    ├─── 消息（1:M）
    │
    └─── aggregates（1:M 来自地区和地图）
```

## 核心集合

### 1. 会众

**用途：** 多租户隔离和组织设置的中心实体

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `code` | text | 是 | 组织代码 |
| `name` | text | 是 | 组织名称 |
| `expiry_hours` | number | 是 | 分配链接有效期（小时） |
| `max_tries` | number | 是 | 标记为完成前的最大"无人在家"尝试次数 |
| `origin` | select | 是 | 国家/地区（sg、my） |
| `timezone` | select | 是 | 日期计算的时区 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**访问规则：**
- **列表：** 用户必须在会众中有角色
- **查看：** 有角色的用户或基于链接的访问
- **更新：** 仅管理员
- **删除：** 仅管理员

**业务规则：**
- 控制所有地图的默认分配时长
- `max_tries` 决定"无人在家"地址何时算作完成
- 时区影响基于日期的计算和报告

**示例：**
```json
{
  "id": "abc123",
  "code": "CONG001",
  "name": "北区会众",
  "expiry_hours": 24,
  "max_tries": 3,
  "origin": "sg",
  "timezone": "Asia/Singapore"
}
```

---

### 2. 地区

**用途：** 会众内的地理划分，用于组织外勤服务

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `code` | text | 是 | 地区标识符（自动："0"） |
| `description` | text | 否 | 人类可读描述 |
| `progress` | number | 自动 | 完成百分比（0-100） |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**索引：**
- `(congregation, code)` - 按会众快速查找
- `(congregation)` - 会众筛选

**关系：**
- 与地图的 1:M 关系
- 与地址的 1:M 关系（为性能去规范化）

**访问规则：**
- **列表：** 会众中有角色的用户
- **查看：** 有角色的用户或链接访问
- **创建/更新/删除：** 组长或管理员

**进度计算：**
根据地区内所有地图的聚合自动计算

**示例：**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "市区",
  "progress": 65.5
}
```

---

### 3. 地图

**用途：** 表示包含地址的具体位置或建筑

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `territory` | relation | 是 | 到地区的 FK（级联删除） |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `sequence` | number | 自动 | 地区内的顺序（自动递增） |
| `code` | text | 是 | 地图标识符/编号（自动："0"） |
| `description` | text | 否 | 位置描述 |
| `type` | select | 是 | `single` 或 `multi`（楼层类型） |
| `coordinates` | JSON | 否 | 地理位置 `{lat, lng}` |
| `aggregates` | JSON | 自动 | 缓存统计数据（最大 2MB） |
| `progress` | number | 自动 | 完成百分比 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**索引：**
- `(territory, code)` - 主要查找
- `(territory, sequence)` - 排序
- `(territory)` - 地区筛选

**级联删除：**
- 地址
- 分配
- 消息

**聚合结构：**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**地图类型：**
- **single**：私人地址（住宅）
- **multi**：公共地址（多层建筑）

**访问规则：**
- **列表：** 有角色的用户或链接访问
- **查看：** 有角色的用户或链接访问
- **创建/更新：** 组长或管理员
- **删除：** 仅管理员

**示例：**
```json
{
  "id": "map_xyz",
  "territory": "terr_abc",
  "congregation": "cong_123",
  "sequence": 5,
  "code": "12",
  "description": "大街公寓",
  "type": "multi",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "aggregates": {
    "done": 20,
    "not_done": 10,
    "dnc": 2,
    "invalid": 1,
    "not_home_max_tries": 3
  },
  "progress": 66.7
}
```

---

### 4. 地址

**用途：** 地图内的单个单元或住户

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `map` | relation | 是 | 到地图的 FK（级联删除） |
| `congregation` | relation | 是 | 到会众的 FK（去规范化） |
| `territory` | relation | 是 | 到地区的 FK（去规范化） |
| `floor` | number | 是 | 楼层号（基于 0） |
| `code` | text | 是 | 单元/门牌号（如"101"、"A"） |
| `sequence` | number | 是 | 地图内的顺序 |
| `status` | select | 是 | 地址拜访状态 |
| `not_home_tries` | number | 自动 | "无人在家"尝试次数计数器 |
| `type` | relation | 否 | 与选项的 M:M（JSON 数组） |
| `notes` | text | 否 | 外勤工作者备注 |
| `last_notes_updated` | date | 自动 | 备注最后更改时间（通过钩子） |
| `last_notes_updated_by` | text | 自动 | 最后更新者用户名（通过钩子） |
| `dnc_time` | date | 否 | 标记为"请勿打扰"的时间 |
| `coordinates` | JSON | 否 | 精确 GPS `{lat, lng}` |
| `updated_by` | text | 否 | 跟踪谁进行了更新 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**状态值：**
- `not_done` - 尚未拜访（初始状态）
- `done` - 成功完成
- `not_home` - 无人在家（可重试）
- `do_not_call` - 排除在外勤工作之外
- `invalid` - 地址不存在

**索引（共 7 个）：**
- `(map)` - 地图筛选
- `(map, status)` - 状态分布查询
- `(map, code)` - 代码查找
- `(map, floor)` - 楼层组织
- `(type, congregation)` - 选项分布
- `(congregation, last_notes_updated_by)` - 用户最近备注
- `(territory, status)` - 整个地区的状态

**自动钩子：**
- 更新时触发 `last_notes_updated` 和 `last_notes_updated_by` 字段
- 触发地图的聚合重新计算
- 更新地区进度

**访问规则：**
- **列表：** 有角色的用户或链接访问
- **查看：** 有角色的用户或链接访问
- **创建：** 组长或管理员
- **更新：** 有角色的用户或链接访问（传道员可以更新）
- **删除：** 仅管理员

**示例：**
```json
{
  "id": "addr_123",
  "map": "map_xyz",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "floor": 2,
  "code": "201",
  "sequence": 5,
  "status": "not_home",
  "not_home_tries": 2,
  "type": ["opt_residential"],
  "notes": "下午6点后尝试",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. 用户

**用途：** 带身份验证的系统用户账户

**类型：** 认证集合（PocketBase user-auth-type）

**系统字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `email` | email | 是 | 登录标识（唯一） |
| `emailVisibility` | bool | 是 | 邮件是否公开 |
| `verified` | bool | 自动 | 邮件验证状态 |
| `password` | password | 是 | Bcrypt 哈希（成本：10，最少 6 个字符） |
| `tokenKey` | text | 隐藏 | 令牌生成密钥（30-60 个字符） |

**自定义字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `name` | text | 是 | 显示名称（2-50 个字符） |
| `disabled` | bool | 否 | 账户禁用标志 |
| `last_login` | date | 自动 | 最后身份验证时间戳（通过钩子） |

**身份验证配置：**
- **密码验证：** 已启用（最少 6 个字符）
- **OAuth2：** Google PKCE 流
- **MFA：** 已启用（持续时间：1800s）
- **OTP：** 已启用（持续时间：180s，长度：4 位数字）
- **邮件验证：** 持续时间 604800s（7 天）
- **密码重置：** 持续时间 1800s（30 分钟）

**访问规则：**
- **列表：** 仅管理员
- **查看：** 管理员或自己
- **创建：** 公开（OAuth 或明确注册）
- **更新：** 仅自己
- **删除：** 仅管理员

**示例：**
```json
{
  "id": "user_abc",
  "email": "john.doe@example.com",
  "name": "John Doe",
  "verified": true,
  "disabled": false,
  "last_login": "2025-12-19T14:30:00Z"
}
```

---

### 6. 角色

**用途：** 将用户与会众关联的权限分配

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `user` | relation | 是 | 到用户的 FK（级联删除） |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `role` | select | 是 | 权限级别 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**角色值：**
- `read_only` - 仅查看访问
- `conductor` - 可以管理分配和地图
- `administrator` - 完全访问

**业务规则：**
- 每个用户在每个会众中只有一个角色
- 角色决定所有操作的权限

**示例：**
```json
{
  "id": "role_xyz",
  "user": "user_abc",
  "congregation": "cong_123",
  "role": "conductor"
}
```

---

### 7. 分配

**用途：** 传道员用于限时地图访问的访问令牌

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(20) | 是 | **用作匿名访问令牌** |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `map` | relation | 是 | 到地图的 FK（级联删除） |
| `user` | relation | 否 | 传道员用户（可选） |
| `type` | select | 是 | 分配分类 |
| `expiry_date` | date | 是 | 自动过期时间戳 |
| `publisher` | text | 否 | 传道员名称/标识符 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**分配类型：**
- `normal` - 常规地区工作
- `personal` - 个人传道地区

**工作流程：**
1. 组长通过 `/territory/link` 端点创建
2. 传道员接收 20 个字符的 `link_id`（此分配的 ID）
3. 传道员通过 HTTP 请求头 `@request.headers.link_id` 传递 `link_id`
4. PocketBase 验证：link_id 与 assignment.id 匹配，且 expiry_date > 当前时间
5. 后台任务每 5 分钟删除过期分配

**示例：**
```json
{
  "id": "asgn_1234567890abcdef",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2025-12-20T14:30:00Z",
  "publisher": "张三"
}
```

---

### 8. 选项

**用途：** 地址类型分类（每个会众可自定义）

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `code` | text | 是 | 选项标识符（如"RES"、"BUS"） |
| `description` | text | 是 | 人类可读标签 |
| `is_countable` | bool | 是 | 是否包含在进度计算中 |
| `is_default` | bool | 是 | 新地址的默认选项 |
| `sequence` | number | 是 | 显示/排序顺序 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**对进度的影响：**
- 可计数选项（`is_countable=true`）包含在进度 % 计算中
- 不可计数选项单独跟踪但排除在进度之外
- 删除选项时，地址会自动获得默认选项

**示例：**
```json
{
  "id": "opt_res",
  "congregation": "cong_123",
  "code": "RES",
  "description": "住宅",
  "is_countable": true,
  "is_default": true,
  "sequence": 1
}
```

---

### 9. 消息

**用途：** 用户之间的通信和通知

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `map` | relation | 是 | 到地图的 FK（级联删除） |
| `congregation` | relation | 是 | 到会众的 FK（级联删除） |
| `message` | text | 是 | 消息内容 |
| `created_by` | text | 是 | 创建者用户名 |
| `read` | bool | 是 | 是否已被管理员阅读 |
| `pinned` | bool | 是 | 管理员置顶指令标志 |
| `type` | select | 是 | 预期接收者类型 |
| `created` | date | 自动 | 创建时间戳 |
| `updated` | date | 自动 | 最后更新时间戳 |

**消息类型：**
- `publisher` - 从传道员到管理员
- `conductor` - 来自组长
- `administrator` - 管理员指令（可置顶）

**后台处理：**
- 每 30 分钟：通过邮件向管理员发送未读消息
- 每 30 分钟：通过邮件向传道员发送置顶管理员消息
- 每 1 小时：向管理员发送备注更新

**示例：**
```json
{
  "id": "msg_abc",
  "map": "map_xyz",
  "congregation": "cong_123",
  "message": "请重新拜访 205 单元",
  "created_by": "john.doe",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

### 10. 地址日志（addresses_log）

**用途：** 地址状态变更的审计追踪——每次地址状态更新时，此处写入一条记录

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `address` | relation | 是 | 到地址的 FK（级联删除） |
| `old_status` | text | 是 | 变更前的地址状态值 |
| `new_status` | text | 是 | 变更后的地址状态值 |
| `changed_by` | relation | 否 | 到用户的 FK — 进行变更的用户 |
| `created` | date | 自动 | 变更时间戳 |

**关系：**
- 与地址的 M:1 关系（每条日志属于一个地址）
- 与用户的 M:1 关系（通过 `changed_by`）

**访问规则：**
- **列表/查看：** 组长或管理员
- **创建：** 系统钩子（地址状态变更时自动写入）
- **更新/删除：** 仅管理员

---

### 11. 聚合快照（aggregates）

**用途：** 地区和地图的缓存进度快照，由后台任务每 10 分钟重新计算

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `notDone` | number | 是 | 尚未拜访的地址数量 |
| `notHome` | number | 是 | "无人在家"的地址数量 |
| `progress` | number | 是 | 完成百分比（0–100） |
| `territory` | relation | 否 | 到地区的 FK — 地区级聚合时设置 |
| `map` | relation | 否 | 到地图的 FK — 地图级聚合时设置 |

**关系：**
- 与地区的 M:1 关系（地区级快照）
- 与地图的 M:1 关系（地图级快照）

**业务规则：**
- 每条记录中 `territory` 或 `map` 只有一个被设置
- 由后台任务每 10 分钟重新计算
- 值为只读快照；实时进度按需计算

**访问规则：**
- **列表/查看：** 有角色的用户或链接访问
- **创建/更新：** 仅系统后台任务
- **删除：** 仅管理员

---

### 12. 地址选项关联表（address_options）

**用途：** 跟踪哪些地址级选项（来自 `options` 集合）已应用于单个地址——地址与选项多对多关系的关联表

**类型：** 基础集合

**字段：**

| 字段 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `id` | text(15) | 是 | 自动生成的唯一 ID |
| `address` | relation | 是 | 到地址的 FK（级联删除） |
| `option` | relation | 是 | 到选项的 FK（级联删除） |
| `congregation` | relation | 是 | 到会众的 FK（去规范化以加速查询） |

**索引：**
- `(address, option)` — 唯一配对（每个地址每个选项只能有一条记录）
- `(congregation)` — 会众范围内的选项使用情况

**关系：**
- 与地址的 M:1 关系
- 与选项的 M:1 关系
- 与会众的 M:1 关系

**访问规则：**
- **列表/查看：** 有角色的用户或链接访问
- **创建/删除：** 组长或管理员

---

## 分析集合

分析集合存储用于报告和洞察的历史数据。这些集合由系统内部管理，具体字段可能随版本演进；此处按用途说明，不列出详细字段表。

### analytics_territories

**用途：** 定期存储地区级指标快照（如完成百分比、地址数量）。用于生成历史趋势图表和地区完成报告。

### analytics_maps

**用途：** 定期存储地图级指标快照。支持对单个地图完成率和活动模式进行精细报告。

### analytics_daily_status

**用途：** 汇总整个会众每日地址状态数量（已完成、未完成、无人在家、请勿打扰、无效）。驱动逐日和逐周趋势仪表板。

### analytics_not_home

**用途：** 跟踪多次被标记为"无人在家"的地址，帮助组长找出需要跟进的目标，识别持续无法联系的地址。

### analytics_user_audit

**用途：** 记录用户操作（包括登录、地址更新、分配操作），用于问责和审计。管理员可通过此集合查看活动历史并调查异常。

> **注意：** 分析集合由后台任务填充，由报告层读取。应避免应用程序代码直接写入。

---

## 数据库技术

**引擎：** 通过 PocketBase 使用 SQLite（modernc.org/sqlite v1.40.2+）

**特性：**
- 纯 Go 实现（无 CGo）- 支持跨平台编译
- 基于使用 ccgo/v4 将 C SQLite 转译为 Go
- 定期安全更新的积极维护（对齐 SQLite 3.50+）

**使用的 SQL 特性：**
- JSON 字段用于灵活数据（坐标、聚合）
- 复合索引用于查询优化
- 事务用于 ACID 合规性
- CTE（公共表表达式）用于复杂聚合

**优势：**
- 自包含的基于文件的数据库
- 无需单独的数据库服务
- 非常适合自托管部署
- 无 CGO 依赖支持静态二进制构建
- 经过安全补丁验证的稳定性

---

## 关键关系

| 关系 | 基数 | 级联行为 |
|------|------|------|
| 会众 → 地区 | 1:M | 删除级联 |
| 地区 → 地图 | 1:M | 删除级联 |
| 地图 → 地址 | 1:M | 删除级联 |
| 地图 → 分配 | 1:M | 删除级联 |
| 地图 → 消息 | 1:M | 删除级联 |
| 地址 → 选项 | M:M | JSON 数组 |
| 用户 → 角色 | M:M | 通过角色表 |
| 用户 → 分配 | 1:M | 保留分配 |
| 会众 → 选项 | 1:M | 删除级联 |
| 会众 → 角色 | M:M | 通过角色表 |
| 地址 → addresses_log | 1:M | 删除级联 |
| 地区 → aggregates | 1:M | 由后台任务重新计算 |
| 地图 → aggregates | 1:M | 由后台任务重新计算 |
| 地址 → address_options | 1:M | 删除级联 |
| 选项 → address_options | 1:M | 删除级联 |

---

## 业务规则

### 进度计算

**公式：**
```
已完成 = (已完成 + 无人在家已达最大尝试次数) / 总可计数数 * 100
```

**可计数地址：**
- 只有带 `is_countable=true` 选项的地址
- 不包括请勿打扰和无效地址

**完成标准：**
- 状态 = `done`，或
- 状态 = `not_home` 且 `not_home_tries >= congregation.max_tries`

### 地址生命周期

```
未完成 → 无人在家（重试尝试）→ 完成
        → 请勿打扰（排除）
        → 无效（排除）
```

### 自动更新触发器

1. **地址更新：**
   - 如果备注更改，更新 `last_notes_updated`
   - 设置 `last_notes_updated_by` 为当前用户
   - 触发聚合重新计算
   - 更新地图进度
   - 更新地区进度

2. **地图操作：**
   - 地址更改时重新计算聚合
   - 更新地区进度

3. **分配创建：**
   - 根据会众设置设定过期时间
   - 生成唯一的 20 字符 ID 作为访问令牌

---

## 迁移与架构更新

**架构文件：** `migrations/1764846700_collections_snapshot.go`

**大小：** 55,459 行（由 PocketBase 生成）

**迁移流程：**
1. PocketBase 在启动时自动应用迁移
2. 迁移在 `_migrations` 系统集合中跟踪
3. 架构更改需要新的迁移文件

---

## 下一步

- [架构](architecture.md) - 系统架构
- [后端设置](backend-setup.md) - 配置数据库
