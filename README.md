# Codex Back After Reset

**五小时重置后，又是一条好汉。**

A Codex desktop skill that arranges continuation at **90% of five-hour quota used**, keeps working until completion or actual exhaustion, and returns to the same chat after quota becomes available.

这是一个给 Codex 桌面版使用的续跑 skill：**90% 安排续跑，安排后继续干，额度恢复后接着干。** 它使用 Codex 内置额度查询和原聊天定时任务，不需要 API key 或第三方服务。

## Install / 安装

在 Codex 中发送：

```text
$skill-installer Install the skill from https://github.com/Sylvia-MtHuang/codex-back-after-reset/tree/main/skills/back-after-reset
```

或者下载本仓库，将 `skills/back-after-reset` 整个文件夹放入 Codex 的个人技能目录，保留其中的 `SKILL.md`、`agents` 和 `references`。

不同版本的个人技能路径可能不同：当前 Codex 文档列出 `~/.agents/skills/`；内置 skill-installer 可能安装到 `$CODEX_HOME/skills/`（通常为 `~/.codex/skills/`）。优先使用你的 Codex 内置安装器并确认技能列表里出现 **Back After Reset**。新安装的技能可在后续轮次使用；未出现时重新打开聊天或重启应用。

## Use / 使用

在需要执行的任务中发送：

```text
$back-after-reset 完成当前任务。额度已用达到 90% 时安排续跑，随后继续工作直到完成或额度耗尽；额度重置后在这个聊天继续。
```

安装技能与授权自动续跑是两件事。上面的请求授权当前任务；其他人的安装不会默认授权该 skill 恢复他们的历史聊天。

### Default activation / 默认启用

如果希望后续未完成任务默认使用，可以请 Codex 将下面这段规则合并到个人 `~/.codex/AGENTS.md`，保留已有内容：

```markdown
## Quota-reset continuation

For authorized Codex desktop task execution, load $back-after-reset at task start and follow its quota checks, checkpointing and original-chat recovery rules. At 90% used, arrange recovery and continue working until completion or actual quota exhaustion. This applies to my unfinished tasks unless I pause, cancel, or they need my input. Do not create wakes solely for discussions or skill-management work. Do not resume other existing chats without checking their latest instructions and my authorization for that scope. If the skill or required tools are missing, report the limitation.
```

这是一条给模型的执行指令，不是后台监控程序，也不能保证每个长工具调用中途都会检查额度。既有中断聊天需要另外检查和登记；不会因为它们处于空闲状态就重新启动。启用后你可以说“暂停自动续跑”或“取消这个任务”，该聊天应停用续跑唤醒。

## Behavior / 行为

- 在任务开始、阶段结束和耗时操作前检查额度。
- 为持续性工作提前安排兜底唤醒，90% 时确认或更新安排，然后继续工作。
- 使用额度工具返回的实际重置时间，建议加 2 分钟缓冲；不会简单地“从现在算 5 小时”。
- 每个聊天只管理一个原聊天 heartbeat，保存简短进度和外部操作结果。
- 醒来后检查最新指令、实际额度、任务状态和正在运行的操作，再继续下一步。
- 已完成、取消、暂停或等待用户输入时停止续跑。遇到周额度等阻塞时重新判断恢复条件。
- 不自动购买额度、兑换重置券、切换模型或扩大原任务权限。

## Compatibility and limits / 兼容性与限制

**Automatic recovery requires both native quota-reading and original-chat scheduling tools.** In the author's environment these capabilities are named `get_usage_limits` and `automation_update`. Tool availability and scheduler features can vary by desktop version, account and workspace. An installable skill does not make missing tools available.

本 skill 的主要目标是 **Codex 桌面版**。CLI 或 IDE 可以读取 skill、整理续跑记录，但不能假定拥有桌面版定时工具；缺少能力时只能保存进度并报告限制。

电脑和应用需要保持运行，工作目录需要可访问。睡眠、关机、应用退出或断网可能导致唤醒延迟或失败。它不会修改系统电源设置。

“重置后 2 分钟”是调度目标，不是精确执行保证。若当前版本只支持无法定位首次触发时间的周期 heartbeat，默认使用至少 60 分钟的支持周期作为兜底，恢复可能延后；提示词的时间检查也不能消除唤醒本身消耗的额度。账号额度由多个聊天共享，需避免重复执行和同时修改同一工作目录。

## Files / 文件

```text
skills/back-after-reset/
  SKILL.md                 Core workflow
  agents/openai.yaml       Codex UI metadata and implicit discovery
  references/runtime.md    Scheduling contract, checkpoints and wake prompt
```

运行时进度应保存在工作区 `.back-after-reset/` 或原聊天，**不要保存到公开仓库或已安装 skill 目录**。

## Verification / 验证

See [VALIDATION.md](VALIDATION.md) for the checks actually performed and the remaining live-runtime verification limits. A scenario evaluation does not establish that a real exhausted account can restart successfully after reset.

## Official references / 官方资料

- [Build skills](https://learn.chatgpt.com/docs/build-skills)
- [Scheduled tasks in an existing chat](https://learn.chatgpt.com/docs/automations#schedule-a-task-inside-a-chat)

## License

MIT — [LICENSE](LICENSE).
