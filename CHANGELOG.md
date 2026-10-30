# Changelog

All notable changes to this project are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-10-30

### Added

- The `chatbot` agent preset (`preset-chatbot`), declared as a single
  `@deepseek-ai/dsh-agent-preset` row in `cordis.patch.yml`.
- Web search and page fetching through `@deepseek-ai/dsh-tool-web`
  (`web_search`, `web_fetch`).
- Clarifying questions through `@deepseek-ai/dsh-tool-ask-user`
  (`ask_user_question`, blocking mode, matching the shipped presets).
- Read-only image support through `@deepseek-ai/dsh-tool-fs` with
  `read: false, readImage: true, write: false, edit: false` (`read_image`).
- Per-step clock context from `@deepseek-ai/dsh-time-context`.
- Long-conversation handling: `dsh-compaction-basic`, the `/compact` command
  and `dsh-compaction-tool-result-pruner`.
- Display metadata in English and Chinese (`locale/en.json`, `locale/zh.json`)
  plus an original icon, so the preset reads correctly in the plugin inventory.
- README with install and configuration instructions, and an MIT license.

### Notes

- The preset deliberately mounts no shell, no file mutation, no filesystem
  discovery, no subagent/delegation, no todo list, no plan mode and no
  `agent-instructions` row, so a workspace `AGENTS.md` cannot turn the
  conversational assistant into a coding agent.
- Verified on DeepSeek Harness Desktop (Windows): the declaration row mounts
  with `status: schema` and `fiberPhase: active`, matching the four shipped
  presets.

[1.0.0]: https://github.com/leipeng1107/dsh-chatbot-preset/releases/tag/v1.0.0
