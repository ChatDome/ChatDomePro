# ChatDomePro

ChatDomePro 是基于 Pi 构建的通用计算机智能体，特别强化 Linux 主机运维。它与 ChatDome 长期并行：ChatDome 保持轻量、保守，ChatDomePro 负责开放任务、多步调查、连续执行和受控主机变更。

当前状态：愿景、边界和首版实施基线已确认；应用代码尚未开始。

## 文档阅读顺序

1. [领域词汇](CONTEXT.md)
2. [产品愿景与范围](docs/01-product/vision-and-scope-zh.md)
3. [确认决策基线](docs/01-product/decision-baseline-zh.md)
4. [Pi 集成与系统架构](docs/02-architecture/system-architecture-zh.md)
5. [安全与执行规范](docs/03-specifications/security-and-execution-zh.md)
6. [长期记忆与审计规范](docs/03-specifications/memory-and-audit-zh.md)
7. [原始 Pi 对照验证](docs/05-validation/pi-vs-chatdomepro-validation-zh.md)
8. [首版实施计划](docs/04-implementation-plans/chatdomepro-v1-implementation-plan-zh.md)
9. [架构决策记录](docs/adr/)

## 核心边界

- Pi 负责模型、循环、会话、压缩、终端、扩展和技能。
- ChatDomePro 负责 Podman 沙箱、风险、审批、根用户执行器、长期记忆和审计。
- 模型工具在任务容器运行；用户直接输入的 Pi shell 以普通 Linux 用户在控制节点运行。
- Telegram 等远程渠道由独立 Pi 插件提供。
- 首版单实例、单控制节点、单活动任务，不优化远程多主机。
