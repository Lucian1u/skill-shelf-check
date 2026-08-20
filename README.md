# Skill Shelf Check

一个 zero-install、fully offline、local-first、read-only 的 Agent Skills 可视化诊断工具。

分发产物是单个 HTML 文件：[`skill-shelf-check.html`](skill-shelf-check.html)。普通用户下载后可以直接双击打开，选择自己的 Skills 文件夹进行检查。不需要 Node、npm、命令行或本地服务器。

**状态：** Final Acceptance 已通过。当前代码覆盖首个稳定公开版本的范围。具体版本与历史以 [`CHANGELOG.md`](CHANGELOG.md) 和 GitHub Releases 为准。

## 直接在线使用

不需要下载，也不需要安装。打开这个页面即可开始：

**[打开 Skill Shelf Check](https://lucian1u.github.io/skill-shelf-check/)**

1. 打开上面的链接
2. 点击「选择 Skills 文件夹」
3. 选择你电脑上的 Skills 文件夹，开始检查

网页本身通过 HTTPS 加载。你选中的 Skill 文件只在当前浏览器里处理，不会上传到 GitHub 或其它地方。检查是只读的，工具不会修改你的文件。

想完全离线使用时，仍然可以下载单个 HTML 文件，见下方 Quick Start。

---

## 项目定位

Skill Shelf Check 是：

- **zero-install** — 双击 HTML 即可使用
- **fully offline** — 产品代码不依赖外部脚本、字体或 CDN
- **local-first** — 文件只在当前浏览器页面本地处理
- **read-only** — 不修改、不删除、不自动修复 Skills
- **single-file HTML** — 给普通用户的产物就是一个文件

它扫描用户主动选择的本地 Skills 根目录中所有名为 `SKILL.md` 的文件，用卡片和需复核清单标出值得人工查看的项。

---

## 为什么存在

用户从 GitHub、社区、插件市场不断收集 Skills 之后，很难快速知道：

- 哪些 Skill 缺字段
- 哪些重名
- 哪些全文重复
- 哪些 description 高度相似
- 哪些本地 Markdown 链接已经失效

Skills 散落在子目录里。只看文件名，看不出两个 Skill 是否同名、正文是否相同、描述是否几乎一样，或是否指向一个并不存在的本地文件。

这个工具只做第一眼的只读体检，不代替人工判断，也不自动清理 Skills。

---

## 核心特性

当前已经实现、并通过正式验收的能力：

- 本地文件夹选择
- 自动发现名为 `SKILL.md` 的文件
- Skill 卡片展示（名称、描述、路径、文件数、最近修改时间）
- Missing required fields（缺 `name` 或 `description`）
- Same name（解析出的非空 `name` 完全相同）
- Exact full-text duplicate（整个 `SKILL.md` 原文严格相等）
- Similar description（规范化后的词 token 集合 Jaccard 相似度 `>= 0.80`）
- Broken local Markdown relative links（`SKILL.md` 中的普通行内相对链接，目标不在本次扫描文件集合中）
- 筛选：全部 / 需复核 / 正常
- 按 name / description / path 搜索
- 内置示例（不读取本地文件）
- 导出当前扫描的结构化 JSON 报告
- 完全本地处理；不上传文件
- 不自动修改或删除 Skills

Similar description 不是 AI 语义相似。完整规则见 [`docs/DECISIONS.md`](docs/DECISIONS.md) 的 D008。JSON 导出语义见 D009。

---

## Quick Start

在线使用：打开 [Skill Shelf Check](https://lucian1u.github.io/skill-shelf-check/)，点击「选择 Skills 文件夹」。

离线使用：

1. 获取 [`skill-shelf-check.html`](skill-shelf-check.html)
2. 双击打开
3. 点击「选择 Skills 文件夹」
4. 查看结果
5. 如有需要，导出 JSON

也可以先点「加载内置示例」，查看页面如何展示问题。

开发者可以从本仓库直接打开同一个 HTML 文件，无需安装步骤，也无需本地服务器。

---

## Privacy

- 用户选择的文件只在当前浏览器页面本地处理
- 不上传 Skill 内容
- 不做 analytics / telemetry
- 产品代码不依赖外部脚本、字体或 CDN
- 工具不会修改用户目录

页面上的隐私说明是：

> 文件仅在当前浏览器标签页内处理；本页没有网络请求，也不会修改所选目录。

这是产品边界，不是额外的安全认证或威胁模型承诺。

---

## What it checks

本工具只做下面五类检查。每条结果都会列出涉及的相对路径。

| 检查 | 含义 |
|---|---|
| Missing required fields | `name` 或 `description` 缺失，或存在该键但去掉首尾空白后为空 |
| Same name | 两个或更多 Skill 的解析 `name` 完全相同，且非空 |
| Exact full-text duplicate | 两个或更多 `SKILL.md` 的原文（`rawText`）严格相等，不做规范化 |
| Similar description | 两个有效、非空 description 经规范化后的词 token 集合，Jaccard 相似度 `>= 0.80` |
| Broken local Markdown relative links | `SKILL.md` 里的普通行内 Markdown 链接指向本地相对文件，且解析后的目标不在用户本次选择的文件集合中 |

Similar description 的计算是 token-set Jaccard，阈值是 `>= 0.80`。它不是 embedding，也不是 AI semantic similarity。若两个 Skill 已是 Exact full-text duplicate，则不再对同一组报告 Similar description。

本工具不检查外部 `http(s)` URL 是否可访问，也不递归检查 `SKILL.md` 之外的 Markdown。

---

## Boundaries / Non-goals

与产品页面「边界」一致。当前版本不会：

- 判断 Skill 内容是否事实正确或过时
- 读取 Agent 日志
- 做安全漏洞扫描
- 自动清理、自动删除或自动修复
- 对外部 URL 做网络验证
- 保证兼容所有 Agent 或所有 Markdown 语法
- 联网、上传文件或修改用户目录

发现的是需要人工复核的信号，不是内容保证。

---

## Repository structure

| 路径 | 作用 |
|---|---|
| [`skill-shelf-check.html`](skill-shelf-check.html) | 给普通用户的完整产品。CSS 和 JavaScript 都嵌在这个文件里。 |
| [`fixtures/`](fixtures/) | 固定测试输入，不打进分发用的 HTML。 |
| [`acceptance.md`](acceptance.md) | 正式验收记录：真实跑过的输入、结果和结论。 |
| [`docs/`](docs/) | 产品规格、状态、路线图和长期决定。 |
| [`.github/`](.github/) | Issue forms 和 pull request 模板。 |
| [`CHANGELOG.md`](CHANGELOG.md) | 变更记录。 |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | 如何参与这个仓库。 |
| [`SECURITY.md`](SECURITY.md) | 如何报告潜在漏洞。 |

长期产品要求在 [`docs/PRODUCT_SPEC.md`](docs/PRODUCT_SPEC.md)。诊断语义以 [`docs/DECISIONS.md`](docs/DECISIONS.md) 为准。

---

## Development / verification

- 没有 build step
- 没有 runtime dependencies
- 产品就是这一个 HTML 文件
- [`fixtures/`](fixtures/) 用来固定测试输入；不要为了让实现“通过”而改 fixtures
- 正式验收记录见 [`acceptance.md`](acceptance.md)

已记录的验收事实：

- Final Acceptance: **PASS**
- 已完成 macOS 上以 `file://` 双击打开的手工 smoke test
- Automated acceptance 使用 Playwright Chromium

这不表示已经验证所有 Chrome / Safari / Firefox，也不表示已经验证 Windows / Linux。

---

## Project status

- Final Acceptance: **PASS**
- 当前代码已经达到首个稳定公开版本的范围
- 具体版本与历史以 [`CHANGELOG.md`](CHANGELOG.md) 和 GitHub Releases 为准

开发进度见 [`docs/PROJECT_STATUS.md`](docs/PROJECT_STATUS.md)。

---

## Contributing / Security / License

- 贡献说明：[CONTRIBUTING.md](CONTRIBUTING.md)
- 行为准则：[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)
- 安全报告：[SECURITY.md](SECURITY.md)
- 许可证：[MIT License](LICENSE)
