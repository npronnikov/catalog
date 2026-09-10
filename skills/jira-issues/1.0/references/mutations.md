# Create and existing-issue procedures

## Preflight and ordering

Complete all shared argument, conflict, path, sequence-shape, and selected-tool
capability checks before mutation. Fail if required payload fields or their
verification cannot be represented by the selected MCP tools. Never silently
drop an unsupported field. Perform all read-only checks possible with the
known issue and current state before the first mutation: load current fields,
labels, and comments if adding a comment; resolve the next transition if
requested. Later sequence availability depends on later states; validate each
step immediately before executing it. Do not demand a created key before create.

Order: resolve/create key and persist it; load issue; preflight available
transitions; apply and verify fields/labels/assignee; deduplicate/add comment;
execute and verify transitions; finalize output. Reuse current verified reads;
do not add redundant final reads when the last read already verifies all
requested field/status changes. A read-only existing request makes one
`jira_get_issue` call (additional comment pages only if requested).

## Creation and idempotency

For `operationMode=create`:

1. With `idempotencyKey=KEY`, use the exact label `ai-idem-KEY`. Call
   `jira_search` with `project = "PROJECT" AND labels = "ai-idem-KEY"`,
   `maxResults: 2`; encode both literals with the structured encoder in
   [search.md](search.md). Use the authoritative total, not page length.
   Zero matches permit creation; exactly one valid matching issue is reused;
   multiple matches or unknown/inconsistent total fail without mutation.
2. On reuse, persist the returned key, keep `operationMode=create`, and put the
   reuse decision in that search operation's normalized result and a warning.
   Never call `jira_create_issue`. Read that issue and verify requested creation
   fields; fail if they disagree rather than silently creating or rewriting a
   different issue. Continue only explicitly requested remaining operations.
3. On zero matches, or without an idempotency key, call `jira_create_issue`
   once with `project`, `summary`, optional original `description`, `issueType`
   (default `Task`), and conflict-free `additionalFields`. Merge creation labels
   with `labelsToAdd` and the idempotency label without dropping any supplied
   label. The idempotency label must be included in the create payload itself;
   if unsupported, fail before create. Never add it in a later update.
4. Immediately when create returns, record the created issue in `sideEffects`,
   capture and validate Jira's returned `taskKey`, and persist the output
   checkpoint with the key before any subsequent read or transition. If Jira
   acknowledges creation without a usable key, record the acknowledged creation
   without inventing a key and fail. Do not start a second create.
5. Call `jira_get_issue` to verify creation fields and resolve remaining
   operations, including a requested `transitionSequence`. If the first
   transition is unavailable, stop with the saved key and created side effect.

An existing `taskKey` never permits creation. `idempotencyKey` applies to create
selection only; all callers must reuse it unchanged for the same logical create.
This label protocol is sequential deduplication, not an atomic uniqueness lock:
concurrent same-key invocations or search-index lag require reconciliation.

## Fields, labels, and assignee

Read the current issue first, requesting fields needed to verify every supplied
field. For existing issues, update explicit `summary`, `description`,
`issueType`, `additionalFields`, and `assignee` only where they differ from the
requested values. Compare canonical identifiers for structured fields; preserve
literal text content. If a requested value cannot be compared reliably, fail.

For `labelsToAdd`, calculate a case-sensitive set union of original labels and
requested labels. An empty list is a no-op. Submit only missing changes through
`jira_update_issue`. After any field/label update, call `jira_get_issue`; verify
every requested field, every requested label, and preservation of every original
label. Set `labelsAdded` only to newly added, confirmed values. Do not replace
labels with only the requested additions.

Normalize assignee as in the entrypoint and compare Jira's canonical user
identity, not display-name similarity. If an assignment attempt was not applied,
allow one corrective `jira_update_issue`, then `jira_get_issue`. If still wrong
or Unassigned, fail and stop further operations. Do not retry a failed MCP
mutation; this correction applies only to a successful call with missing assignment.

Every field mutation requires a read verification. Successful creation also
requires a read; an acknowledgment alone does not establish field correctness.

## Comments

Before `jira_add_comment`, load all existing comment bodies via the initial
`jira_get_issue` comments data and necessary comment pagination. If the read is
incomplete or bodies cannot be compared, fail rather than assuming no duplicate.
Preflight must establish that the selected server returns comparable string
bodies after its native conversion. If it exposes only structured/native
objects, cannot establish a comparable string representation from tool
capabilities or current reads, or any loaded body is non-string, fail before
add (and before other mutations when discovered in preflight). Never flatten
objects or guess Markdown/native equivalence.
The comparison function only replaces CRLF and CR with LF; all other characters
and whitespace remain unchanged. Compare that representation of each existing
string body with the same representation of explicit `commentReadbackBody`.
This argument is the caller-declared expected string from comment responses or
`jira_get_issue` after the selected MCP server's native conversion; it need not
equal the outbound Markdown `commentBody`.

If a match exists and `allowDuplicateComment=false`, skip the add call, set
`commentAdded=false`, `commentPresent=true`, and record a deduplication warning.
Otherwise call
`jira_add_comment` once with the exact original UTF-8 `commentBody` bytes.
Comparison normalization must never change the payload: no trimming, newline
conversion, Markdown transformation, prefix, suffix, or rephrasing. Transport
JSON escaping is allowed only if decoding preserves those bytes. Let the MCP
server perform its native Jira format conversion.

Set `commentBodyExact` to the original outbound body and
`commentReadbackBodyExact` to the original expected read-back string. Set
`commentAdded=true` only after an acknowledged successful add; record its side
effect and set `retrySafe=false` immediately, before verification or persistence.
Verify the returned string body against `commentReadbackBody` using the
comparison function above; only then set `commentPresent=true`. If the
successful response omits a body, read the issue/comments (including necessary
pagination) and verify an exact match. A non-string or mismatching returned
body, failed read, incomplete comment data, or no matching body fails without
retrying the add; retain `commentAdded=true`, `commentPresent=false`,
`retrySafe=false`, and the acknowledged side effect.
No extra read is needed when the add response already verifies the body.
Only verified presence with `allowDuplicateComment=false` may clear this
comment's pending-verification retry barrier; restore `retrySafe=true` only
if no other unsafe condition has occurred. An unverified or unknown outcome
never restores true. Existing transition/create/unknown-effect restrictions
still win over successful comment verification.
An uncertain comment response stops the invocation and is handled by
[output.md](output.md); never claim verified presence from an acknowledgment alone.

## Constraints

No automatic whole-invocation retries or rollback. Stop at the first failure,
persist known keys and all completed/uncertain effects, and calculate retry
safety using [output.md](output.md). An unavailable post-create transition never
erases the creation, its key, or its effect record.
