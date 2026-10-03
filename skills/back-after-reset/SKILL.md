---
name: back-after-reset
description: Use when Codex desktop work may exhaust its five-hour quota, when a task was interrupted by quota, or when the user requests quota-reset continuation or daily scheduled messages to try to start a quota window.
---

# Back After Reset

Five hours later, back on your feet.

Arrange continuation at **90% used**, then keep working until the task finishes or the quota actually blocks execution. This threshold schedules recovery; it does not pause work.

For explicitly requested **daily starter messages**, read [the scheduled-start reference](references/scheduled-start.md) and use that mode. It creates brief model replies at user-selected times to try to start a window; it cannot force or guarantee a quota reset. Installation and default recovery activation do not enable this mode.

## Activate

Use for unfinished work the user has authorized to continue. Preserve the current task scope, model, permissions, and newer user instructions. Skill installation alone is not authorization to resume unrelated chats. Do not create wakes merely while explaining, installing, reviewing, or publishing this skill.

Discover native tools by capability; desktop names commonly end in `get_usage_limits` and `automation_update`. Read [the runtime reference](references/runtime.md) before the first scheduling attempt or a scheduled wake. If either capability is missing, save progress and identify the missing capability; automatic continuation is unavailable in that environment. Do not substitute a standalone cron for an in-chat heartbeat.

## Work and arm recovery

1. Check quota at task start, meaningful milestones, and before expensive operations. On long work, check about every 10 minutes when execution allows; from 90% used, check about every 2 minutes and checkpoint after each meaningful step. One indivisible call can cross a threshold; these are cooperative checks, not background monitoring.
2. Prefer `rateLimitsByLimitId` for the bucket applicable to the active model; use legacy `rateLimits` when appropriate. Identify the five-hour window by `windowDurationMins: 300`. `usedPercent` means used, and `resetsAt` is a Unix timestamp in seconds. Do not choose a low-usage bucket just because it permits more work. If attribution or reset time is unknown, report uncertainty rather than inventing a schedule.
3. Save a per-chat checkpoint outside the distributed skill. For sustained work, pre-arm one fallback wake early if reliable reset and scheduling capabilities exist; recheck it at 90%. Target the relevant reset time plus 120 seconds, including any other known quota that blocks this task. Do not calculate reset as “now plus five hours.”
4. Create or update **one owned recovery heartbeat per chat**, preserving unrelated fields and separately authorized daily starter schedules. Inspect an existing automation before reusing it. Confirm the tool's success and returned schedule before recording recovery as armed. An ambiguous timeout requires reconciliation before retrying; never create duplicates blindly. Allow at most one retry after confirmed failure; if unsuccessful, report that recovery is not armed, checkpoint, and continue available work.
5. Keep working after arming. Record successful external operations immediately, then the next step; an uploaded file or created PR must not be recreated after interruption. Do not auto-buy credits, redeem reset vouchers, change billing sources, or switch models to extend quota.

## Wake and continue

Read latest user messages and checkpoint before doing task work. Finished, cancelled, paused, or waiting-for-user tasks must not resume; disable this task's recovery heartbeat. A later explicit user continuation can re-enable it. If work in this chat is still active, do not start a second copy.

Verify actual quota availability. If still blocked, re-arm from the latest reliable blocking reset; avoid rapid retries. A stale checkpoint marked running does not outweigh a newer quota error or actual idle state. Resolve uncertain operations through status identifiers or destination evidence before repeating them. Resume the next unfinished step within the original authorization.

Refresh the checkpoint and next reset for subsequent quota windows. Stop the owned recovery heartbeat when the task ends. If another chat is writing to the same workspace, defer conflicting work; use available state checks and admit uncertainty rather than claiming an atomic cross-chat lock.

Report completion, failures, or required user input. Stay quiet for unchanged/non-actionable wakes; keep notification preferences in the scheduler's supported configuration fields.
