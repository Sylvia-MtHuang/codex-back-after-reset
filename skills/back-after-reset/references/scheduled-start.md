# Daily starter messages

Use only after the user requests scheduled messages to try to start a five-hour quota window. This mode runs a brief model turn even without unfinished work, consuming some quota. It cannot force a reset, move an existing window, or guarantee that the backend starts a new window. Treat returned quota data as observation, not proof that this message caused a reset.

## Configure

Use native chat-attached heartbeat scheduling, discovering the current schema before mutations. Inspect existing automations to avoid duplicates. Keep starter ownership separate from task-recovery ownership: retain purpose, chat id, timezone, time and confirmed automation id in a private workspace note or this chat, outside the distributed skill. Do not create a set in every unfinished chat; chats share account quota. A daily starter does not authorize resuming other chats.

Use the user's chosen daily times: one time or any set of times, not a fixed four-slot timetable. If no times are supplied or established by the user, ask for them before scheduling. Preserve exact requested times; add or accumulate a buffer only when requested.

Default to the user's local timezone. Read the Codex host's system timezone and verify that it represents the user's local time; confirm the resolved timezone with the saved schedule. If user context and host timezone disagree, or local time cannot be established, clarify which zone to use. An explicitly requested timezone takes precedence. Installing this skill does not choose times or enable schedules.

An optional buffered example is:

| Local time | Offset from nominal slot |
|---|---|
| 05:00 | 0 minutes |
| 10:02 | 2 minutes |
| 15:04 | 4 minutes |
| 20:06 | 6 minutes |

These adjacent daytime slots are five hours and two minutes apart; they are examples, not defaults. Use local wall-clock scheduling through daylight-saving changes. Confirm the scheduler's effective timezone through supported metadata or its documented use of the host's verified local timezone. Writing a timezone in the prompt alone does not configure the scheduler. If the scheduler cannot honor the chosen zone, report the mismatch before creating an incorrect schedule; do not alter the system timezone.

Use one daily heartbeat per selected hour/minute pair unless the scheduler explicitly supports an exact multi-time set. For example, 08:30 and 19:45 require two schedules; the buffered table requires four. A recurrence listing multiple hours and multiple minutes creates a cross product, not paired times. Confirm each successful creation/update and the saved schedule; report partial success precisely. Reconcile ambiguous results before retrying, with at most one retry after confirmed failure. Never overwrite an unrelated greeting or recovery automation.

## On each starter wake

Read later user instructions concerning these starter schedules. A request to pause or cancel starters disables the owned starter set through the supported tool. Completing or pausing an ordinary work task only disables that task's recovery; daily starters persist until the user stops them. Do not reopen finished work or continue task work merely because this starter fired.

Generate one brief reply. If quota-reading is available, check the applicable five-hour bucket and include its observed reset time, converted to the chosen timezone. Otherwise say reset is unverified. A successful turn does not prove a fresh window. If known quota is still blocked, report that observation without claiming activation; leave the daily timetable intact and avoid added retry wakes. Unfinished work's separately authorized recovery uses the actual blocking reset plus 120 seconds. When quota prevents the turn from starting, it cannot inspect or repair itself.

Keep the starter prompt short and durable, for example:

> Use $back-after-reset in daily starter mode. This is a brief scheduled message to try to start a quota window, not a work-continuation request. Follow later instructions about pausing or cancelling these starter schedules. Check quota if available and reply with one short sentence containing the observed five-hour reset time in [chosen timezone], or say it is unverified. Do not claim a new window was guaranteed. Keep this daily schedule after ordinary tasks finish. Owned purpose: daily starter at [local time], in this original chat.

Substitute verified values before scheduling. Set notification preferences through supported scheduler fields. Local wakes require the computer and app to remain running; sleep, app exit, network failure or quota can delay or prevent the turn. Report schedule configuration separately from actual execution and quota-window behavior.
