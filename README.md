# MCP-SDLC Demo

## 项目简介
本项目旨在通过 VS Code + Copilot Agent + MCP Server，实践和调研 MCP Server 在软件开发生命周期（SDLC）六大阶段的实际作用。目标是让 Agent 能自动维护开发进度、架构、MCP server 使用情况，并为开发者和调研者提供完整的上下文。

本次 Demo 选用“容器化 DevTool 在线面板（DevBox）”作为实践对象：
- 一个基于 Web 的轻量工具平台，用户可一键启动/停止常用开发容器（如 Jupyter、MySQL、Nginx），并查看日志或访问入口。
- 设计目标是最大化展示 MCP Server 在 SDLC 各阶段的能力，且开发环境轻量、适合 VS Code。

---

## 架构设计（设计阶段）

### MVP 架构概览
- 前端：极简 HTML 页面（如 index.html），通过按钮触发“启动/停止 Nginx”、“查看状态/日志”等操作。
- 后端：Python + Flask 提供 REST API，负责调用 MCP server（docker-mcp、fs-mcp）实现容器管理、配置读取、日志检索。
- 配置文件：.devbox.json，存储 Nginx 容器参数（如端口、镜像名等）。
- MCP server 集成点：
  - docker-mcp：启动/停止 Nginx 容器，获取状态、日志
  - fs-mcp：读取 .devbox.json 配置

### 目录结构建议
```
/ (根目录)
├── backend/           # Python + Flask 后端
│   ├── app.py         # 主入口
│   ├── routes/        # 路由
│   └── ...
├── frontend/          # 前端静态资源
│   └── index.html     # 极简页面
├── .devbox.json       # Nginx 容器配置
├── README.md
└── ...
```

### 主要模块与职责
| 模块         | 说明                                   |
|--------------|----------------------------------------|
| 前端页面     | 展示按钮，发起 API 请求                 |
| 后端 API     | 提供 /start, /stop, /status, /logs 等接口 |
| MCP 调用层   | 封装 docker-mcp、fs-mcp 的调用逻辑      |
| 配置管理     | 读取/写入 .devbox.json                  |

### 关键接口示例
- `POST /api/start`：启动 Nginx 容器
- `POST /api/stop`：停止 Nginx 容器
- `GET /api/status`：获取容器状态
- `GET /api/logs`：获取容器日志

### 架构图（后续补充）

---

## 需求梳理（规划阶段）

### DevBox 项目核心需求（精简 MVP 版）
1. 支持通过 Web 面板一键启动/停止一个常用开发容器（Nginx）。
2. 支持查看该容器的运行状态、日志输出、端口映射信息。
3. 支持通过简单的 JSON 配置文件（如 .devbox.json）自定义 Nginx 容器参数。
4. 支持通过 MCP Server（docker-mcp、fs-mcp）实现容器管理、配置读取、日志检索等自动化操作。
5. 提供极简直观的前端界面，适配桌面浏览器。

### 需求优先级建议
- MVP（最小可用产品）：仅实现 Nginx 容器的启动/停止、状态与日志查看、简单配置。
- 进阶功能：多容器支持、批量操作、配置版本管理、界面美化、可扩展性等。

---

## 技术选型
- 后端：Python + Flask
- 前端：HTML + Tailwind（或 Vite + Vue-lite，可选）
- 配置/数据：JSON/YAML
- 容器调度：Docker CLI + Compose
- 主要 MCP Server：docker-mcp、fs-mcp、log-mcp、git-mcp、time-mcp、openapi-mcp（可选）

---

## 项目进度表（Roadmap & 状态追踪）
| 序号 | 阶段   | 标题         | 变更内容         | 状态     | 负责人         | 相关 MCP Server      | 备注 |
|------|--------|--------------|------------------|----------|----------------|----------------------|------|
| 1    | 规划   | 需求梳理     | 明确 DevBox 项目目标、功能需求 | 已完成 | Copilot Agent | Git, 文件系统, 时间  |      |
| 2    | 设计   | 架构设计     | 完成 MVP 架构设计 | 进行中 | Copilot Agent | UML, OpenAPI         |      |
| 3    | 开发   | 代码实现     | 初始化代码结构、依赖管理 | 未开始 | Copilot Agent | Git, NPM, 文件系统, CI |      |
| 4    | 测试   | 单元与集成测试 | 编写并运行测试脚本     | 未开始 | Copilot Agent | CI, 安全扫描, Puppeteer |      |
| 5    | 部署   | 容器化与部署 | Docker Compose 部署    | 未开始 | Copilot Agent | Docker, Kubernetes    |      |
| 6    | 维护   | 日志与监控   | 日志分析、监控配置      | 未开始 | Copilot Agent | Logging, Metrics, Issue Tracker |      |

> 状态：未开始、进行中、待审核、已完成
> 变更内容与 MCP Server 字段将由 Agent 在每次操作后自动补充

---

## MCP Server 使用表
| 阶段   | MCP Server         | 作用           | 触发指令/操作         | 效果与表现 | 备注 |
|--------|--------------------|----------------|-----------------------|------------|------|
| 规划   | Git, 文件系统, 时间 | 拉取需求、文档 | list-issues, 读取文档 |            |      |
| 设计   | UML, OpenAPI       | 生成架构、接口 | 生成 UML、接口校验    |            |      |
| 开发   | Git, NPM, CI       | 代码管理、依赖 | 分支、提交、CI        |            |      |
| 测试   | CI, 安全扫描, Puppeteer | 测试与安全   | 运行测试、扫描       |            |      |
| 部署   | Docker, Kubernetes | 部署与发布     | 容器部署、服务发布    |            |      |
| 维护   | Logging, Metrics, Issue Tracker | 日志监控 | 日志检索、监控      |            |      |

---

## 关键开发记录
- 每次阶段切换、重要决策、MCP server 效果评估等将在此补充。

---

## MCP-SDLC 调研结论（持续补充）
- 各阶段 MCP server 的实际表现、优缺点、改进建议等将在开发过程中逐步完善。

---

> 本 README.md 由 Copilot Agent 自动维护，确保任何新 Agent 均可据此无缝接手开发与调研。
