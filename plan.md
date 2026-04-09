# Plan: 系统性拆解 Claude Code v2.1.88 源码

## Context

`source/unpack/src/` 下的 Claude Code 源码包含约 2,000+ 文件、数十万行 TypeScript/TSX 代码。文档按依赖关系分层，从入口到核心循环再到子系统逐步深入，共 8 个阶段。

---

## Phase 1: 入口与启动流程

**目标**：理解 Claude Code 如何启动，掌握整体目录布局

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| 目录全貌 | `source/unpack/src/` 下各目录 | 浏览 37 个顶层目录，了解命名规范和 import 模式 |
| CLI 快速路径 | `entrypoints/cli.tsx` (302 行，全读) | `--version`、`--daemon`、`--dump-system-prompt` 等分支短路逻辑 |
| REPL 启动 | `replLauncher.tsx` (23 行，全读) | App 包裹 REPL 的方式 |
| 主引导流 (A) | `main.tsx` 1-500 行 | import 区 + Commander.js CLI 参数解析，映射所有 flag |

---

## Phase 2: 初始化与配置系统

**目标**：理解完整的启动序列：配置加载、认证、Feature Flag

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| 主引导流 (B) | `main.tsx` 500-1200 行 | `run()` 函数：解析参数 → 认证 → GrowthBook → 启动 REPL |
| 主引导流 (C) | `main.tsx` 1200-4683 行 | 会话恢复(`--resume`)、打印模式、SDK 模式、无头模式 |
| 初始化 | `entrypoints/init.ts` (~300 行，全读) + `bootstrap/state.ts` | 配置启用、环境变量、TLS、代理、遥测、仓库检测 |
| 配置系统 | `utils/config.ts`、`utils/settings/settings.ts`、`utils/settings/types.ts`、`utils/settings/validation.ts`、`utils/settings/applySettingsChange.ts` | 多源配置：用户/项目/本地/策略/CLI flag |
| Feature Flag | `services/analytics/growthbook.ts`、`utils/betas.ts` | `feature()` 编译时函数 + GrowthBook 运行时 |
| 认证 | `utils/auth.ts`、`services/oauth/client.ts`、`utils/secureStorage/` | OAuth、API Key、订阅类型 |

---

## Phase 3: 核心类型、状态与 Tool 接口

**目标**：掌握基础类型系统、状态管理和 Tool 接口——后续所有模块的前置知识

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| 共享类型 | `types/` 全部 11 个文件 | `command.ts`、`permissions.ts`、`hooks.ts`、`ids.ts`、`logs.ts`、`plugin.ts` 等，无依赖用于打破循环引用 |
| 状态管理 | `state/AppStateStore.ts`、`state/AppState.tsx`、`state/store.ts`、`state/selectors.ts`、`bootstrap/state.ts` | 两层状态：React Context（AppState）+ 全局可变状态（bootstrap/state） |
| Tool 接口 (A) | `Tool.ts` 1-350 行 | 类型定义：ToolInputJSONSchema、ToolUseContext、ToolResult、ToolProgress |
| Tool 接口 (B) | `Tool.ts` 350-792 行 | Tool 生命周期：isEnabled → validateInput → checkPermissions → call → render |
| Tool 注册表 | `tools.ts` (~500 行，全读) | 所有工具的收集、`feature()` 条件加载 |

---

## Phase 4: 核心查询循环

**目标**：理解 Claude Code 的心脏——agentic 循环

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| QueryEngine (A) | `QueryEngine.ts` 1-400 行 | 会话状态、对话历史、预算跟踪 |
| QueryEngine (B) | `QueryEngine.ts` 400-900 行 | 消息生命周期、流式事件处理 |
| QueryEngine (C) | `QueryEngine.ts` 900-1295 行 | compact 触发、预算限制、错误处理 |
| 查询循环 (A) | `query.ts` 1-300 行 | `query()` async generator 签名、QueryParams |
| 查询循环 (B) | `query.ts` 300-1000 行 | `queryLoop()` 核心：API 调用 → 解析响应 → tool_use 执行 → 重复 |
| 查询循环 (C) | `query.ts` 1000-1729 行 | 错误恢复、auto-compact、prompt 过长处理 |
| API 客户端 | `services/api/claude.ts`、`services/api/client.ts`、`services/api/errors.ts`、`services/api/withRetry.ts` | 与 Anthropic API 通信 |
| 工具编排 | `services/tools/toolOrchestration.ts` (188 行) | 并发安全工具并行执行，非安全工具串行 |

---

## Phase 5: Tool 实现

**目标**：深入学习重要 Tool 的实现模式

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| BashTool (A) | `tools/BashTool/BashTool.tsx` 1-200 行 | schema 定义、`call()` 入口 |
| BashTool (B) | `tools/BashTool/bashPermissions.ts`、`bashSecurity.ts` (2592 行)、`shouldUseSandbox.ts`、`commandSemantics.ts` | 权限规则匹配、安全检查、沙箱决策、命令语义分析 |
| BashTool (C) | `utils/bash/ast.ts`、`utils/bash/commands.ts`、`utils/bash/bashParser.ts` (4436 行) | Bash AST 解析和命令拆分 |
| 文件读取 | `tools/FileReadTool/` 全目录 | 图片处理、PDF 支持 |
| 文件编辑/写入 | `tools/FileEditTool/`、`tools/FileWriteTool/` | diff 替换机制 |
| AgentTool (A) | `tools/AgentTool/AgentTool.tsx`、`builtInAgents.ts`、`loadAgentsDir.ts` | 子代理系统、内置代理类型 |
| AgentTool (B) | `tools/AgentTool/runAgent.ts`、`forkSubagent.ts`、`resumeAgent.ts`、`agentMemory.ts` | 子代理执行、fork、恢复 |
| 搜索/辅助工具 | `tools/GlobTool/`、`tools/GrepTool/`、`tools/WebSearchTool/`、`tools/TodoWriteTool/`、`tools/AskUserQuestionTool/` | 简单工具模式参考 |
| 任务协调工具 | `tools/TaskCreateTool/`、`tools/TaskOutputTool/`、`tools/TaskGetTool/`、`tools/TaskUpdateTool/`、`tools/TaskListTool/`、`tools/ConfigTool/` | 后台任务管理 |

---

## Phase 6: 权限系统

**目标**：理解多层权限系统如何控制工具执行

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| 权限类型与模式 | `types/permissions.ts`、`utils/permissions/PermissionMode.ts`、`utils/permissions/PermissionResult.ts`、`utils/permissions/PermissionRule.ts` | PermissionMode、PermissionBehavior、规则类型 |
| 规则加载与匹配 | `utils/permissions/permissions.ts`、`utils/permissions/permissionsLoader.ts`、`utils/permissions/permissionRuleParser.ts`、`utils/permissions/pathValidation.ts` | 从设置加载规则、模式匹配 |
| Auto 模式与分类器 | `utils/permissions/autoModeState.ts`、`utils/permissions/bashClassifier.ts`、`utils/permissions/yoloClassifier.ts`、`utils/permissions/denialTracking.ts` | 自动权限决策逻辑 |
| 权限 UI | `components/permissions/`、`hooks/useCanUseTool.tsx`、`hooks/toolPermission/` | 权限请求对话框 |

---

## Phase 7: UI 层——Ink、REPL 与组件

**目标**：理解 React + Ink 终端 UI 渲染

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| Ink 框架 (A) | `ink/ink.tsx`、`ink/renderer.ts`、`ink/reconciler.ts`、`ink/render-to-screen.ts`、`ink/terminal.ts` | React reconciler 终端渲染 |
| Ink 框架 (B) | `ink/layout/`、`ink/wrap-text.ts`、`ink/parse-keypress.ts`、`ink/hooks/` | flexbox 布局、文本换行、按键解析 |
| REPL (A) | `screens/REPL.tsx` 1-300 行 | import、props、状态初始化 |
| REPL (B) | `screens/REPL.tsx` 300-1200 行 | 消息渲染、PromptInput 集成 |
| REPL (C) | `screens/REPL.tsx` 1200-3000 行 | effect hooks、QueryEngine 交互、用户输入处理 |
| REPL (D) | `screens/REPL.tsx` 3000-5005 行 | 消息渲染辅助、键绑定、模态状态 |
| 关键组件 | `components/App.tsx`、`components/PromptInput/`、`components/Message.tsx`、`components/VirtualMessageList.tsx`、`components/Markdown.tsx` | 核心组件实现 |
| 快捷键与 Vim | `keybindings/`、`vim/` | 键绑定系统和 Vim 模式 |

---

## Phase 8: 子系统与集成

**目标**：学习剩余子系统，完成全貌

| 模块 | 关键文件 | 要点 |
|------|----------|------|
| 系统提示词 | `constants/prompts.ts` (914 行)、`utils/systemPrompt.ts` | 提示词分段构建、缓存边界 |
| 上下文收集 | `context.ts`、`utils/claudemd.ts`、`memdir/` | CLAUDE.md 加载、memory 文件系统 |
| 命令系统 | `commands.ts`、`commands/help/`、`commands/compact/`、`commands/config/`、`commands/commit/` | 命令注册与执行模式 |
| MCP 集成 | `services/mcp/client.ts` (3348 行)、`services/mcp/MCPConnectionManager.tsx`、`services/mcp/config.ts`、`tools/MCPTool/` | Model Context Protocol 客户端 |
| Hooks 系统 | `utils/hooks/hookHelpers.ts`、`utils/hooks/AsyncHookRegistry.ts`、`utils/hooks/hooksConfigManager.ts` | 预/后置工具钩子 |
| 上下文压缩 | `services/compact/compact.ts`、`services/compact/autoCompact.ts`、`services/compact/microCompact.ts` | 对话历史压缩策略 |
| Skills 与 Plugins | `skills/loadSkillsDir.ts`、`skills/bundledSkills.ts`、`utils/plugins/pluginLoader.ts`、`plugins/builtinPlugins.ts` | 技能与插件发现加载 |
| 后台任务 | `tasks/types.ts`、`tasks/LocalShellTask/`、`tasks/LocalAgentTask/`、`tasks/InProcessTeammateTask/` | 任务类型与执行 |
| Swarm/多代理 | `utils/swarm/teamHelpers.ts`、`utils/swarm/permissionSync.ts`、`utils/swarm/backends/` | 多代理协作 |
| 遥测与分析 | `services/analytics/index.ts`、`services/analytics/growthbook.ts`、`services/analytics/datadog.ts` | 分析与遥测 |
| Bridge/远程 | `bridge/bridgeMain.ts` (2999 行)、`bridge/types.ts`、`remote/RemoteSessionManager.ts` | IDE 桥接与远程会话 |
| LSP 集成 | `services/lsp/manager.ts`、`services/lsp/LSPClient.ts`、`tools/LSPTool/` | Language Server Protocol |

---

## 大文件阅读策略

| 文件 | 行数 | 分几次读 |
|------|------|----------|
| `main.tsx` | 4,683 | 3 次 |
| `REPL.tsx` | 5,005 | 4 次 |
| `query.ts` | 1,729 | 3 次 |
| `QueryEngine.ts` | 1,295 | 3 次 |
| `bashParser.ts` | 4,436 | 3 次 |
| `bashSecurity.ts` | 2,592 | 2 次 |
| `prompts.ts` | 914 | 2 次 |

先扫 `export` 和函数声明建心智地图，再分段精读。
