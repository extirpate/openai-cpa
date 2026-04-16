# routers 模块

> 结构层模块文档 - API路由层

---

## 模块职责

处理所有HTTP/WebSocket端点，包括：
- Web Console认证与会话管理
- 任务控制（启动/停止/状态查询）
- 账号库存CRUD操作
- 集群节点通信协议
- 实时日志流（SSE）
- 配置管理API

---

## 核心文件

| 文件 | 职责 | 关键端点 |
|------|------|---------|
| api_routes.py | 主路由模块 | `/login`, `/start`, `/stop`, `/accounts`, `/cluster/*` |

---

## 关键接口

### 认证
- `POST /login` - 密码登录，返回Bearer token
- 依赖函数：`verify_token()` - 验证请求头中的token

### 任务控制
- `POST /start` - 启动注册任务（自动识别normal/CPA/Sub2API模式）
- `POST /stop` - 停止运行中的任务
- `GET /status` - 获取任务状态和统计数据

### 库存管理
- `GET /accounts` - 分页查询本地SQLite账号库存
- `POST /accounts/export` - 批量导出账号（JSON/TXT格式）
- `POST /accounts/delete` - 批量删除账号

### 集群通信
- `WebSocket /ws/cluster` - 集群节点心跳与日志上报
- `POST /cluster/control` - 主控节点向工作节点发送命令

### 实时日志
- `GET /logs/stream` - SSE流式推送后端日志

---

## 规则

### 禁止
- 禁止在响应中返回完整的 `cluster_secret`
- 禁止绕过 `verify_token` 依赖访问受保护端点

### 强制
- 所有写操作（启动任务、删除账号）必须验证token
- WebSocket连接必须验证 `cluster_secret` 后才能接收命令

### Know-how
- 使用 `StreamingResponse` + `yield` 实现SSE日志流
- 集群节点通过 `CLUSTER_NODES` 全局字典维护在线状态
- 任务状态通过 `global_state.engine` 单例访问

---

*模块文档 | 生成时间: 2026-04-16*
