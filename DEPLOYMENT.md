# 部署指南

> 细节层辅助文档 - Docker部署与环境配置

<!-- 创建于 init-project，后续由 /zn-update-docs 维护 -->

---

## 约定

- **推荐部署方式:** Docker Compose（开箱即用）
- **原生部署要求:** Python 3.11+ (macOS/Linux) 或 Python 3.12+ (Windows)
- **配置文件位置:** `data/config.yaml`（首次启动自动生成）

---

## 发现方式

查看项目根目录的 `docker-compose.yml` 和 `Dockerfile` 获取最新部署配置。

---

## 关键索引

### Docker Compose 部署（推荐）

**步骤:**
1. 确保已安装 Docker 和 Docker Compose
2. 在项目根目录执行：
   ```bash
   docker compose up -d
   ```
3. 访问 Web Console：http://127.0.0.1:8000
4. 默认密码：`admin`

**配置持久化:**
- `./data:/app/data` - 配置文件、SQLite数据库、导出文件

**端口映射:**
- `8000:8000` - Web Console端口（可在 `docker-compose.yml` 中修改）

**自动更新:**
- 项目包含 `watchtower` 服务，每24小时自动拉取最新镜像并重启

---

### 原生部署

**步骤:**
1. 安装依赖：
   ```bash
   pip install -r requirements.txt
   ```
2. 启动服务：
   ```bash
   python wfxl_openai_regst.py
   ```
3. 访问 Web Console：http://127.0.0.1:8000

**注意事项:**
- macOS/Linux 必须使用 Python 3.11
- Windows 必须使用 Python 3.12.6 或 Python 3.12
- 确保 `data/` 目录有写权限

---

### 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `PORT` | Web Console监听端口 | 8000 |
| `CONFIG_PATH` | 配置文件路径 | data/config.yaml |

---

### 配置文件

首次启动时，系统自动从 `config.example.yaml` 生成 `data/config.yaml`。

**关键配置项:**
- `default_proxy` - 默认代理地址
- `email_service` - 邮箱后端类型
- `enable_cpa_mode` - 是否启用CPA模式
- `enable_sub2api_mode` - 是否启用Sub2API模式
- `cluster_master_url` - 集群主控地址（工作节点配置）

**配置热更新:**
- 修改配置文件后，在Web Console中点击"重载配置"按钮
- 或重启服务生效

---

### 集群部署

**主控节点:**
1. 不配置 `cluster_master_url`（留空）
2. 启动服务后自动进入主控模式

**工作节点:**
1. 配置 `cluster_master_url` 指向主控地址（如 `http://192.168.1.100:8000`）
2. 配置 `cluster_node_name` 设置节点名称
3. 配置 `cluster_secret` 与主控保持一致
4. 启动服务后自动连接到主控

---

### 代理配置

**Clash/Mihomo 节点切换:**
1. 启动 Mihomo 服务（参考 README.md 中的 Mihomo 部署章节）
2. 配置 `clash_proxy_pool.api_url` 指向 Clash API（如 `http://127.0.0.1:9097`）
3. 配置 `clash_proxy_pool.secret` 与 Clash 配置一致
4. 配置 `clash_proxy_pool.test_proxy_url` 指向代理端口（如 `http://127.0.0.1:7897`）

**代理池模式:**
1. 启动多个 Mihomo 容器（参考 README.md 中的代理池脚本）
2. 配置 `clash_proxy_pool.pool_mode: true`
3. 配置 `warp_proxy_list` 列出所有代理端口

---

### 故障排查

**Web Console 无法访问:**
- 检查端口是否被占用：`lsof -i :8000`
- 检查防火墙是否放行端口
- Docker 部署检查容器状态：`docker compose ps`

**配置文件未生成:**
- 检查 `data/` 目录权限
- 检查 `config.example.yaml` 是否存在

**集群节点无法连接:**
- 检查 `cluster_master_url` 是否正确
- 检查 `cluster_secret` 是否一致
- 检查主控节点防火墙是否放行端口

---

*部署文档 | 生成时间: 2026-04-16*
