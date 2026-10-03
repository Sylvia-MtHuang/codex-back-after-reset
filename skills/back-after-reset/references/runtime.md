# Desktop runtime contract

## Capability checks

Native Codex desktop capabilities are required for automatic recovery. In the author's environment these are `mcp__codex_app__get_usage_limits` and `mcp__codex_app__automation_update`; other installations may expose different names or omit them. Discover and inspect the current tool definitions rather than copying an assumed schema.

The quota tool is read-only. Prefer `rateLimitsByLimitId` where applicable and determine the bucket for the active model. Legacy `rateLimits` may be the applicable single-bucket representation. Missing values are unknown, not zero. For the five-hour window, `windowDurationMins` is 300; timestamps in `resetsAt` use seconds. Do not persist a full response containing account details when a percentage and reset suffice.

Use the actual availability flags and relevant blocking limits. A five-hour reset is insufficient when a known weekly, model-specific, or spend limit also blocks work. For several known time-reset blockers, target the latest reset plus 120 seconds. Unknown or non-time-reset blockers require reporting the blocker, not fabricating a future recovery time. Do not spend credits or redeem vouchers automatically.

## Heartbeat lifecycle

Use an automation attached to the original chat so its context is retained. Create through the supported tool, never by editing internal automation storage or emitting handwritten automation directives. Where the current schema supports them, select `kind: heartbeat`, a thread destination, and the target chat. Do not invent update/delete/status fields; inspect current documentation for each mutation. Preserve existing fields unless changing this skill's schedule or prompt requires otherwise.

Use a recognizable name such as `Back After Reset — <chat title>` plus the actual chat identifier in the durable prompt and checkpoint. Names alone are insufficient proof of ownership. Before creation or retry, use available inspection tools to locate an existing owned automation. A tool timeout is an unknown outcome: verify ownership, id and schedule before retrying. Do not overwrite another automation because its title happens to match.

For long work, pre-arm a fallback at a reliably known reset even before 90%, since an expensive call can use the remaining quota before another check. At 90% or above, refresh the checkpoint and confirm the wake remains armed. After reset, if work continues, query and update the next reset; the former timestamp does not describe the new window.

Prefer a supported next-fire/start-time facility. When only interval scheduling exists, calculate a supported interval to place the next run at or after the target, confirm the returned next-run time if available, and include `not_before_utc` in the prompt. If scheduling cannot be anchored or its next fire cannot be established, state that precision is unverified. A recurring heartbeat may serve as a fallback only with a supported low-frequency cadence, a due-time check, and clear cleanup; explain the delayed/best-effort behavior.

Time gating in the prompt prevents premature task work; it does not prevent the wake itself using quota. For an unanchored recurring fallback, use a supported interval of at least 60 minutes unless the user explicitly accepts a shorter cadence; if no such interval is supported, report that this fallback cannot be arranged. A supported and verified first-fire delay to the actual reset can be shorter. Avoid repeated wakes while exhausted. A heartbeat shares account limits with ordinary work and may fail to start while exhausted. Do not claim it can query or repair itself in that state.

Keep the checkpoint synchronized with confirmed scheduler results. If scheduling definitively fails, retry at most once for a recoverable cause. Otherwise report the failure and continue available authorized work; never claim recovery is arranged. Unknown outcomes remain unconfirmed until reconciled, without blind retries.

On completion, cancellation, pause, or required user input, use the supported tool to disable the owned heartbeat. Cancellation of one task must not disable other tasks. If disable fails, record it, report it, and ensure the saved prompt still gates task execution against current user instructions. Do not archive the chat unless requested.

## Per-chat checkpoint

Prefer `.back-after-reset/<chat-id>.md` in an allowed writable workspace. Use the actual known chat id; if unavailable, use a stable locally assigned id and explicitly record the limitation. Keep checkpoints out of public commits (add the directory to the workspace's ignore configuration when authorized). Do not modify the installed skill directory to save task progress. If no writable location exists, put a compact checkpoint in the original chat and link it in the wake prompt.

Use this contract, omitting only unavailable optional identifiers:

```text
chat_id / current task identity:
goal and latest constraints:
status: working | waiting-quota | waiting-user | paused | cancelled | done
completed work and evidence:
remaining work / next step:
workspace and relevant files:
in-flight operations and status identifiers:
confirmed external results (URLs/ids; never credentials):
last_checkpoint_utc:
quota_bucket / observed_used_percent / reset_utc:
automation_id / confirmed_schedule / not_before_utc:
```

Write atomically when normal file tools support it, and checkpoint immediately after external side effects. Avoid unnecessary entire-history copies, credentials and personal account metadata. Redact signed URLs, token-bearing query parameters and unnecessary private identifiers; store a safe operation id or redacted destination instead. Before committing or sharing a workspace containing checkpoints, inspect the actual publication file set (including staged files/diffs or archive contents, as applicable) and exclude these private records unless the user specifically authorized their disclosure. A snapshot marked working is not evidence of an active turn; inspect actual state and newer messages when recovering.

## Durable wake prompt

Adapt the following with verified chat identity, checkpoint location and a supported schedule. Keep the text readable; no raw automation directives are needed.

> Use $back-after-reset in this original chat. Resume only the user's still-authorized unfinished task identified by this checkpoint: [verified checkpoint location or chat note]. Read newer user messages before acting. Respect later changes, cancellation, pause and pending questions. If finished or waiting for the user, disable this owned heartbeat. If another turn is active, do not start duplicate work. Do no task work before [not_before_utc]; recheck actual quota after that time. If quota still blocks execution, re-arm from a reliable blocking reset and avoid rapid retries. Verify uncertain operations before repeating them. Continue the next unfinished step, checkpoint meaningful progress, and at 90% used confirm or update recovery while continuing work until completion or actual quota exhaustion. Keep this checkpoint current and disable this owned heartbeat when the task ends. Owned automation: [confirmed automation id, if known].

Bracketed fields are adaptation slots in this example, not values to pass literally. After creating an automation, update the checkpoint with its confirmed id; enrich the prompt with that id only if the supported update mechanism allows it.

## Global activation and existing chats

A personal skill can be selected across projects. It does not run a daemon, guarantee invocation, or automatically migrate old chats. An optional user-level AGENTS rule can require loading it during authorized task execution; see the repository README for an example.

With explicit user authorization for all unfinished tasks, use available chat list/read tools to register suitable existing chats. Idle, needs-attention, pinned, or unread status alone does not prove an unfinished authorized objective. Inspect latest messages. Do not message or schedule other chats unless the user's instruction authorizes that scope. If the scheduler cannot target another chat, report that it is not covered.

Chats share account quota. Stagger recovery when scheduling permits. Avoid simultaneous writes to the same workspace; state checks are not a guaranteed lock. Computer sleep, shutdown, app exit, missing tools, inaccessible files and network failure can prevent local wakes. Do not alter power, security, or billing settings to compensate.

## Sources and validation boundary

- [Scheduled tasks and original-chat continuation](https://learn.chatgpt.com/docs/automations#schedule-a-task-inside-a-chat)
- [Skill format and activation](https://learn.chatgpt.com/docs/build-skills)

Instruction checks and simulated scenarios do not prove a live exhaustion-and-reset cycle. Report actual verification levels separately: file/format checks, independent scenario evaluation, real scheduling smoke test if performed, and real quota-exhaustion recovery if observed.
