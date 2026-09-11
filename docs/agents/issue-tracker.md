# Issue tracker: GitHub

ChatDomePro 的工作项和规格保存在 GitHub Issues 中，使用 GitHub 命令行工具 `gh` 操作。

## Repository

- Repository: `ChatDome/ChatDomePro`
- Remote: `https://github.com/ChatDome/ChatDomePro.git`

在本仓库中运行 `gh` 时，由 Git 远程配置确定目标仓库。

## Conventions

- 创建：使用 `gh issue create`；多行正文写入临时文件并通过 `--body-file` 提交。
- 阅读：使用 `gh issue view <number> --comments`，同时读取正文、评论和标签。
- 列表：使用 `gh issue list`，按状态和标签筛选。
- 评论：使用 `gh issue comment <number>`。
- 修改标签：使用 `gh issue edit <number> --add-label` 或 `--remove-label`。
- 关闭：使用 `gh issue close <number>`，并记录实施结果或不处理原因。

## Pull requests as a triage surface

不将外部合并请求作为需求分诊入口。

## Skill behavior

当技能要求发布到问题跟踪器时，创建 GitHub Issue。

当技能要求读取相关任务时，读取对应 Issue 的正文、评论和标签。

`to-spec` 和 `to-tickets` 生成的工作项直接使用 `ready-for-agent`，无需再次分诊。

## Dependencies

优先使用 GitHub 原生阻塞关系记录任务依赖。

原生依赖不可用时，在工作项正文中写入：

`Blocked by: #<number>`

只有所有阻塞任务均已关闭的工作项才可以开始实施。
