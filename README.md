# dsh-chatbot-preset

给 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) 增加 **Chatbot** 模式：能联网、看图、反问，**不能**读写文件或执行命令。

## 安装

在 Creator 模式（`cordis` 预设）的会话里：

```text
action: install_bundle
target: github:leipeng1107/dsh-chatbot-preset
```

或从 npm：`target: dsh-chatbot-preset`

装完刷新页面，Settings → Agent presets 出现 **Chatbot** 卡片，新建会话时选中即可。已存在的会话不受影响。

卸载：`plugin_manager action: remove_bundle target: dsh-chatbot-preset`

## 内容

| 行 | 提供 |
|---|---|
| `dsh-tool-web` | `web_search`、`web_fetch` |
| `dsh-tool-ask-user` | `ask_user_question` |
| `dsh-tool-fs`（仅 `readImage`） | `read_image` |
| `dsh-time-context` | 时间上下文 |
| compaction 组 | 长对话压缩 |

不含 shell、文件写入、grep/glob、subagent、plan mode、`agent-instructions`。

## 自定义

改 `cordis.patch.yml` 后重装。常用：

- 允许写文件 → `tool-fs` 的 `write`/`edit` 改 `true`
- 反问不阻塞 → `tool-ask-user` 加 `config: { mode: timed, timeout: 120 }`
- 改人设 → `persona.prefix`（`{{…}}` 除 `{{model}}` 外不要写）

本包是纯配置 bundle，无 `index.js`、无依赖、无安装脚本。

## 许可证

[MIT](LICENSE) © 2026 leipeng1107
