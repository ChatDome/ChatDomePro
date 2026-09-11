# Domain docs

ChatDomePro 采用单上下文领域文档布局。

## Before exploring

开始分析或修改代码前：

1. 读取仓库根目录的 `CONTEXT.md`。
2. 读取 `docs/adr/` 中与当前工作相关的架构决策记录。
3. 使用领域文档定义的术语编写代码、测试、规格和工作项。

文件不存在时继续工作，不提前创建空文档。`domain-modeling` 在形成实际术语或决策时按需创建。

## Layout

```text
ChatDomePro/
├── CONTEXT.md
├── docs/
│   ├── agents/
│   │   ├── issue-tracker.md
│   │   ├── triage-labels.md
│   │   └── domain.md
│   └── adr/
└── src/
```

`CONTEXT.md` 保存项目的领域词汇、概念关系及明确排除的近义词。

`docs/adr/` 保存难以撤销或会约束后续实施的架构决策记录。

## Vocabulary

工作项标题、规格、模块、接口和测试应使用 `CONTEXT.md` 中定义的术语。

需要使用尚未定义的概念时，先判断它是已有概念的不同说法，还是需要通过 `domain-modeling` 补充的新概念。

## Architecture decisions

如果拟议实现与现有架构决策记录冲突，必须明确指出冲突及重新评估的原因，不能静默覆盖原决策。
