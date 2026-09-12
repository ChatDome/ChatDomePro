---
status: accepted
---

# 将模型工具路由到 rootless Podman

Pi 不内置沙箱，模型文件和 shell 工具因此进入每任务 rootless Podman，特权操作进入 根用户执行器。用户明确输入的 Pi shell 仍以普通用户运行；该区分限制模型异常并保留直接终端能力。
