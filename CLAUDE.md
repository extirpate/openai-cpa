# Wenfxl Codex Manager

> 结构层主文档 - 项目全局架构与模块索引

---

## 项目概述

**名称:** Wenfxl Codex Manager Web Console  
**类型:** 分布式自动化平台（Web应用）  
**技术栈:** Python 3.11+ / FastAPI / Vue.js / SQLite / Docker  
**核心功能:** 高并发账号注册、全生命周期库存管理、分布式集群控制

---

## 模块划分

| 模块 | 职责 | 文档 |
|------|------|------|
| routers | API路由层，处理所有HTTP/WebSocket端点 | → `routers/CLAUDE.md` |
| utils | 核心引擎、配置管理、数据库、代理管理 | → `utils/CLAUDE.md` |
| utils/email_providers | 多后端邮箱OTP检索实现（15+ providers） | → `utils/email_providers/CLAUDE.md` |
| utils/integrations | 第三方服务集成（Sub2API, HeroSMS, TG Bot, AI） | → `utils/integrations/CLAUDE.md` |
| luckmail | LuckMail高级服务集成 | → `luckmail/CLAUDE.md` |
| static | Web Console前端资源（Vue.js, CSS, JS） | 前端代码自释 |
| plugin | 分布式浏览器扩展（Classic模式） | 扩展代码自释 |

---

## 辅助文档索引

| 文档 | 用途 | 路径 |
|------|------|------|
| 系统架构 | 分层架构、技术选型、设计决策 | → `ARCHITECTURE.md` |
| API文档 | API端点索引、认证机制 | → `API.md` |
| 部署指南 | Docker部署、环境配置 | → `DEPLOYMENT.md` |
| 集成文档 | 第三方服务集成说明 | → `INTEGRATION.md` |

---

## 快速开始

```bash
# 安装依赖
pip install -r requirements.txt

# 启动Web Console
python wfxl_openai_regst.py

# Docker部署（推荐）
docker compose up -d
```

**访问地址:** http://127.0.0.1:8000  
**默认密码:** admin

---

## 核心工作流

1. **Web Console登录** → 配置邮箱/代理/仓库
2. **启动任务** → 自动识别模式（normal/CPA/Sub2API）
3. **实时监控** → SSE日志流、统计面板
4. **库存管理** → 本地SQLite + 云端仓库同步

---

## 规则

> 全局技术规则和约束

### 禁止
- 禁止在日志中输出完整邮箱域名（使用 `mask_domain` 保护敏感配置）
- 禁止提交 `data/config.yaml`、`data/token.json`、`data/credentials.json` 到代码库
- 禁止在生产环境使用默认密码 `admin`

### 强制
- 所有配置变更必须通过 `config.example.yaml` 模板同步
- 新增邮箱provider必须实现 `get_otp()` 和 `generate_email()` 接口
- 集群节点通信必须验证 `cluster_secret`

### Know-how
- 配置文件自动补全：`deep_update_config` 检测缺失参数并合并默认值
- Docker环境自动适配：`format_docker_url` 重写 `127.0.0.1` → `host.docker.internal`
- 代理池模式：`pool_mode=true` 时使用 `warp_proxy_list` 多端口轮询

---

*文档遵循五层文档体系（结构层、细节层、自释层、规约层、底蕴层） | 生成时间: 2026-04-16*
