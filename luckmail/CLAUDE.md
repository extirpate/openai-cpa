# luckmail 模块

> 结构层模块文档 - LuckMail高级服务集成

---

## 模块职责

LuckMail商业邮箱服务的高级功能封装：
- API邮箱购买（自动标签、批量购买）
- 历史复用模式（降低成本）
- 邮箱生命周期管理
- Web Console批量购买面板

---

## 核心功能

### API购买
- 调用LuckMail API购买邮箱
- 支持自动标签（`auto_tag`）
- 返回邮箱地址和密码

### 历史复用模式
- 从历史购买记录中复用邮箱
- 检查邮箱可用性
- 降低购买成本

### 批量购买
- Web Console提供批量购买面板
- 支持自定义数量和标签
- 实时显示购买进度

---

## 配置

```yaml
luckmail:
  api_url: "https://api.luckmail.com"
  api_token: "your_token"
  history_reuse_mode: true
  auto_tag: true
```

---

## 规则

### 禁止
- 禁止在日志中输出完整的 `api_token`
- 禁止绕过余额检查直接购买

### 强制
- 购买前必须检查余额
- 购买失败时必须记录详细错误信息

### Know-how
- 历史复用模式优先使用历史邮箱
- 批量购买使用异步并发（提高速度）
- 购买成功后自动添加到本地库存

---

*模块文档 | 生成时间: 2026-04-16*
