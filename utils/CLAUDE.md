# utils 模块

> 结构层模块文档 - 核心引擎与工具集

---

## 模块职责

提供核心业务逻辑和基础设施：
- 配置管理（YAML加载、自动补全、Docker适配）
- 数据库管理（SQLite账号库存）
- 代理管理（Clash/Mihomo节点切换、代理池）
- 注册引擎（账号生成、OTP检索、仓库上传）

---

## 子模块

| 子模块 | 职责 | 文档 |
|--------|------|------|
| email_providers/ | 多后端邮箱OTP检索实现 | → `email_providers/CLAUDE.md` |
| integrations/ | 第三方服务集成 | → `integrations/CLAUDE.md` |

---

## 核心文件

| 文件 | 职责 | 关键函数/类 |
|------|------|------------|
| config.py | 配置加载与管理 | `init_config()`, `deep_update_config()`, `format_docker_url()` |
| db_manager.py | SQLite数据库操作 | `init_db()`, `insert_account()`, `query_accounts()` |
| proxy_manager.py | 代理池管理 | `switch_clash_node()`, `get_proxy_from_pool()` |
| register.py | 注册引擎核心逻辑 | `run_registration_loop()`, `handle_otp_verification()` |

---

## 配置管理（config.py）

### 自动补全机制
- 读取 `data/config.yaml` 和 `config.example.yaml`
- 调用 `deep_update_config()` 递归合并缺失参数
- 自动写回用户配置文件

### Docker环境适配
- `format_docker_url()` 检测 `/.dockerenv` 文件
- 自动重写 `127.0.0.1` → `host.docker.internal`

---

## 代理管理（proxy_manager.py）

### Clash节点切换
- 通过 `clash_proxy_pool.api_url` 调用Clash API
- 支持黑名单过滤（`blacklist_regions`）
- 支持最快节点模式（`fastest_mode`）

### 代理池模式
- `pool_mode=true` 时从 `warp_proxy_list` 轮询
- 每个worker使用独立代理端口

---

## 规则

### 禁止
- 禁止在日志中输出完整邮箱域名（使用 `mask_domain`）
- 禁止硬编码配置路径（使用 `BASE_DIR` 动态拼接）

### 强制
- 配置文件操作必须使用 `CONFIG_FILE_LOCK` 加锁
- 新增配置项必须同步到 `config.example.yaml`

### Know-how
- 配置加载失败时自动从模板生成
- 代理切换失败时回退到 `default_proxy`
- 数据库连接使用连接池（`check_same_thread=False`）

---

*模块文档 | 生成时间: 2026-04-16*
