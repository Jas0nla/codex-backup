# CODEX Backup

中文说明：

这个仓库用于整理 Codex 本地备份、恢复、故障排查相关文档。

当前重点内容：

- Codex 本地备份与恢复流程
- 恢复后 `By project` 丢失但 `Chronological list` 仍存在时的处理方法
- 避免把“workspace 没注册回 UI”误判成“聊天数据丢失”

当前文档：

- [`docs/codex-backup-and-restore-guide.md`](docs/codex-backup-and-restore-guide.md)

适用场景：

- 做过 Codex 本地备份或恢复
- `Chats` 看起来异常
- 需要确认问题到底是数据丢失、UI 缓存问题，还是 workspace root 丢失

English:

This repository stores documentation for local Codex backup, restore, and troubleshooting workflows.

Current focus:

- local Codex backup and restore procedures
- what to do when `By project` is missing after restore
- how to avoid mistaking missing workspace registration for chat data loss

Current document:

- [`docs/codex-backup-and-restore-guide.md`](docs/codex-backup-and-restore-guide.md)

Typical use cases:

- after a local Codex backup or restore
- when `Chats` behaves unexpectedly
- when you need to distinguish between true data loss, UI/cache issues, and missing workspace roots
