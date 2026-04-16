# email_providers 子模块

> 结构层模块文档 - 多后端邮箱OTP检索

---

## 模块职责

实现15+种邮箱后端的统一接口：
- 邮箱地址生成（支持多级子域名）
- OTP验证码检索（轮询/WebSocket）
- 邮箱生命周期管理（购买/复用/清理）

---

## Provider列表

| Provider | 后端类型 | 特性 |
|----------|---------|------|
| cloudflare_temp_email | Cloudflare Worker | 多级子域名、免费 |
| freemail | 自建后端 | 多级子域名、API控制 |
| cloudmail | 自建后端 | 多级子域名、API控制 |
| imap | IMAP协议 | Gmail/Outlook通用 |
| luckmail | 商业服务 | 历史复用、自动购买 |
| tempmail_org | TempMail.org | 公共服务 |
| tempmail_lol | Tempmail.lol | 公共服务 |
| duckmail | Duckmail | 公共服务 |
| generator | Generator | 公共服务 |
| local_microsoft | 本地Microsoft库 | 资产隔离 |
| gmail_oauth | Gmail OAuth | 官方API |

---

## 统一接口

所有provider必须实现：

```python
def generate_email() -> str:
    """生成邮箱地址"""
    pass

def get_otp(email: str, timeout: int = 120) -> str:
    """检索OTP验证码，超时返回None"""
    pass
```

---

## 多级子域名生成

支持的provider：`cloudflare_temp_email`, `freemail`, `cloudmail`

生成逻辑：
1. 从 `subdomain_pool` 随机选择子域名
2. 拼接格式：`{username}@{subdomain}.{base_domain}`
3. 示例：`user123@ai-vector.example.com`

---

## 规则

### 禁止
- 禁止在日志中输出完整邮箱域名（使用 `mask_domain`）
- 禁止硬编码邮箱域名（从配置文件读取）

### 强制
- 新增provider必须实现 `generate_email()` 和 `get_otp()` 接口
- OTP检索必须支持超时参数

### Know-how
- IMAP provider使用 `imaplib` 库，需处理编码问题
- LuckMail provider支持历史复用模式（`history_reuse_mode`）
- 公共服务provider需处理反爬虫机制（User-Agent轮换）

---

*模块文档 | 生成时间: 2026-04-16*
