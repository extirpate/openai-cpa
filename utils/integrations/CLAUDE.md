# integrations 子模块

> 结构层模块文档 - 第三方服务集成

---

## 模块职责

集成外部服务API：
- Sub2API仓库管理（账号上传、健康检查、配额刷新）
- HeroSMS短信验证（余额查询、国家选择、号码获取）
- Telegram通知（任务完成、库存告警、异常报告）
- AI服务（Codex名称生成、子域名生成）

---

## 核心文件

| 文件 | 职责 | 关键类/函数 |
|------|------|------------|
| sub2api_client.py | Sub2API仓库客户端 | `Sub2APIClient` 类 |
| hero_sms.py | HeroSMS短信服务 | `get_balance()`, `get_number()` |
| tg_notifier.py | Telegram通知 | `send_tg_msg_async()` |
| ai_service.py | AI服务调用 | `generate_codex_names()` |

---

## Sub2API集成

### 核心功能
- 账号上传：`upload_accounts()`
- 健康检查：`check_account_health()`
- 配额刷新：`refresh_quota()`
- 批量操作：`batch_disable()`, `batch_delete()`

### 配额阈值逻辑
- 读取 `weekly_quota_threshold`（百分比）
- 计算剩余配额：`remaining / total * 100`
- 低于阈值时触发补货

---

## HeroSMS集成

### 核心功能
- 余额查询：`get_balance()`
- 全球价格表：`get_pricing(service="openai")`
- 获取号码：`get_number(country, service)`
- 获取验证码：`get_sms(activation_id)`

### 国家选择策略
- 黑名单过滤（`sms_blacklist_countries`）
- 价格排序（从低到高）
- 库存检查（`count > 0`）

---

## Telegram通知

### 通知类型
- 任务完成：成功/失败统计
- 库存告警：低于阈值时触发
- 异常报告：代理失败、OTP超时

### 配置
- `tg_bot_token` - Bot Token
- `tg_chat_id` - 接收Chat ID
- `enable_tg_notify` - 开关

---

## AI服务

### Codex名称生成
- 调用 `gpt-5.1-codex` 模型
- 生成欧美风格名称（`firstname.lastname`）
- 批量生成（`batch_size`）

### 子域名生成
- 生成科技/AI关键词
- 示例：`vector-database`, `neural-network`
- 注入到多级子域名池

---

## 规则

### 禁止
- 禁止在日志中输出完整的 `api_token` 或 `bot_token`
- 禁止硬编码API端点（从配置文件读取）

### 强制
- Sub2API操作失败时必须记录详细错误信息
- HeroSMS余额不足时必须停止任务并通知

### Know-how
- Sub2API使用Bearer认证（`Authorization: Bearer {token}`）
- HeroSMS使用API Key认证（`api_key` 参数）
- Telegram通知使用异步发送（`send_tg_msg_async`）

---

*模块文档 | 生成时间: 2026-04-16*
