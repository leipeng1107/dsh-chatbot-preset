# Chatbot 预设 bundle（`@local/dsh-chatbot-preset`）

给 DeepSeek Harness Web 新增一个 **Chatbot** 智能体预设（agent preset）。Web 里「新会话选择器 / Settings → Agent presets」中的每个 mode 都是一个 `@deepseek-ai/dsh-agent-preset` 声明行，本 bundle 只做一件事：插入这样一行，预设 id 为 `chatbot`。

## 这个预设能做什么

| 能力 | 行 | 说明 |
|---|---|---|
| 联网搜索 | `tool-web`（`search: true`） | `web_search`，多查询并发、结果需引用 URL |
| 抓取网页 | `tool-web`（`fetch: true`） | `web_fetch`，HTML 转 Markdown |
| 反问用户 | `tool-ask-user` | `ask_user_question`，阻塞模式（与内置预设一致） |
| 读取图片 | `tool-fs`（仅 `readImage: true`） | `read_image`，只读 PNG/JPEG/WebP/GIF |
| 时钟上下文 | `time-context` | 让模型能解释未限定的日期时间 |
| 长对话不溢出 | `compaction` 组 | `compaction-basic` + `/compact` + `tool-result-pruner` |

明确**不包含**：`write` / `edit` / 读文件 / glob / grep、bash / pwsh / 终端、subagent / workflow、todo、plan mode、`present`、`agent-instructions`（避免把工作区 AGENTS.md 的编码指令带进对话）。

## 安装

在 DeepSeek Harness 里让 agent 调用 `plugin_manager`：

```text
action: install_bundle
target: C:\Users\LEI\Documents\deepseek-harness\chatbot_workspace\chatbot-preset
```

该操作会写入当前 profile 的依赖与 bundle 列表，属于需要提升权限的操作；即使审批策略为 `never`，也可以由具备 Full access 的会话执行，或在 Web 的 Plugins 页面手动安装本目录。安装后：

- Settings → Agent presets 会出现 **Chatbot** 卡片（英文界面为 *Chatbot Preset*）；
- 新会话选择器可选中它，新建的会话才使用该预设；
- 已存在的会话保留它们原来的预设。

本 bundle 不含 `.` 运行时导出、无 `index.js`、无依赖、无安装脚本——它是纯 patch bundle（与官方 MCP bundle 模板同构）。

## 文件

- `package.json`：`dsh.bundle.patch` 指向 patch，导出 `./locale/*.json` 与 `./icon`
- `cordis.patch.yml`：唯一的实质内容，`insert` 一条 `@deepseek-ai/dsh-agent-preset` 行
- `locale/en.json`、`locale/zh.json`：Plugin Manager 卡片标题与描述（不激活插件也可读）
- `icon.svg`：原创对话气泡图标

## 调整

- **改文案**：`cordis.patch.yml` 里 `persona.prefix`。注意 persona 文本是模板，`{{…}}` 会按严格模式解析，除 `{{model}}` 外不要写花括号。
- **改能力**：增删 `config.plugins` 里的行；可用包名见 `cordis-composition-reference` 的 packages 清单。若要让 Chatbot 能写文件，把 `tool-fs` 的 `write`/`edit` 改回 `true`（会同时引入改写风险）。
- **改反问行为**：给 `tool-ask-user` 加 `config: { mode: timed, timeout: 120 }`。
- **设为默认预设**：那是 `agent-preset-registry` 的 `default` / `selectedDefault`，不在本 bundle 内；改它会连带影响所有新会话。

## 撤销

`plugin_manager` 的 `set_bundle`（`enabled: false`）可停用，`remove_bundle` 可卸载。注意本地目录安装是链接语义：删除本目录会让预设失效。

## 已知限制

- 未在 Web 界面中验证渲染效果（卡片外观、选择器顺序）——需要安装后在浏览器里确认。
- `read_image` 还要求当前模型声明支持图片输入；文本模型下该工具会拒绝执行并给出切换模型的提示。
- 预设不是安全沙箱：它只决定挂哪些插件，不限制这些插件的能力。
