# dsh-chatbot-preset

为 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) 增加 **Chatbot** 模式：能联网、看图、反问，**不能**读写文件或执行命令。

Adds a **Chatbot** mode to [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness): web search, image reading and clarifying questions — with **no** file or shell access.

## 安装 / Install

在 Creator 模式（`cordis` 预设）的会话里执行 / In a Creator-mode (`cordis` preset) session:

```text
action: install_bundle
target: github:leipeng1107/dsh-chatbot-preset
```

从 npm 安装 / From npm: `target: dsh-chatbot-preset`

装完刷新页面，Settings → Agent presets 出现 **Chatbot** 卡片，新建会话时选中即可。已存在的会话不受影响。

Refresh the page afterwards; a **Chatbot** card appears under Settings → Agent presets. Pick it when starting a new session. Existing sessions keep their preset.

卸载 / Uninstall: `plugin_manager action: remove_bundle target: dsh-chatbot-preset`

## 内容 / What's inside

| 行 / Row | 提供 / Provides |
|---|---|
| `dsh-tool-web` | `web_search`, `web_fetch` |
| `dsh-tool-ask-user` | `ask_user_question` |
| `dsh-tool-fs`（仅 `readImage` / readImage only） | `read_image` |
| `dsh-time-context` | 时钟上下文 / clock context |
| compaction 组 / group | 长对话压缩 / long-conversation compaction |

不含 / Not included：shell、文件写入 / file writes、`grep`/`glob`、subagent、plan mode、`agent-instructions`。

## 自定义 / Customize

改 `cordis.patch.yml` 后重装。Edit `cordis.patch.yml`, then reinstall the bundle.

- 允许写文件 / Allow file writes → `tool-fs` 的 `write`/`edit` 改为 `true`
- 反问不阻塞 / Non-blocking questions → `tool-ask-user` 加 `config: { mode: timed, timeout: 120 }`
- 改人设 / Change persona → `persona.prefix`（`{{…}}` 除 `{{model}}` 外不要写 / no `{{…}}` other than `{{model}}`）

本包是纯配置 bundle：无 `index.js`、无依赖、无安装脚本。
Pure-config bundle: no `index.js`, no dependencies, no install scripts.

## 许可证 / License

[MIT](LICENSE) © 2026 leipeng1107
