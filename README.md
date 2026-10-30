# dsh-chatbot-preset

**A community agent preset for [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) that adds a Chatbot mode to the Web UI.**

**为 DeepSeek Harness 添加 Chatbot 模式的社区智能体预设（agent preset）。**

DSH Web 的「新会话选择器 / Settings → Agent presets」里每个 mode 都是一个
`@deepseek-ai/dsh-agent-preset` 声明行。本包只做一件事：插入这样一行，预设 id 为
`chatbot`，让模型能联网、看图、必要时反问，但**不能**读写文件或执行命令。

---

## 特性 | Features

| 能力 | 挂载的行 | 模型可见工具 |
|---|---|---|
| 联网搜索 | `@deepseek-ai/dsh-tool-web`（`search: true`） | `web_search` |
| 抓取网页正文 | `@deepseek-ai/dsh-tool-web`（`fetch: true`） | `web_fetch` |
| 反问用户 | `@deepseek-ai/dsh-tool-ask-user` | `ask_user_question` |
| 看图 | `@deepseek-ai/dsh-tool-fs`（仅 `readImage`） | `read_image` |
| 时间上下文 | `@deepseek-ai/dsh-time-context` | —（系统提示注入） |
| 长对话压缩 | `dsh-compaction-basic`、`/compact`、`tool-result-pruner` | — |

明确**不包含**：

- `write` / `edit` / `read` / `glob` / `grep` —— 不能碰你的文件
- bash / pwsh / 终端 —— 不能执行命令
- subagent / workflow —— 不做委派
- todo、plan mode、`present`
- `agent-instructions` —— 避免把工作区 `AGENTS.md` 的编码指令带进对话

> 定位是「日常对话助手」，不是「第五个编码代理」。想让它写文件，见下方
> [自定义](#自定义--customize)。

---

## 安装 | Installation

### 方式一：从 GitHub 直接安装（推荐）

在 DeepSeek Harness 里，对任意具备 Creator 模式（`cordis` 预设）的会话说：

```text
用 plugin_manager 安装 bundle：github:leipeng1107/dsh-chatbot-preset
```

或者等价地直接指定包名：

```text
action: install_bundle
target: github:leipeng1107/dsh-chatbot-preset
```

安装需要 `danger-full-access` 或当次审批；它会写入当前 profile 的依赖与
bundle 列表，影响该 profile 下的所有会话，并持久保留。

### 方式二：从 npm 安装

```text
action: install_bundle
target: dsh-chatbot-preset
```

### 方式三：本地目录（开发调试）

把仓库克隆到任意位置，然后：

```text
action: install_bundle
target: C:\path\to\dsh-chatbot-preset
```

本地目录以 `link:` 方式安装，**删除目录会让预设失效**。

### 安装后

1. 打开 Settings → Agent presets，应出现 **Chatbot** 卡片（英文界面为 *Chatbot Preset*）；
2. 新建会话时在预设选择器里选它；
3. 已存在的会话保持它们原有的预设——预设只影响新会话与显式切换。

若卡片没立刻出现，刷新一次页面即可（名册由 `agentPresets/list` 提供）。

---

## 验证安装 | Verify

免审批的检查方式（让会话里的 agent 执行）：

```text
cordis_inspect_query  platform: host  provider: Config  method: listConfigs
input: { "name": "@deepseek-ai/dsh-agent-preset" }
```

期望看到 `include:preset-chatbot`，且 `status` 为 `schema`，与内置的
`preset-standard` / `preset-ptc` / `preset-minimal` / `preset-cordis` 一致。

也可以看 `plugin_manager` 的 `list_plugins`：`include:preset-chatbot` 应为
`enabled: true`、`fiberPhase: active`。

---

## 卸载 | Uninstall

```text
plugin_manager  action: remove_bundle  target: dsh-chatbot-preset
```

只想临时停用而不卸载：

```text
plugin_manager  action: set_bundle  target: dsh-chatbot-preset  enabled: false
```

---

## 自定义 | Customize

所有内容都在 `cordis.patch.yml` 一个文件里，改完重装即可（本包不含 JS 构建产物）。

**让它能写文件**：把 `tool-fs` 的开关改回默认：

```yaml
- id: tool-fs
  name: '@deepseek-ai/dsh-tool-fs'
  config:
    read: false
    readImage: true
    write: true
    edit: true
```

**让反问不阻塞**：给 `tool-ask-user` 加配置：

```yaml
- id: tool-ask-user
  name: '@deepseek-ai/dsh-tool-ask-user'
  config:
    mode: timed
    timeout: 120
```

**改人设**：编辑 `persona.prefix`。注意它是模板，`{{…}}` 按严格模式解析，
除 `{{model}}` 外不要写花括号。

**设为默认预设**：那是 `agent-preset-registry` 的 `default` / `selectedDefault`，
不在本包范围内；改它会影响所有新会话。

**改名/改排序**：`config.name` 与 `config.order`（内置预设占 1–4，本包为 5）。

---

## 结构 | What's inside

```text
dsh-chatbot-preset/
├── cordis.patch.yml   # 唯一实质内容：插入 preset-chatbot 行
├── package.json       # dsh.bundle.patch 指向上面；导出 locale 与 icon
├── locale/en.json     # 插件清单里的标题与描述（英文）
├── locale/zh.json     # 同上（中文）
├── icon.svg           # 原创对话气泡图标
├── README.md
├── CHANGELOG.md
└── LICENSE            # MIT
```

本包是 **patch-only bundle**：没有 `index.js`、没有运行时依赖、没有安装脚本，
与 DSH 官方的 MCP bundle 同构。它不执行任何代码，只是声明一串插件行。

---

## 常见问题 | FAQ

**这算插件吗？会不会有安全风险？**
它是 bundle（bundle 可以是纯配置）。本包不含可执行代码，只声明用哪些官方插件行。
不过 agent preset 本身不是安全沙箱：它决定挂哪些插件，不限制这些插件的能力。

**为什么没有文件读写？**
预设定位是对话助手。挂上 `tool-fs` 的写能力会让它顺手改你的仓库——多数人不需要。

**`read_image` 提示模型不支持图片？**
它要求当前模型声明支持图片输入。换一个多模态模型即可。

**能不能同时装多个预设包？**
可以，id 不冲突即可。本包用 `chatbot`，不与 `standard/ptc/minimal/cordis` 冲突。

**支持哪些 DSH 版本？**
在 DeepSeek Harness Desktop（Windows）上验证。只用到长期稳定的
`@deepseek-ai/dsh-agent-preset` 声明格式与官方插件包，没有私有 API。

---

## 贡献 | Contributing

欢迎提 PR / Issue。改动 `cordis.patch.yml` 时请注意：

- 引用的包名必须存在于 `cordis-composition-reference` 的可用包清单；
- `persona.prefix` 里的 `{{…}}` 除 `{{model}}` 外不要出现；
- `tool-result-pruner` 需满足 `headChars + tailChars < thresholdChars`；
- 提交前至少确认 `package.json` 与 `cordis.patch.yml` 能被正常解析。

---

## 许可证 | License

[MIT](LICENSE) © 2026 leipeng1107
