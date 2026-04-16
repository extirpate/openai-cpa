# API 接口

> 细节层辅助文档 - API端点索引与认证机制

<!-- 创建于 init-project，后续由 /zn-update-docs 维护 -->

---

## 约定

- **风格:** RESTful API
- **格式:** JSON (Content-Type: application/json)
- **认证:** Bearer Token (除 `/login` 外所有端点需认证)
- **错误码:** 标准HTTP状态码 (200/400/401/500)

---

## 发现方式

查看 `routers/api_routes.py` 获取最新接口列表。所有端点使用 `@router` 装饰器注册。

---

## 关键索引

### 认证
| 端点 | 方法 | 说明 |
|------|------|------|
| `/login` | POST | 密码登录，返回Bearer token |

**请求示例:**
```json
{
  "password": "admin"
}
```

**响应示例:**
```json
{
  "token": "abc123...",
  "expires_in": 86400
}
```

---

### 任务控制
| 端点 | 方法 | 说明 |
|------|------|------|
| `/start` | POST | 启动注册任务（自动识别模式） |
| `/stop` | POST | 停止运行中的任务 |
| `/status` | GET | 获取任务状态和统计数据 |

**任务模式自动识别:**
- `enable_cpa_mode=true` → CPA仓库维护模式
- `enable_sub2api_mode=true` → Sub2API仓库维护模式
- 否则 → 普通注册模式

---

### 库存管理
| 端点 | 方法 | 说明 |
|------|------|------|
| `/accounts` | GET | 分页查询本地SQLite账号库存 |
| `/accounts/export` | POST | 批量导出账号（JSON/TXT格式） |
| `/accounts/delete` | POST | 批量删除账号 |

**分页参数:**
- `page` - 页码（从1开始）
- `page_size` - 每页数量（默认50）

---

### 集群通信
| 端点 | 方法 | 说明 |
|------|------|------|
| `/ws/cluster` | WebSocket | 集群节点心跳与日志上报 |
| `/cluster/control` | POST | 主控节点向工作节点发送命令 |
| `/cluster/upload_accounts` | POST | 工作节点上传账号到主控 |

**WebSocket消息格式:**
```json
{
  "type": "heartbeat",
  "node_name": "worker-1",
  "stats": {
    "success": 10,
    "failed": 2
  }
}
```

---

### 实时日志
| 端点 | 方法 | 说明 |
|------|------|------|
| `/logs/stream` | GET | SSE流式推送后端日志 |

**SSE事件格式:**
```
data: {"timestamp": "12:34:56", "level": "INFO", "message": "任务启动"}
```

---

### 配置管理
| 端点 | 方法 | 说明 |
|------|------|------|
| `/config` | GET | 获取当前配置 |
| `/config` | POST | 更新配置（需重启生效） |

---

## 认证机制

### Bearer Token
- 登录成功后返回token
- 后续请求在Header中携带：`Authorization: Bearer {token}`
- Token有效期：24小时（可配置）

### 集群节点认证
- 工作节点通过 `cluster_secret` 验证身份
- 主控节点检查 `secret` 参数是否匹配配置文件

---

## 错误处理

### 标准错误响应
```json
{
  "error": "错误描述",
  "code": "ERROR_CODE"
}
```

### 常见错误码
- `401 Unauthorized` - Token无效或过期
- `403 Forbidden` - 集群secret不匹配
- `400 Bad Request` - 参数校验失败
- `500 Internal Server Error` - 服务器内部错误

---

*API文档 | 生成时间: 2026-04-16*
