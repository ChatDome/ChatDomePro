# ChatDomePro

基于 Pi 的智能体验证项目，逐步接入 ChatDome 的命令审批、受控执行、审计和主动巡检能力。

当前状态：实施方案已编写，应用代码、依赖安装和模型对照测试尚未开始。

## 实施入口

- [Pi 验证与特色能力接入实施方案](docs/04-implementation-plans/chatdomepro-pi-validation-plan-zh.md)
- [Pi 官方仓库](https://github.com/earendil-works/pi)
- [ChatDome 原项目](https://github.com/ChatDome/ChatDome)

首个里程碑是固定模型与软件版本、建立用量记录、复现一项 ChatDome 失败案例。先验证原版 Pi，再逐项接入特色能力。

ChatDomePro 使用独立配置、会话和测试环境。现有 ChatDome 保留为产品及对照基线，不共享运行数据，也不在此阶段切换现有部署。

开发目录：`D:\University\github\ChatDomePro`。
