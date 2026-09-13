---
doc-id: chatdomepro-pi-v1-version-research
authority: research-evidence
status: completed
language: zh-CN
last-updated: 2026-09-13
---

# 首版 Pi 正式版本锁定与接口调研

## 结论与适用范围

ChatDomePro 首版锁定 **`@earendil-works/pi-coding-agent@0.85.1`**，使用 npm 包管理器官方注册表中的发布包，通过包根入口嵌入软件开发工具包（SDK）。Node.js 选用 **24 长期支持系列，首轮验证基线为 `24.21.0`**。本次是依赖选型和接口审查，不代表已完成 Linux 安装、类型编译、模型调用或沙箱验收；不创建功能代码和应用依赖锁文件。

官方版本与安全公告核对截至 2026-09-12，文档于 2026-09-13 完成审阅。后续发布不会自动改变本文锁定值。

本文落实[嵌入固定版本 Pi 的架构决策](../adr/0001-embed-pinned-pi.md)，受[安全与执行规范](../03-specifications/security-and-execution-zh.md)约束。本文的“已核实”指官方发布元数据、发布包声明或固定版本源码可证实；“集成判断”指依据这些证据形成的 ChatDomePro 约束；“待验证”指尚未通过实际运行确认。正式版本指非预发布版本，不表示 Pi 已承诺跨版本接口稳定。

## 版本身份与可重复获取

| 项目 | 锁定值与证据 |
| --- | --- |
| 官方仓库 | [`earendil-works/pi`](https://github.com/earendil-works/pi) |
| npm 包与精确版本 | `@earendil-works/pi-coding-agent`，`0.85.1`；[官方版本元数据](https://registry.npmjs.org/@earendil-works%2fpi-coding-agent/0.85.1) |
| 正式发布 | [`v0.85.1`](https://github.com/earendil-works/pi/releases/tag/v0.85.1)，2026-09-05；检索日官方发布页标为 Latest；[npm 根元数据](https://registry.npmjs.org/@earendil-works%2fpi-coding-agent)的 `dist-tags.latest` 亦为 `0.85.1`，该包发布时间为协调世界时 `2026-09-05T12:17:19.281Z` |
| 对应提交 | `d981de1229ef899957bbe968bc8dcda02a21f477`；npm 元数据 `gitHead` 与[发布提交](https://github.com/earendil-works/pi/commit/d981de1229ef899957bbe968bc8dcda02a21f477)一致 |
| 发布包 | [pi-coding-agent-0.85.1.tgz](https://registry.npmjs.org/@earendil-works/pi-coding-agent/-/pi-coding-agent-0.85.1.tgz) |
| 完整性摘要 | `sha512-FGRN+OHbWaefBPGaTggAdLjrIHW+s2PzLyglz/5dfLzb9of7uuXMXYC0fJIeZTw+shS32o2cuQ9jF7YSDuL/oQ==`；已下载官方发布包并实算核对 |
| 依赖形态 | 包内含 `npm-shrinkwrap.json`；已核对锁文件格式版本为 3，Pi 的 `chord`、`pi-agent-core`、`pi-ai`、`pi-tui`、`pi-telemetry` 解析版本均为 `0.85.1` |

官方已于 2026-05-07 迁移仓库和包命名空间。`0.74.0` 是 `@earendil-works` 命名空间首版；`@mariozechner/pi-coding-agent@0.73.1` 是旧包最后一版，已废弃且没有下文安全公告的修复版本。新集成不得采用旧包或依赖其临时导入兼容映射。[官方迁移公告](https://pi.dev/news/2026/5/7/pi-has-a-new-home)

选择 `0.85.1` 的直接理由是它已修复 `0.85.0` 的 SDK 导入失败。修复同时将 `client`、`experimental/plugin` 子路径及实验服务端、客户端命令限于源码开发使用；官方支持的本地 SDK 和标准输入输出远程过程调用接口不受该撤回影响。不得因仓库存在这些实验目录，就将它们视为首版可用的发布接口。[固定版变更记录](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/CHANGELOG.md#0851---2026-09-05)

## Node.js 与模块要求

已核实 Pi `engines.node` 为 **`>=22.19.0`**；不是 Node.js 20，也不是任意 22.x。包为 ECMAScript 模块（ESM），根导入入口是 `dist/index.js`，类型入口是 `dist/index.d.ts`。发布包的声明与固定标签源码一致。[固定版包声明](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/package.json)

集成判断：首版采用 Node.js 24 长期支持系列。官方 `24.21.0` 发布正文日期为 2026-09-08。Node.js 24 的主动维护到 2026-10-20，计划支持到 2028-04-30；22 系列计划支持到 2027-04-30；20 系列已于 2026-04-30 结束支持。最低兼容声明不能代替选择仍受支持的补丁版本。[Node.js 24.21.0 发布说明](https://nodejs.org/en/blog/release/v24.21.0)、[官方维护日程](https://github.com/nodejs/Release/blob/main/schedule.json)

待验证：在首版支持的 Ubuntu、Debian 和两种目标处理器架构上，实际验证 Node.js `24.21.0`、发布包及其可选原生依赖。当前没有干净 Linux 安装和 SDK 导入运行结果，不能据声明直接宣称兼容性验收通过。后续建立应用锁文件时仍固定直接 Pi 依赖为 `0.85.1`，保留完整传递依赖和摘要，使用锁文件安装；不得只依靠源码中的 `^0.85.1`。

## 公开接口与复用清单

下表以固定版 `src/index.ts` 和发布包 `dist/index.d.ts` 的根导出为准；内部文件仅作为审查证据，不构成允许深层导入的约定。[固定版公开导出](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/index.ts)

| ChatDomePro 需要 | 已核实的公开接口 | 使用约束 |
| --- | --- | --- |
| 嵌入单个会话 | `createAgentSession`、`AgentSession` | 复用模型循环；不直接操作私有字段 |
| 随会话切换重建运行状态 | `createAgentSessionRuntime`、`AgentSessionRuntime`、`createAgentSessionServices`、`createAgentSessionFromServices` | 工作目录相关服务由工厂重建 |
| 凭据、模型及配置 | `ModelRuntime`、`SettingsManager` | 复用 Pi 凭据与模型状态，不实现第二份认证 |
| 会话历史 | `SessionManager` | Pi 文件是会话权威；控制状态继续放审批控制器 |
| 扩展、技能、上下文 | `DefaultResourceLoader`、`ResourceLoader`、`ExtensionAPI`、`loadSkills` | 显式处理项目可信；插件是同进程可信代码 |
| 自定义工具 | `defineTool`、`ToolDefinition`、`create*ToolDefinition`、各 `*Operations` 类型 | 可替换实际执行函数；不是单纯提示词覆盖 |
| 终端 | `InteractiveMode`，另有 `runPrintMode`、`runRpcMode`、`RpcClient` | 公开终端入口不等于已经有可分离后台客户端；见待验证事项 |

`CreateAgentSessionOptions.tools` 是**工具名称数组 `string[]`**，不是工具实例数组。实际定义通过 `customTools: ToolDefinition[]` 传入，也可由扩展 `pi.registerTool()` 注册。该选项没有 `toolsOptions` 字段；不要把工具工厂的 `ToolsOptions` 类型误当成会话工厂参数。允许列表 `tools` 与排除列表 `excludeTools` 可限制最终注册范围，排除优先；`noTools: "builtin"` 关闭默认内置工具而保留扩展、自定义工具，`noTools: "all"` 指初始无工具，不是操作系统权限边界。[固定版会话工厂类型](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/sdk.ts)

## 会话、排队、引导和取消

| 能力 | 核对结论与官方来源 |
| --- | --- |
| 新建、恢复、列举 | `SessionManager.create`、`open`、`continueRecent`、`list`、`listAll` 可用；`inMemory` 不持久化。树结构、分支、压缩由 Pi 管理。[会话管理源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/session-manager.ts) |
| 运行中替换会话 | `newSession`、`switchSession`、`fork`、`importFromJsonl` 在 **`AgentSessionRuntime`**，不在 `AgentSession`。旧会话先取消、关闭并释放，新建失败向调用方抛错；新会话对象需要重新订阅事件和绑定扩展。[运行时源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/agent-session-runtime.ts) |
| 引导 | `session.steer(text)` 在当前助手轮的工具调用完成后交付，不会强杀当前工具；不能当作取消。[SDK 消息语义](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/sdk.md#prompting-and-message-queueing) |
| 后续消息 | `session.followUp(text)` 等智能体停止后交付。两种队列都会展开文件提示模板，不能排入扩展命令。[SDK 消息语义](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/sdk.md#prompting-and-message-queueing) |
| 输入受理 | `prompt` 流式运行时需指定 `streamingBehavior`；`preflightResult(true)` 仅表示受理、排入队列或即时处理，不表示工作完成、持久化或幂等交付成功。[SDK 输入约定](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/sdk.md#prompting-and-message-queueing) |
| 取消 | `session.abort()` 取消重试、压缩、分支摘要并调用 `agent.abort()`，随后等待空闲。工具是否实际停止仍取决于其取消信号处理。[取消源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/agent-session.ts) |

集成判断：Pi 的待交付引导与后续消息是内存队列，不能替代审批控制器持久消息队列。会话通过消息完成事件保存历史，进程崩溃可能发生在受理与持久记录之间；外部编号、交付状态和去重仍由审批控制器持有。Pi 不自动保证全进程只有一个活动任务，也不提供 ChatDomePro 主机操作的批准、幂等或结果未知语义。[队列与消息落盘源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/agent-session.ts)、[本项目恢复约束](../02-architecture/system-architecture-zh.md#消息取消和恢复)

集成判断：取消模型推理和任务容器进程，与根用户执行器完成已开始的主机操作分开处理。不能将 Pi `abort` 的返回解释为主机副作用已停止或回滚。终端复用采用公开 `InteractiveMode`；其与后台会话分离、断线恢复的具体接入仍须独立验证，不能用 `0.85.1` 已撤回的实验客户端填补。

## 工具替换能力及沙箱边界

已核实八种内置名称：`read`、`bash`、`powershell`、`edit`、`write`、`grep`、`find`、`ls`，默认启用 `read`、`bash`、`edit`、`write`。每种均有公开工具定义工厂和可替换操作接口；首版 Linux 无需启用 `powershell`。[工具工厂与操作类型](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/tools/index.ts)

已核实同名覆盖：最终注册先建立内置定义，再写入扩展及 `customTools`，后者覆盖同名项；本版 `customTools` 位于扩展定义之后。`getAllTools()` 可查工具及 `sourceInfo`，`getActiveToolNames()` 可查活动工具名称。重载会重建注册表，因此启动、会话替换和重载后均应核对最终工具来源，不能只在第一次注册时检查。[注册表构建源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/agent-session.ts)

集成判断：优先使用公开同名工具定义覆盖，将模型文件和 shell 操作转交任务容器；必须覆盖或排除所有读取、搜索、列目录工具，不能只替换 `bash`。下列细节要求逐项审查，不能把注入 `operations` 直接写成已满足沙箱规范：

- `read` 在调用自定义文件操作前，仍通过 `resolveReadPathAsync` 探测控制节点文件是否存在；路径辅助函数直接调用 Node.js 文件接口。需要通过公开完整工具定义替换避免此路径，而非假定自定义 `readFile` 足以隔离。[读取工具](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/tools/read.ts)、[路径辅助函数](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/tools/path-utils.ts)
- `BashOperations.exec` 可接收输出回调、取消信号、超时和环境；默认后端在控制节点创建子进程。模型后端不得使用该默认执行器，也不得在 Podman 失败时回退它。内置包装还暂存超限输出，需另审输出落点和脱敏。[shell 执行源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/tools/bash.ts)
- shell 工具默认注入会话文件路径及模型信息；`exposeSessionEnvironment: false` 可关闭。向任务容器传递环境必须使用明确允许列表，不能整体继承 Pi 凭据环境。[环境变量文档](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/environment-variables.md#shell-tool-session-environment)
- 同名覆盖的结果结构、`details` 与渲染约定必须匹配；仅修改执行不自动保留提示元数据。用户明确输入的 `!`、`!!` 命令属于 `user_bash` 入口，必须与模型 shell 路由分开。[扩展工具覆盖与用户 shell 文档](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/extensions.md#overriding-built-in-tools)

Pi 扩展可调用 Node.js 文件和进程接口，以 Pi 身份执行任意代码。工具活动列表、`tool_call` 拦截和项目可信都是集成能力，不能替代 Podman、普通用户权限、审批控制器或根用户执行器。当前公开完整工具替换接口仍可用于封闭上述路径，**没有形成必须维护 Pi 补丁的证据**。[官方扩展权限说明](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/extensions.md#extension-locations)、[本项目 Podman 决策](../adr/0003-rootless-podman-model-tools.md)

## 项目可信与已知安全问题

### 嵌入接口的可信默认值

固定版提供 `ProjectTrustStore` 和资源加载可信回调，但裸 `createAgentSession()` 不能据交互命令的行为假定自动询问信任。`SettingsManager.create()` 的 `projectTrusted` 默认值是 `true`；`DefaultResourceLoader.reload()` 只有提供 `resolveProjectTrust` 回调时才先以未信任状态加载全局扩展，再作项目决定。[设置默认值](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/settings-manager.ts)、[资源加载顺序](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/resource-loader.ts)

`createAgentSessionServices` 也不自动补齐信任决策；它将 `resourceLoaderReloadOptions` 传给加载器。可通过其公开选项接入 `resolveProjectTrust`，但必须同时给创建设置的步骤显式未信任初值，避免前序加载。[服务工厂源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/agent-session-services.ts)

集成判断：创建设置时显式采用未信任状态，在加载项目可执行资源之前复用 Pi 的可信存储和公开加载流程。每次跨工作目录恢复、分支及重载重新确认适用状态；无人交互且无已有决定时保持不信任。不可先运行默认加载再补询问，也不可把模型回答作为项目可信决定。此为需要在最小集成中验证的接入义务，并非认定 `0.85.1` 存在尚未公告的新漏洞。

### 官方公告核对

核对日为 2026-09-12。下表使用 GitHub 安全公告编号（GHSA）；旧包与新包分别指 `@mariozechner/pi-coding-agent`、`@earendil-works/pi-coding-agent`。公告记录的前三项旧包均无修复版本。

| 官方公告 | 影响版本与前提 | 修复版与对首版的结论 |
| --- | --- | --- |
| [GHSA-7v5m-pr3q-6453](https://github.com/earendil-works/pi/security/advisories/GHSA-7v5m-pr3q-6453) | 旧包 `>=0.27.5 <=0.73.1`；新包 `>=0.74.0 <0.78.1`。导出网页包含不安全链接，打开并点击后可能执行跨站脚本；影响导出文档，不是 Pi shell 执行 | `0.78.1`；`0.85.1` 不在公告影响范围。迁移已有不可信导出时须重新生成 |
| [GHSA-r95r-rj6r-c39x](https://github.com/earendil-works/pi/security/advisories/GHSA-r95r-rj6r-c39x) | 旧包 `>=0.28.0 <=0.73.1`；新包 `>=0.74.0 <0.78.1`。本地用户能遍历凭据目录时，`auth.json` 写入权限竞态可能泄露凭据 | `0.78.1`；`0.85.1` 不在影响范围。私有凭据目录仍是部署要求 |
| [GHSA-jfgx-wxx8-mp94](https://github.com/earendil-works/pi/security/advisories/GHSA-jfgx-wxx8-mp94) | 旧包 `>=0.50.0 <=0.73.1`；新包 `>=0.74.0 <0.78.1`。共享 Linux 临时目录被预置后，通过 `-e` 临时 npm、git 扩展安装触发，以 Pi 使用者身份执行攻击代码 | `0.78.1`；`0.85.1` 不在影响范围。此处提权是取得受害使用者权限，不等于自动成为根用户 |
| [GHSA-mqxh-6gq7-558m](https://github.com/earendil-works/pi/security/advisories/GHSA-mqxh-6gq7-558m) | 新包 `<0.79.0`；启动不可信仓库时隐式加载项目扩展及配置 | `0.79.0`；`0.85.1` 不在公告影响范围，但 SDK 嵌入仍须按上文接入可信流程 |

“不在已查公告影响范围”不等于无漏洞，亦不构成传递依赖、所有第三方插件或 Podman 的完整审计结论。后续必须针对实际应用锁文件再次执行依赖安全检查。

Pi 官方明确不提供内置沙箱或文件、进程、网络、凭据的权限限制。因此 ChatDomePro 的模型异常和提示注入防护仍须由本项目的确定性执行边界承担。[固定版安全说明](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/security.md)

### 发布包依赖审计快照

2026-09-12，在 Windows 临时目录以官方发布包的 `package.json` 和 `npm-shrinkwrap.json` 为输入，使用本机 Node.js `24.12.0`、npm `11.6.2` 执行以下只读依赖审计查询：

```text
npm.cmd audit --omit=dev --ignore-scripts --json --registry=https://registry.npmjs.org
```

命令退出码为 `0`；官方 npm 审计返回的各严重程度漏洞数及总数均为 `0`，依赖统计总数为 `164`。这是该日上游发布包锁定依赖树的公告匹配结果；没有安装依赖、执行 Pi 或验证 Node.js `24.21.0`，也没有扫描尚未创建的 ChatDomePro 应用锁文件。该结果不能作为无漏洞或运行安全证明。[审计输入发布包](https://registry.npmjs.org/@earendil-works/pi-coding-agent/-/pi-coding-agent-0.85.1.tgz)、[npm 官方 audit 说明](https://docs.npmjs.com/cli/v11/commands/npm-audit)

## 许可证与分发

Pi 固定标签的许可证为 MIT，版权人为 Mario Zechner，年份 2025；包元数据同样声明 MIT。许可文本允许使用、修改、分发及商业使用，分发副本或实质内容时须保留版权及许可文本。ChatDomePro 分发包应保留 Pi 许可，并针对实际打包的全部传递依赖生成第三方许可清单；Pi 的顶层许可不替代依赖自身许可。[固定版 LICENSE](https://github.com/earendil-works/pi/blob/v0.85.1/LICENSE)、[固定版包声明](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/package.json)

## 升级约束

以下为本次锁定产生的集成约束，不声称是 Pi 官方长期支持承诺：

1. Pi 直接版本使用精确 `0.85.1`，不使用 `latest`、`^`、`~`、移动分支或全局 Pi 安装作为运行依据。应用锁文件、运行时版本与发布摘要共同记录；原始 Pi 对照入口使用同一版本。
2. 每次升级重新核对完整发布包根导出、工具定义和操作接口、会话替换生命周期、项目可信、取消和队列行为。`0.84.0` 已有流式消息字段和认证接口不兼容变更，`0.85.0` 又曾出现发布包导入回归；不能仅凭补丁版本号跳过检查。[官方变更记录](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/CHANGELOG.md)
3. 使用者发起更新前，按项目规范备份控制状态及 Pi 会话；在副本上验证迁移与恢复。保留旧程序和对应数据备份，不承诺将新版会话文件直接交给旧版回滚。Pi 有会话迁移实现，不能推导反向迁移保证。[会话迁移源码](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/src/core/session-manager.ts)、[项目决策基线](../01-product/decision-baseline-zh.md)
4. 部署显式设置 `PI_SKIP_VERSION_CHECK=1`、`PI_TELEMETRY=0`；需要关闭启动联网时另设置 `PI_OFFLINE=1`。官方说明这些开关覆盖的行为不同，不能将关闭版本检查等同于禁用遥测、包更新或所有网络。它们不能限制可信插件网络，更不能替代任务容器网络控制。[固定版环境变量](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/environment-variables.md#pi-process-configuration)
5. 模型目录和供应商行为可独立变化。对照回归记录实际模型、参数和目录状态；Pi 版本固定不意味着上游模型行为固定。模型目录网络更新通过 `ModelRuntime` 控制。[SDK 模型目录说明](https://github.com/earendil-works/pi/blob/v0.85.1/packages/coding-agent/docs/sdk.md#model)
6. 仅在出现可复现绕过、且公开接口不能封闭时重新评估最小 Pi 补丁；否则保持官方发布包。安全修复升级仍走版本审查和针对性回归，不自动更新。

## 本次证据与未完成验收

已完成：官方迁移、发布、元数据、摘要、许可、安全公告及固定版公开接口核对；在临时目录静态查看发布包类型和收缩锁文件；仓库更新前已执行 `git pull` 并确认最新。

未完成且不得记为通过：干净 Linux 安装与类型编译、真实模型调用、会话恢复与取消的运行测试、Podman 工具替换和故障注入、终端断连重连、可信提示的实际交互验证、应用完整依赖与第三方许可证清单。上述属于后续实施验收；本次未开始正式功能实现。
