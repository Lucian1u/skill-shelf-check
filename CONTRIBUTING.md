# Contributing

感谢关注 Skill Shelf Check。本仓库的产品是一个 zero-install、fully offline、local-first、read-only 的单文件 HTML 工具。下面的约定是为了保护这个边界，不是为了增加流程。

## Before contributing

请先阅读：

- [`AGENTS.md`](AGENTS.md) — Agent / 贡献者如何恢复上下文，以及 Source of Truth 的优先级
- [`docs/PRODUCT_SPEC.md`](docs/PRODUCT_SPEC.md) — 产品要求、技术约束、验收要求和明确不做的事
- [`docs/DECISIONS.md`](docs/DECISIONS.md) — 不得随意推翻的长期决定，尤其是 D008（诊断语义）和 D009（UI / JSON 映射）
- [`docs/PROJECT_STATUS.md`](docs/PROJECT_STATUS.md) — 当前做到哪一步

本仓库以仓库内文件为跨会话的 Source of Truth。旧聊天、猜测或摘要不能覆盖规格。

如果要改变产品要求、技术边界或已接受的长期决定：先改对应的 Source of Truth 文件，再改代码。不要只在实现里静默改语义。

请同时遵守 [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)。

## Project constraints

贡献不能破坏这些边界：

- 分发给用户的产品仍是单个 `skill-shelf-check.html`
- fully offline
- local-first：用户文件只在当前浏览器页面处理
- read-only：不自动修改、删除、清理或修复用户 Skills
- 不引入 Node / npm
- 不引入 framework 或 build system
- 不引入 CDN、远程字体或外部脚本
- 不做 analytics / telemetry
- 不增加隐藏的网络访问

Fixtures 是固定测试输入。不要为了让新实现“通过”去改 [`fixtures/`](fixtures/) 的内容和期望结果。

## Types of contributions

欢迎：

- bug fixes
- 可访问性改进
- 文档修正
- 诊断正确性修复（必须符合 D008，不能另发明一套算法）
- 范围清楚、且符合产品边界的 feature proposal

Feature proposal 只是讨论，不代表会被接受，更不代表可以越过 [`docs/PRODUCT_SPEC.md`](docs/PRODUCT_SPEC.md) 的 Out of Scope。

通常不接受：

- 把产品改成需要安装或编译才能用
- 自动删除 / 自动修复 Skills
- 读取 Agent 日志
- 把本工具做成安全扫描器
- 对外部 URL 做网络验证
- 把 Similar description 改成 AI 语义相似

## Development

没有 install，也没有 build。

改产品时直接编辑 [`skill-shelf-check.html`](skill-shelf-check.html)。CSS 和 JavaScript 都写在这个文件里。

测试输入使用 [`fixtures/`](fixtures/)：

- `fixtures/normal/` — 2 个健康 Skill
- `fixtures/conflict/` — 同名、全文重复、失效本地链接
- `fixtures/empty/` — 没有 `SKILL.md`

页面里的「加载内置示例」是 HTML 内嵌数据，不会去读取 `fixtures/`，也不应发起网络请求。

不要新增 `package.json`、npm test、formatter 或 linter 依赖来“补齐工程化”。

## Testing expectations

只要改了产品行为，就需要：

- 覆盖受影响的 fixture（或说明为什么现有 fixture 不够、以及用了什么最小复现）
- 运行页面内的 self-check（若本次改动触及它）
- 做必要的浏览器验证，并写明打开方式（例如 `file://`）和实际输入
- 只记录真实跑过的结果，不写假 PASS
- 只有这次改动属于正式验收范围时，才更新 [`acceptance.md`](acceptance.md)

诊断相关改动必须能对照 D008 解释。不要用“看起来更聪明”替换已锁定的 Jaccard / `>= 0.80` 规则。

## Pull requests

- 一个 PR 只做一件事
- 写清 What / Why
- 给出真实测试结果：用了哪些 fixture、什么浏览器/打开方式、看到了什么
- 如果改变长期产品语义，先同步相应的 Source of Truth 文档
- 不要顺手重构无关代码
- 不要顺手加入依赖、构建链或网络请求

PR 模板见 [`.github/pull_request_template.md`](.github/pull_request_template.md)。
