##### 一、项目定位与核心价值

[MCP（Model Context Protocol）Gateway](https://github.com) 是面向 Kubernetes 环境的反向代理与管理层，专为 **会话感知的路由** 与 **MCP 实例生命周期管理** 设计。它兼具 **数据平面**（流量转发）和 **控制平面**（部署、运维、监控）功能，为多实例的模型服务提供统一入口，解决了会话粘性、弹性伸缩、企业安全等关键痛点。

[![image](https://img2024.cnblogs.com/blog/510/202510/510-20251025191731073-1909333975.png "image")](https://github.com):[MeoMiao 萌喵加速](https://memiao.org)

##### 二、关键能力

























|  |  |
| --- | --- |
| 能力 | 说明 |
| **Session‑Aware Stateful Routing** | 同一 `session_id` 的请求始终落到同一 MCP 实例，确保对话上下文不被打断。 |
| **Enterprise‑Ready Management** | 通过 RESTful API 完成 MCP 的部署、更新、查询、日志、状态检查及删除等全生命周期管理。 |
| **Kubernetes‑Native** | 基于 StatefulSet 与 headless Service 实现弹性伸缩、服务发现和高可用。 |
| **生产特性** | 支持 Bearer Token + RBAC/ACL 鉴权、遥测、可观测性（日志、指标、追踪）。 |



---

##### 

三、概念模型

* **MCP Server**：实现 Model Context Protocol 的后端服务（如 SSE、流式 HTTP）。
* **Adapters**：在 `/adapters` 路径下注册的逻辑资源，代表各 MCP 实例，可与其他资源（如 `/agents`）共存。
* **Session‑Aware Routing**：确保同一会话始终使用同一后端实例，实现状态保持。

---

##### 四、控制平面 API（示例）













































|  |  |  |
| --- | --- | --- |
| 方法 | 路径 | 功能 |
| POST | `/adapters` | 部署并注册新 MCP |
| GET | `/adapters` | 列出所有 MCP |
| GET | `/adapters/{name}` | 获取适配器元数据 |
| GET | `/adapters/{name}/status` | 查询部署状态 |
| GET | `/adapters/{name}/logs` | 查看运行日志 |
| PUT | `/adapters/{name}` | 更新配置 |
| `DELETE` | `/adapters/{name}` | 删除实例 |

---

##### 五、数据平面路由（示例）

* `GET /adapters/{name}/sse` – Server‑Sent Events
* `POST /adapters/{name}/messages` – 基于会话的消息投递
* `POST /adapters/{name}/mcp` – 流式 HTTP 接口

这些接口均通过 **Session‑Aware Routing** 自动将请求路由到对应的 MCP 实例。

---

##### 六、项目结构概览

```
mcp-gateway/
 ├─ dotnet/                     # 主网关服务 (.NET 8)
 │   ├─ Microsoft.McpGateway.Service/
 │   └─ Microsoft.McpGateway.Management/
 ├─ mcp-example-server/        # 示例 MCP 服务器
├─ deployment/
 │   ├─ infra/azure-deployment.bicep
 │   └─ k8s/                    # K8s manifests
 ├─ openapi/                    # OpenAPI 3.0 规范
└─ workflows/                  # CI/CD
```

* **技术栈**：.NET 8 + ASP.NET Core、Docker、Kubernetes、分布式内存存储、Bearer Token + RBAC、Azure + Bicep。
* **部署方式**：本地 Docker Desktop + 本地 K8s；或 Azure AKS + 企业安全配置。

---

##### 七、快速入门路径

1. **Quick Start** – 首次部署，获取完整的示例与默认配置。
2. **Local Development Setup** – 本地环境（Docker + K8s）搭建，便于调试。
3. **Azure Cloud Deployment** – 使用 Azure Bicep 脚本在 AKS 上部署生产环境。
4. **VS Code Integration** – 与 VS Code 交互式测试，实时查看路由与日志。

---

##### 八、使用场景与优势

* **多模型服务统一入口**：在同一集群内运行多个模型实例，Gateway 自动实现会话粘性。
* **弹性伸缩**：基于 Kubernetes 的水平扩展，流量高峰时自动扩容。
* **企业安全**：统一的鉴权与审计，满足合规要求。
* **可观测性**：统一日志、指标、追踪，便于运维与故障排查。

---

##### 九、结语

MCP Gateway 为构建 **可扩展、会话感知、企业级安全** 的模型服务平台提供了完整的技术方案。通过 .NET 8 与 Kubernetes 的深度结合，它实现了从 **本地开发** 到 **云端生产** 的全链路支持，帮助开发者专注于模型本身，而将路由、管理、监控等基础设施交给 Gateway 统一处理。
