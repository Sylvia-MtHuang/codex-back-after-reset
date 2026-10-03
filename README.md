# Codex Back After Reset

**五小时重置后，又是一条好汉。 / After the five-hour quota resets, back on your feet.**

A Codex desktop skill that arranges continuation at **90% of five-hour quota used**, keeps working until completion or actual exhaustion, and returns to the same chat after quota becomes available. It uses native Codex quota and chat-scheduling tools, with no API key or third-party service required.

这是一个给 Codex 桌面版使用的续跑 skill：**90% 安排续跑，安排后继续干，额度恢复后接着干。** 它使用 Codex 内置额度查询和原聊天定时任务，不需要 API key 或第三方服务。

## Install / 安装

在 Codex 中发送以下请求。 / Send this request in Codex:

```text
$skill-installer Install the skill from https://github.com/Sylvia-MtHuang/codex-back-after-reset/tree/main/skills/back-after-reset
```

或者下载本仓库，将 `skills/back-after-reset` 整个文件夹放入 Codex 的个人技能目录，保留其中的 `SKILL.md`、`agents` 和 `references`。

Alternatively, download this repository and copy the entire `skills/back-after-reset` folder into your personal Codex skills directory, preserving `SKILL.md`, `agents`, and `references`.

不同版本的个人技能路径可能不同：当前 Codex 文档列出 `~/.agents/skills/`；内置 skill-installer 可能安装到 `$CODEX_HOME/skills/`（通常为 `~/.codex/skills/`）。优先使用你的 Codex 内置安装器并确认技能列表里出现 **Back After Reset**。新安装的技能可在后续轮次使用；未出现时重新打开聊天或重启应用。

Personal skill locations can vary by version: current Codex documentation lists `~/.agents/skills/`, while the built-in skill-installer may use `$CODEX_HOME/skills/` (usually `~/.codex/skills/`). Prefer your built-in installer and confirm that **Back After Reset** appears in the skill list. Newly installed skills are available on subsequent turns; reopen the chat or restart the app if the skill does not appear.

## Use / 使用

在需要执行的任务中发送以下请求，选择一种语言即可。

Send either of these requests in the chat containing the task you want to execute.

**中文示例 / Chinese example**

```text
$back-after-reset 完成当前任务。额度已用达到 90% 时安排续跑，随后继续工作直到完成或额度耗尽；额度重置后在这个聊天继续。
```

**英文示例 / English example**

```text
$back-after-reset Complete my current task. Arrange continuation at 90% quota used, then keep working until completion or actual quota exhaustion. Resume in this chat after quota resets.
```

安装技能与授权自动续跑是两件事。上面的请求授权当前任务；其他人的安装不会默认授权该 skill 恢复他们的历史聊天。

Installing the skill and authorizing automatic continuation are separate actions. These requests authorize the current task; installation alone does not authorize the skill to resume your older chats.

### Daily starter messages / 每日定时开工

可以另行启用“定时开工”模式：在指定时间生成一句简短回复，尝试启动五小时额度窗口。下面的示例按瑞士本地时间安排，每次比前一条晚五小时零两分钟，缓冲逐次累加。

You can separately enable daily starter messages: brief model replies at selected times to try to start a five-hour quota window. This example uses Swiss local time, with each daytime message five hours and two minutes after the previous one, accumulating the buffer.

| 瑞士时间 / Europe/Zurich time | 相对整点延迟 / Offset from nominal slot |
|---|---|
| 05:00 | 0 分钟 / 0 minutes |
| 10:02 | 2 分钟 / 2 minutes |
| 15:04 | 4 分钟 / 4 minutes |
| 20:06 | 6 分钟 / 6 minutes |

**中文示例 / Chinese example**

```text
$back-after-reset 设置每日定时开工消息：Europe/Zurich 时区的 05:00、10:02、15:04、20:06。每次只回复一句简短消息，尝试启动五小时额度窗口，并报告观察到的重置时间。任务完成后保留这些每日安排，直到我暂停或取消。不要因开工消息重新启动已完成的任务。
```

**英文示例 / English example**

```text
$back-after-reset Set daily starter messages at 05:00, 10:02, 15:04 and 20:06 Europe/Zurich. Each time, give one brief reply to try to start a five-hour quota window and report the observed reset time. Keep these daily schedules after tasks finish until I pause or cancel them. Do not reopen completed tasks because a starter message fires.
```

时间和时区可以修改；上表只是一个示例，不会因安装 skill 或启用默认续跑而自动创建。四个时间分别设置，避免小时和分钟组合出多余触发。每天的开工安排与每个任务的续跑安排分开管理，可以说“暂停每日定时开工”或“取消每日定时开工”。账号额度由聊天共享，无需在每个聊天都复制一套。

Times and timezone are configurable; this table is an example, not a schedule enabled by installation or default recovery activation. Set each time separately to avoid extra hour/minute combinations. Daily starters and task recovery have separate lifecycles; say “pause daily starter messages” or “cancel daily starter messages” to stop the starter set. Account quota is shared across chats, so there is no need to copy the set into every chat.

**定时消息不能强制重置额度，也不能保证启动新窗口。** 每次执行也会消耗额度；实际状态以额度工具返回的数据为准。其他聊天的使用、调度延迟或睡眠都可能改变预期时间。若额度阻止模型启动，这条消息本身可能无法执行；每日时间表保持不变，未完成任务仍按实际重置时间加两分钟安排续跑。

**Scheduled messages cannot force a quota reset or guarantee a new window.** Each reply consumes some quota; use returned quota data for the actual state. Other chats, scheduling delays and computer sleep can change expected timing. If quota prevents a model turn from starting, the message may not execute. Keep the daily timetable unchanged; unfinished tasks still use actual reset time plus two minutes for recovery.

### Default activation / 默认启用

如果希望后续未完成任务默认使用，可以请 Codex 将下面**任意一版**规则合并到个人 `~/.codex/AGENTS.md`，保留已有内容。

To enable the skill by default for future unfinished tasks, ask Codex to merge **either version** of the following rule into your personal `~/.codex/AGENTS.md`, preserving existing instructions.

**中文规则 / Chinese rule**

```markdown
## 额度重置后续跑

执行已授权的 Codex 桌面版任务时，在任务开始加载 $back-after-reset，并遵循其中的额度检查、进度保存和原聊天恢复规则。额度已用达到 90% 时安排续跑，随后继续工作直到任务完成或额度实际耗尽。这适用于我的未完成任务，除非我暂停、取消或任务需要我提供输入。不要仅因讨论或管理 skill 而创建唤醒任务。恢复其他既有聊天前，检查其中的最新指令以及我对该范围的授权。若 skill 或必要工具缺失，说明限制。
```

**英文规则 / English rule**

```markdown
## Quota-reset continuation

For authorized Codex desktop task execution, load $back-after-reset at task start and follow its quota checks, checkpointing and original-chat recovery rules. At 90% used, arrange recovery and continue working until completion or actual quota exhaustion. This applies to my unfinished tasks unless I pause, cancel, or they need my input. Do not create wakes solely for discussions or skill-management work. Do not resume other existing chats without checking their latest instructions and my authorization for that scope. If the skill or required tools are missing, report the limitation.
```

这是一条给模型的执行指令，不是后台监控程序，也不能保证每个长工具调用中途都会检查额度。既有中断聊天需要另外检查和登记；不会因为它们处于空闲状态就重新启动。启用后你可以说“暂停自动续跑”或“取消这个任务”，该聊天应停用续跑唤醒。

This is an instruction for the model, not a background watchdog, and it cannot guarantee quota checks during every long tool call. Previously interrupted chats need separate inspection and registration; being idle does not justify restarting them. You can say “pause automatic continuation” or “cancel this task” to disable the recovery wake for that chat.

## Behavior / 行为

- 在任务开始、阶段结束和耗时操作前检查额度。 / Check quota at task start, milestones, and before expensive operations.
- 为持续性工作提前安排兜底唤醒，90% 时确认或更新安排，然后继续工作。 / Pre-arm a fallback wake for sustained work; confirm or update it at 90% used, then keep working.
- 使用额度工具返回的实际重置时间，建议加 2 分钟缓冲；不会简单地“从现在算 5 小时”。 / Use the actual reset time returned by the quota tool, with a recommended two-minute buffer; do not assume reset is five hours from now.
- 每个任务聊天只管理一个续跑 heartbeat；另行授权的每日开工安排独立保留。保存简短进度和外部操作结果。 / Manage one recovery heartbeat per task chat, retaining separately authorized daily starter schedules. Save concise progress plus confirmed external-operation results.
- 醒来后检查最新指令、实际额度、任务状态和正在运行的操作，再继续下一步。 / On wake, check newer instructions, actual quota, task state, and in-flight operations before continuing.
- 已完成、取消、暂停或等待用户输入时停止续跑；遇到周额度等阻塞时重新判断恢复条件。 / Stop recovery when finished, cancelled, paused, or waiting for user input; reassess recovery when weekly quota or another limit blocks work.
- 不自动购买额度、兑换重置券、切换模型或扩大原任务权限。 / Do not automatically purchase credits, redeem reset vouchers, switch models, or expand the task's authorization.

## Compatibility and limits / 兼容性与限制

**自动恢复需要内置额度查询和原聊天定时工具。** 在作者的环境中，这些能力名为 `get_usage_limits` 和 `automation_update`。工具可用性和调度功能可能因桌面版版本、账户和工作区而不同。安装 skill 不会增加环境中缺失的工具。

**Automatic recovery requires both native quota-reading and original-chat scheduling tools.** In the author's environment these capabilities are named `get_usage_limits` and `automation_update`. Tool availability and scheduler features can vary by desktop version, account and workspace. An installable skill does not make missing tools available.

本 skill 的主要目标是 **Codex 桌面版**。CLI 或 IDE 可以读取 skill、整理续跑记录，但不能假定拥有桌面版定时工具；缺少能力时只能保存进度并报告限制。

This skill primarily targets **Codex desktop**. CLI or IDE environments can read it and prepare checkpoints, but desktop scheduling tools cannot be assumed to exist there. When required capabilities are missing, save progress and report the limitation.

电脑和应用需要保持运行，工作目录需要可访问。睡眠、关机、应用退出或断网可能导致唤醒延迟或失败。它不会修改系统电源设置。

The computer and app must remain running, and the workspace must be accessible. Sleep, shutdown, app exit, or network failure can delay or prevent wakes. The skill does not change system power settings.

“重置后 2 分钟”是调度目标，不是精确执行保证。若当前版本只支持无法定位首次触发时间的周期 heartbeat，默认使用至少 60 分钟的支持周期作为兜底，恢复可能延后；提示词的时间检查也不能消除唤醒本身消耗的额度。账号额度由多个聊天共享，需避免重复执行和同时修改同一工作目录。

“Two minutes after reset” is a scheduling target, not an exact execution guarantee. If only recurring heartbeats with an unverified first-fire time are available, the default fallback uses a supported interval of at least 60 minutes, so recovery may be delayed. Time checks in the prompt do not eliminate quota consumed by the wake itself. Chats share account quota, so avoid duplicate execution and concurrent writes to the same workspace.

## Files / 文件

```text
skills/back-after-reset/
  SKILL.md                 核心规则 / Core workflow
  agents/openai.yaml       Codex 界面信息与自动发现 / UI metadata and implicit discovery
  references/runtime.md    调度约定、进度记录与唤醒提示词 / Scheduling, checkpoints and wake prompt
  references/scheduled-start.md  每日定时开工模式 / Daily starter mode
```

运行时进度应保存在工作区 `.back-after-reset/` 或原聊天，**不要保存到公开仓库或已安装 skill 目录**。

Save runtime progress in the workspace's `.back-after-reset/` directory or the original chat. **Do not store it in a public repository or the installed skill directory.**

## Verification / 验证

已执行的检查和仍未验证的真实运行条件见 [VALIDATION.md](VALIDATION.md)。场景检查不能证明真实账户在额度耗尽并重置后一定能够自动恢复。

See [VALIDATION.md](VALIDATION.md) for the checks actually performed and the remaining live-runtime verification limits. A scenario evaluation does not establish that a real exhausted account can restart successfully after reset.

## Official references / 官方资料

- [构建技能 / Build skills](https://learn.chatgpt.com/docs/build-skills)
- [原聊天定时任务 / Scheduled tasks in an existing chat](https://learn.chatgpt.com/docs/automations#schedule-a-task-inside-a-chat)

## License / 许可证

采用 MIT 许可证，完整条款见 [LICENSE](LICENSE)。

Released under the MIT license. See [LICENSE](LICENSE) for the full terms.
