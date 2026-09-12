---
doc-id: chatdomepro-system-architecture
authority: architecture
status: accepted
language: zh-CN
last-updated: 2026-09-12
---

# Pi 集成与系统架构

首版控制节点也是唯一目标主机；任务容器不是目标主机副本。

~~~mermaid
flowchart LR
 U[本地终端] --> A[Pi 与插件]
 R[远程 Pi 插件] --> A
 A --> S[Podman 任务容器]
 A --> C[审批与状态控制]
 C --> D[(SQLite)]
 C --> O[保护输出]
 C --> E[根用户执行器]
 E --> H[控制节点]
~~~

## 身份与职责

| 组件 | 身份和职责 | 禁止 |
| --- | --- | --- |
| Pi 与插件 | chatdomepro-agent 普通用户；会话、工具、终端、Podman | sudo、SQLite、根用户执行器套接字 |
| 审批控制器| chatdomepro-审批控制器普通用户；任务、风险、审批、锁、记忆、审计 | 模型、自然语言批准、root shell |
| 根用户执行器| root；验证、执行、幂等日志、结果查询 | 对话、模型、创建批准、网络监听 |

建议服务：chatdomepro-agent.service、chatdomepro-审批控制器.service、chatdomepro-根用户执行器.service。agent 直接嵌入 Pi；终端只是客户端。

## Pi 复用与数据权威

不得重写 Pi 的模型、认证、循环、会话、压缩、steer、followUp、abort、Token 统计、终端、扩展、技能和项目可信。

| 数据 | 唯一写入者 |
| --- | --- |
| Pi 消息、分支、压缩 | Pi 会话文件 |
| 未交付消息、任务 | 审批控制器SQLite |
| 审批、操作、记忆、审计 | 审批控制器SQLite |
| 大型输出 | 审批控制器保护目录 |
| 去重和最近执行状态 | 根用户执行器日志 |
| 模型凭据 | Pi 凭据目录 |
| 项目文件 | 项目工作区 |

SQLite 只保存 Pi 会话编号关联，不复制对话。

## 工具路由

| 入口 | 位置 |
| --- | --- |
| 模型文件工具与 bash | 任务容器 |
| 用户 !command、!!command | 控制节点普通用户 |
| 主机观察 | 审批控制器只读操作 |
| 主机变更 | 审批控制器创建操作和审批 |
| 记忆工具 | 审批控制器受限接口 |
| 插件工具 | Pi 进程，不能连接根用户执行器|

启动后必须能查询最终模型工具清单。模型工具仍指向控制节点时禁用或启动失败。

## 消息、取消和恢复

外部消息在有活动任务时先写 SQLite；使用者可转为引导并通过 Pi steer 交付；Pi 接受后才标记 已交付，消息编号去重。取消调用 Pi abort 并终止可安全终止的容器进程；root 操作继续到可判定终态。

恢复顺序：审批控制器打开 SQLite 并验证审计；根用户执行器加载日志；Pi 进程使用 SessionManager 恢复 Pi；恢复未交付消息；查询未完成操作；不明确时标记为结果未知并查现场；原任务标 中断，等待决定。不得按最后 Pi 消息自动重放。

## 协议、状态和锁

内部只用 Unix 域套接字。agent 只能访问 审批控制器；审批控制器单独访问 根用户执行器；根用户执行器核对操作系统身份。请求含版本、持久编号、类型、期限、摘要，并限制大小和超时。

~~~text
task: queued -> 有效 -> completed | failed | cancelled | 中断
operation: proposed -> waiting_approval -> denied | expired | approved
approved -> dispatched -> running -> succeeded | failed | 结果未知| cancelled
~~~

操作 proposed 后不可改。approved 不代表执行；dispatched 断线不能退回 approved；结果未知不自动重试。

每目标主机只有一把操作锁，观察与变更都串行。容器和用户直接 shell 不占锁。

## 降级

| 故障 | 保留 | 禁止 |
| --- | --- | --- |
| Podman | 对话、研究 | 模型 shell、文件修改 |
| 审批控制器| 对话、研究、可用沙箱 | 记忆写入、审批、主机操作 |
| 根用户执行器| 对话、沙箱、记忆、可安全观察 | 特权变更 |
| 审计链 | 查询、对话、沙箱 | 新主机变更 |

故障不得把模型工具退回控制节点。
