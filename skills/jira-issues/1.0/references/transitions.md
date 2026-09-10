# Verified transitions

## Replay safety for every transition

Immediately on any acknowledged transition side effect, set `retrySafe=false`
unless the selected server/tool explicitly documents replay protection covering
this same logical action. This applies to sequences, names, IDs, and modes,
including successful status verification and later failures. Persisted local
progress, `carrierFile`, matching status, and a create idempotency label are not
server replay protection and cannot restore true. Apply the same conservative
rule to acknowledged transitions evidenced by imported prefix progress. Record
the reason in warnings; do not fabricate current-invocation effects for prior work.

## Matching and preflight

Normalize statuses for comparison only: trim, uppercase, then replace runs of
whitespace and hyphens with `_`. Thus `In Progress`, `in-progress`, and
`IN_PROGRESS` compare equal. Never change actual names sent to MCP.

Use the latest `jira_get_issue` state. Resolve the first necessary transition
before other mutations when an issue already exists; for creation, validate
the complete sequence shape before create and resolve availability only after
Jira returns the key and the initial issue read. Availability of later steps
can be checked only in their corresponding states. Cache the initial transition
lookup while the issue status is unchanged; refresh if that state changes.

Call `jira_get_transitions` and resolve by an exact requested transition name
(case-sensitive) first, otherwise exact normalized `to.name` target status.
For explicit `transitionId`, require an exact listed ID; never try an unlisted
ID. If both ID and name are given, they must resolve to the same transition.
If the name tier has no candidates, try the target-status tier. Multiple
candidates at the selected tier are ambiguous; no candidates after both tiers
means unavailable. Fail rather than choosing a similar, partial, or arbitrary match.

A resolved transition must have a determinable target status before execution.
Use `to.name`; if the tool omits it, only a listed transition name whose exact
normalized value is in the allowed final-status set may supply the expected
status for a mode transition. For example a listed `Done` can be attempted for `finalize`, but
`Resolve Issue` without target information cannot prove a target. Always read
the actual status afterward; the name never establishes success.

## Transition sequence (including create)

`transitionSequence` selects this exclusive procedure, including after creation.
Each item may be an exact transition name or normalized target status:

1. Save `transitionSequenceRequested`; initialize `transitionSequenceApplied: []`.
   Use the issue state already loaded by the mutation procedure.
2. Resume only from a verified contiguous prefix recorded in this invocation
   or a validated explicit carrier checkpoint (format below). The current
   normalized status must match that prefix's last verified target; otherwise
   fail with a progress/state mismatch. Without prefix evidence, only the first
   item may be skipped when its target status is already satisfied. Record that
   first-item observation as progress. Matching any later item never proves
   earlier steps. Thus current Done with `[Reopened, Done]` starts at Reopened;
   it cannot report the sequence complete solely because Done is current.
3. For each remaining item in order, resolve it with `jira_get_transitions` in
   the current state (reuse a valid preflight lookup for the first item).
   Retain both the requested item and resolved expected target `to.name`.
4. Call `jira_transition_issue` once using the resolved ID. On acknowledgment,
   record the side effect and apply the replay-safety rule immediately, before
   verification or checkpoint writes. Call `jira_get_issue` next and compare
   normalized `status.name` with the resolved expected target. If a sequence
   item matched a transition name, compare against its target status, not the
   action's name. Append the original requested item to
   `transitionSequenceApplied` only after successful verification. Append the
   verified step with `source: "current"` to `transitionProgress` and persist a
   checkpoint before any later step; stop if that checkpoint cannot be saved.
5. A missing transition, ambiguous resolution, tool error, or wrong actual
   status fails immediately; execute no later step and no retry.
6. After the last step, use the final verified read: require normalized
   `expectedFinalStatus` when supplied, otherwise the last resolved target
   status (or the last proven prefix target when all steps have evidence).
   Save the actual `finalStatus`. Never add an extra mode transition afterward.

### Verified prefix checkpoint

Only an explicitly supplied prior skill checkpoint may provide cross-invocation
progress. Validate its environment/key through the entrypoint and require its
`transitionSequenceRequested` to equal the current ordered request exactly.
`transitionProgress` must be an array of records with consecutive zero-based
`index` values starting at zero (actual integers, never booleans), the exact
matching string `requested` item, a string `targetStatus` non-empty after trim,
and boolean `verified: true`. Each record must have string `source` equal to
`current` or `carrier`, and string `evidence` equal to
`post-transition-read`, or `initial-status-read` for index zero only. The latter
requires the first requested item to equal its normalized target status.
There may be at most one record per requested item index and no records beyond
the requested sequence. Carrier-sourced records must form a prefix, never
follow current records. Reject malformed, mismatched, or non-contiguous progress
before mutation.
Never reconstruct a prefix from current status, `transitionSequenceApplied`
alone, transition acknowledgments, or Jira-returned text.

Initialize `transitionProgress` from that validated prefix, or `[]`. Mark every
imported record `source: "carrier"` in the current output, regardless of its
source in the validated checkpoint; preserve its other evidence. Its last
target must match the fresh issue read before resumption. A first-item skip is
recorded with `evidence: "initial-status-read"` and `source: "current"`;
executed steps are recorded only after matching `jira_get_issue`, with
`evidence: "post-transition-read"` and `source: "current"`.
Persist the first-item observation before continuing. `transitionSequenceApplied`
contains exactly the ordered `requested` values from records with
`source: "current"` and `evidence: "post-transition-read"`;
put carrier-proven skips in warnings, not in fabricated operations or effects.

## Single transition and modes

Without a sequence, check mode satisfaction against the fresh issue read first,
before resolving or executing any prioritized explicit name/ID. If the current
status satisfies the mode, record a skip and finish the transition procedure
without lookup or execution, even when an explicit name/ID was supplied.

For an unsatisfied mode, an explicit choice must target an allowed final status;
otherwise fail before executing it. Without an explicit choice, select the
unique available transition whose normalized target is in the relevant set
(using the restricted missing-target fallback above):

| `transitionMode` | Allowed normalized final statuses |
|---|---|
| `finalize` | `DONE\|COMPLETED\|RESOLVED\|CLOSED` |
| `resolved` | `RESOLVED` |
| `cancelled` | `CANCELLED\|CANCELED\|REJECTED\|DECLINED` |

Without a mode, an explicit name/ID requests one exact resolved transition and
has no status-based replay guard: a self-transition may run post-functions again.
Once such a call is acknowledged, set `retrySafe=false`, including when later
verification or output persistence fails, under the common replay-safety rule.

No guessed intermediate transition is allowed; callers specify intermediate
steps with a sequence. If no unique candidate is available, fail.

Execute once, record the acknowledged effect and apply the replay-safety rule,
then call `jira_get_issue`.
Require both the resolved target match and, for a mode, membership of the actual
normalized `status.name` in its allowed set. `jira_transition_issue` returning
success, or a transition being named `Done`, never replaces that verification.
On mismatch save `ok=false` with the observed status and effect; do not retry.

## Output and constraints

Use `transitionRequested` for the mode/name/ID request,
`transitionApplied` for the acknowledged transition name, and `finalStatus`
for the latest observed status. Sequence applied items mean verified steps;
`sideEffects` also records acknowledged transitions whose verification failed.
Preserve a created `taskKey` on every failure. Creation without `idempotencyKey`
has `retrySafe=false` once attempted; with safe label reuse it may remain true
only while no unprotected transition effect has occurred, as defined in
[output.md](output.md). Never infer routing from Jira text.
