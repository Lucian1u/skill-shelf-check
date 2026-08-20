# Security Policy

Skill Shelf Check 是一个在用户浏览器里本地运行的单文件 HTML 工具。它不是安全扫描器，也不对用户 Skills 做漏洞分析。

## Supported versions

目前还没有正式的 `v1.0.0` release。

在 `v1.0.0` 之前，安全修复只针对当前开发版本。正式 release 之后再更新版本支持策略。

这里不提供尚未存在的版本支持矩阵。

## Reporting a vulnerability

请不要把潜在漏洞直接发成公开 Issue。

仓库发布到 GitHub 后，优先使用 GitHub 的 private vulnerability reporting（仓库 Security 页上的 “Report a vulnerability”）。

如果该功能暂不可用，请使用 repository maintainer 在 GitHub profile 上公开提供的私人联系方式。不要在公开讨论里粘贴可利用细节。

当前没有单独的安全邮箱，也没有承诺回复时限、漏洞赏金或 CVE 流程。

## Scope note

仍然欢迎报告与本工具实际运行方式有关的问题，例如：

- 产品代码发起了非预期的网络请求
- 不安全的 DOM 处理（例如把 Skill 文本当 HTML 插入）
- 意外暴露扫描结果或文件内容
- 本地文件处理上的问题

报告时请尽量说明：打开方式（例如 `file://`）、浏览器、是否使用内置示例或本地文件夹，以及如何复现。不要附上私人或敏感的 `SKILL.md` 原文。
