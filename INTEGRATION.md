# 第三方服务集成

> 细节层辅助文档 - 外部服务集成说明

<!-- 创建于 init-project，后续由 /zn-update-docs 维护 -->

---

## 约定

- 所有第三方服务集成代码位于 `utils/integrations/`
- API密钥和Token存储在 `data/config.yaml` 中
- 集成失败时记录详细错误日志

---

## 发现方式

查看 `utils/integrations/` 目录下的各个服务实现文件。

---

## 关键索引

### Sub2API 仓库集成

**文件:** `utils/integrations/sub2api_client.py`

**功能:**
- 账号上传：`upload_accounts()`
- 健康检查：`check_account_health()`
- 配额刷新：`refresh_quota()`
- 批量禁用/删除：`batch_disable()`, `batch_delete()`

**配置:**
```yaml
sub2api_mode:
  api_url: "https://api.sub2api.com"
  api_token: "your_token"
  weekly_quota_threshold: 20  # 剩余配额低于20%时触发补货
```

**认证方式:** Bearer Token (`Authorization: Bearer {token}`)

---

### HeroSMS 短信验证集成

**文件:** `utils/integrations/hero_sms.py`

**功能:**
- 余额查询：`get_balance()`
- 全球价格表：`get_pricing(service="openai")`
- 获取号码：`get_number(country, service)`
- 获取验证码：`get_sms(activation_id)`

**配置:**
```yaml
hero_sms:
  api_key: "your_api_key"
  service: "openai"
  blacklist_countries: ["CN", "HK"]  # 黑名单国家
```

**认证方式:** API Key (`api_key` 参数)

---

### Telegram 通知集成

**文件:** `utils/integrations/tg_notifier.py`

**功能:**
- 任务完成通知
- 库存告警通知
- 异常报告通知

**配置:**
```yaml
telegram:
  bot_token: "your_bot_token"
  chat_id: "your_chat_id"
  enable_notify: true
```

**消息格式:**
```
🎉 任务完成
成功: 10
失败: 2
耗时: 5分钟
```

---

### AI 服务集成

**文件:** `utils/integrations/ai_service.py`

**功能:**
- Codex名称生成：`generate_codex_names()`
- 子域名生成：`generate_subdomains()`

**配置:**
```yaml
ai_service:
  api_url: "https://api.openai.com/v1/chat/completions"
  api_key: "your_api_key"
  model: "gpt-5.1-codex"
```

**生成示例:**
- 名称：`john.smith`, `emily.johnson`
- 子域名：`vector-database`, `neural-network`

---

### LuckMail 商业邮箱集成

**文件:** `luckmail/` 模块

**功能:**
- API邮箱购买
- 历史复用模式
- 批量购买面板

**配置:**
```yaml
luckmail:
  api_url: "https://api.luckmail.com"
  api_token: "your_token"
  history_reuse_mode: true
  auto_tag: true
```

---

### Cloudflare API 集成

**用途:** 批量同步子域名到 Cloudflare DNS

**配置:**
```yaml
cloudflare:
  api_email: "your_email"
  api_key: "your_api_key"
```

**功能:**
- 批量添加DNS记录
- 子域名池同步

---

## 集成测试

### Sub2API 连接测试
```bash
# 在Web Console中点击"测试连接"按钮
# 或通过API调用：
curl -X POST http://127.0.0.1:8000/test/sub2api \
  -H "Authorization: Bearer {token}"
```

### HeroSMS 余额查询
```bash
# 在Web Console的"SMS验证"面板中查看余额
# 或通过API调用：
curl -X GET http://127.0.0.1:8000/sms/balance \
  -H "Authorization: Bearer {token}"
```

---

## 故障排查

**Sub2API 上传失败:**
- 检查 `api_token` 是否正确
- 检查账号格式是否符合要求
- 检查网络连接是否正常

**HeroSMS 获取号码失败:**
- 检查余额是否充足
- 检查国家是否在黑名单中
- 检查服务类型是否支持

**Telegram 通知未收到:**
- 检查 `bot_token` 和 `chat_id` 是否正确
- 检查Bot是否已添加到对应Chat
- 检查 `enable_notify` 是否为 `true`

---

*集成文档 | 生成时间: 2026-04-16*
