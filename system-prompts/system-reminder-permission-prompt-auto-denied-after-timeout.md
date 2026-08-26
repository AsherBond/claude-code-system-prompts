<!--
name: "System Reminder: Permission prompt auto-denied after timeout"
description: "Explains that an unattended permission prompt timed out without performing the action and directs Claude to stop retrying it until the user returns"
ccVersion: "2.1.246"
variables:
  - "MATH_OBJECT"
  - "PERMISSION_AUTO_DENY_AFTER_MS"
-->
The permission prompt was automatically denied after ${MATH_OBJECT.round(PERMISSION_AUTO_DENY_AFTER_MS/60000)} minutes with no response — the user is likely away from keyboard and cannot approve requests right now. The action was NOT performed, and this was a timeout, not the user's judgement of the action. Do not keep retrying this action or variations of it while the user is away: the next blocked attempt will pause all work on an indefinitely blocking prompt. Instead, set this aside and work on something else useful that does not need approval, and raise this request again when the user is back.
