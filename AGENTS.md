# ChatDomePro — Agent 工作规范

## 开始工作前

1. 读取 CONTEXT.md。
2. 读取 docs/01-product/vision-and-scope-zh.md 和 docs/01-product/decision-baseline-zh.md。
3. 读取相关 docs/adr/、架构规范和实施计划。
4. 代码或文档更新前执行 git pull。

规范中的“必须”和“不得”是首版约束。不得用提示词、界面隐藏或模型判断替代确定性安全控制。

## Agent skills

工作项与规格保存在 GitHub Issues，规则见 docs/agents/issue-tracker.md。标签规则见 docs/agents/triage-labels.md。领域词汇在 CONTEXT.md，架构决策在 docs/adr/，规则见 docs/agents/domain.md。

## 权威顺序

1. 已接受且未被取代的架构决策记录。
2. docs/03-specifications/ 规范。
3. docs/02-architecture/ 架构。
4. docs/01-product/decision-baseline-zh.md。
5. docs/04-implementation-plans/ 计划。
6. README 和 GitHub Issue。

发现冲突先修正文档或新增架构决策，不得静默选择。

## 实施约束

- 先审查固定版本 Pi，已有能力直接复用。
- 只有具体绕过可复现且公开接口无法封闭时才考虑最小 Pi 补丁。
- 不重写 Pi 的模型、认证、会话、压缩、引导、Token 统计、扩展或技能。
- 核心不实现 Telegram。
- 模型工具不得绕过 Podman、审批控制器或根用户执行器。
- Pi 插件是可信同进程代码；恶意插件不属于首版威胁模型。
