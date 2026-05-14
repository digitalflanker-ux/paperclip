# FMA-1366 Repro Note: FMA-56 Mirrored Comment Wake Loop

## Before fix
1. Issue is `blocked` and assigned to an agent.
2. Comment A is posted with blocker context (owner/unblock text).
3. A mirrored Comment B is posted with equivalent semantics (timestamp/UUID-only delta).
4. Both comments trigger `issue_commented` wakeups for the assignee.
5. Result: repeated wake loop with no actionable delta.

## After fix
1. Issue is `blocked` and assigned to an agent.
2. Comment A posts and wakes assignee (normal behavior).
3. Mirrored Comment B posts with equivalent semantic signature and unchanged blocker fingerprint.
4. Route logs `wake_comment_dedup_checked` and `wake_suppressed_duplicate_comment`.
5. Result: duplicate wake is suppressed; assignee is not re-woken.

## Delta cases still expected to wake
- Blocker status/owner-action context changes.
- New credential/access evidence appears in the comment body.
- Non-blocked issues keep prior duplicate-comment wake behavior.
