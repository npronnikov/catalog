---
name: jira-issues
description: >
  Works with JIRA issues through the Atlassian MCP server. All operations
  are performed only through MCP tools. By default, the result and state file
  is jira-task.json (a flow node instruction may override the file name).
  The skill works only with explicitly provided arguments and does not depend
  on specific flows, artifacts, or file names.
---

# Work with JIRA through MCP

## Goal

Perform JIRA issue operations only through Atlassian MCP tools.
Save all results to `jira-task.json` or to the file explicitly specified by the
flow node instruction.

This file is used:
- as the skill execution result;
- as the state carrier between flow steps;
- as the source of `taskKey` for later steps when the flow explicitly uses it.

---

## Skill Contract

The skill works only with explicitly provided arguments.

The skill must not:
- read arbitrary artifacts on its own;
- assume file names, except `jira-task.json` or a file explicitly specified by
  the flow node instruction and used as a state carrier;
- search for data in the runner filesystem;
- derive values from context that was not explicitly passed as arguments or via
  the carrier file.

If a flow wants to use data from an artifact, output parameter, or other source,
the flow itself must extract that data and pass it to the skill as concrete
arguments.

### Execution Model

The skill does not infer the action from flow text or external context.
Operations for the current run are determined only by explicitly provided
arguments and the routing rules below.

Compute the operation set as follows:
- if `jql` is provided, perform issue search through `jira_search` using raw JQL;
- if `project` and `assignee` are provided without `taskKey`, `summary`, or `jql`,
  perform structured search;
- if `taskKey` is provided, work with an existing issue;
- if `taskKey` is not provided but `project` and `summary` are provided, create a new issue;
- if `description` is provided, use it during creation or update it on an existing issue;
- if `additionalFields` is provided, update those fields;
- if `assignee` is provided in a taskKey/creation context, assign the issue;
- if `commentBody` is provided, add a comment;
- if `transitionMode`, `transitionName`, `transitionId`, or `transitionSequence` is provided,
  execute a transition scenario;
- if `labelsToAdd` is provided, add labels without removing existing ones;
- if search mode is active (`jql` or structured search), ignore modifying arguments
  (`description`, `additionalFields`, `commentBody`, `labelsToAdd`, `transitionMode`, etc.)
  and perform only search. In structured search, `project`, `assignee`, and
  `searchStatuses` are search criteria, not issue changes.

If none of `jql`, `project` + `assignee` for search, `taskKey`, or `project` +
`summary` is provided, fail because the target issue cannot be determined.

If only `taskKey` is provided and no modifying argument is provided, the skill must:
1. fetch the issue through `jira_get_issue`;
2. write the current state to `jira-task.json`;
3. finish successfully without side effects.

---

## Arguments

### Required Arguments

- `environment` - JIRA environment: `SIGMA`, `DELTA`, or `SBERTRACK`

### Optional Arguments

- `taskKey` - key of an existing issue; when provided, the skill works with that issue
- `jql` - JQL query for issue search; when provided, execute `jira_search`
- `maxResults` - result limit for search; default is `1`
- `searchStatuses` - comma-separated status list for search, for example `To Do,Reopened,Resolved`;
  used only in structured search mode
- `project` - project key for issue creation or structured search
- `summary` - summary for a new issue
- `description` - description for creation or new description for update
- `assignee` - user to assign the issue to; pass as email
- `commentBody` - ready comment body that must be added without changes
- `transitionMode` - transition mode: `finalize`, `resolved`, or `cancelled`
- `transitionName` - exact transition name to try first
- `transitionId` - exact transition id to try first
- `transitionSequence` - ordered list of target status names or transition names to execute
- `expectedFinalStatus` - expected final status after `transitionSequence`
- `labelsToAdd` - labels to add while preserving existing labels
- `issueType` - issue type during creation; default to `Task` when omitted
- `additionalFields` - additional fields for create/update in the format supported by the MCP tool
- `debug` - when `true`, extended technical details may be saved in `operations[*].result`

### Argument Interpretation

- If `taskKey` is provided, the skill works with an existing issue.
- If `jql` is provided, the skill searches issues through `jira_search` using the JQL as-is.
- If `project` and `assignee` are provided without `taskKey`, `summary`, and `jql`,
  the skill performs structured search: normalizes assignee, builds JQL, and calls `jira_search`.
- If `taskKey` is not provided but `project` and `summary` are provided, the skill creates a new issue.
- If `commentBody` is provided, the comment must be added literally without changes.
- If `transitionMode`, `transitionName`, `transitionId`, or `transitionSequence` is provided,
  a transition scenario must be executed.
- If `labelsToAdd` is provided, labels must be merged with current values without duplicates
  and verified with a repeated `jira_get_issue`.
- If `assignee` is provided outside search mode, the issue must be assigned to that user and verified.
- If both top-level fields and `additionalFields` are provided, top-level fields take priority.
- It is forbidden to duplicate `project`, `summary`, `description`, `issuetype`, or `assignee`
  inside `additionalFields`; on conflict, fail the skill.

---

## Assignee Normalization

If `assignee` is provided, normalize it before use:
1. trim leading and trailing spaces;
2. remove spaces directly before and after `@` when the value looks like an email;
3. use the normalized value for JIRA calls, the output file, and the short result.

Do not insert additional spaces inside the assignee value in summary, result, or error.

---

## MCP Server Selection

Select the MCP server strictly by `environment`:
- `SIGMA` -> Sigma Atlassian
- `DELTA` -> Delta Atlassian
- `SBERTRACK` -> SberTrack

Before selecting the server, normalize `environment`: trim outer spaces and convert
the value to uppercase.

If the value is unsupported or the required server is unavailable, fail and write
that failure to the output file. Do not use another MCP server as fallback:
environments have different URLs and tokens, so mixing environments is forbidden.

Perform all JIRA operations only through MCP tools of the selected server.

---

## MCP Tools

| Task | MCP tool |
|---|---|
| Get one issue details | `jira_get_issue` |
| Search issues by JQL | `jira_search` |
| Get all project issues | `jira_get_project_issues` |
| Create a new issue | `jira_create_issue` |
| Update an issue | `jira_update_issue` |
| Add a comment | `jira_add_comment` |
| Get transitions | `jira_get_transitions` |
| Execute transition | `jira_transition_issue` |
| Download attachments | `jira_download_attachments` |
| Get worklogs | `jira_get_worklog` |
| Get sprints from board | `jira_get_sprints_from_board` |
| Get sprint issues | `jira_get_sprint_issues` |
| Search field list | `jira_search_fields` |
| Get field options | `jira_get_field_options` |

---

## Canonical Rules

### 1. MCP Only

Use only MCP tools for JIRA work.
Do not write integration code.
Do not look up data through side channels.

### 2. Result File

By default, create or update `jira-task.json`.
If the flow node instruction explicitly specifies another file, save to that file.
The "jira-task.json Format" section describes the structure for any output file.

### 3. Task Key Source

Use the task key in this priority order:
1. `taskKey` explicitly provided as an argument
2. `taskKey` from a previously provided carrier file, if the flow explicitly uses it as state

If there is no task key but `project` and `summary` are provided, create an issue.
If there is no task key and creation lacks `project` or `summary`, fail.

### 4. Idempotency

If the task key is already known, do not create a new issue.
Use the existing issue.

### 5. Issue Creation

If `taskKey` is not provided and `project` and `summary` are provided:
1. Use `project`
2. Use `summary`
3. Use `description` if provided
4. Use `issueType` if provided; otherwise use `Task`
5. Use `additionalFields` if provided
6. If `assignee` is provided, try to assign the issue to that user
7. If `commentBody` is provided, add a comment
8. If `transitionMode`, `transitionName`, or `transitionId` is provided, execute the transition after creation
9. After creation, save `taskKey` to the output file

If required creation arguments are missing, fail.

### 6. Assignee Assignment

If `assignee` is provided outside search mode, it is required for the scenario to succeed.

Action order:
1. During issue creation, pass assignee to `jira_create_issue` if the tool supports it
2. After creation or update, always verify the actual assignee through `jira_get_issue`
3. If the assignee was not applied, call `jira_update_issue` as a fallback
4. After fallback, verify the issue through `jira_get_issue` again
5. If the assignee is still missing or the issue remains `Unassigned`, fail immediately
6. Do not treat the scenario as successful even if the issue was created and a comment was added

Do not treat assignee assignment as successful without `jira_get_issue` verification.

### 7. Comment

If `commentBody` is provided, the skill must add it to the issue **strictly without changes**.

`commentBody` is provided in **Markdown**. The mcp-atlassian MCP server automatically
converts Markdown to the native Jira format (Wiki Markup for Server/DC or ADF for Cloud).

Forbidden:
- rephrase `commentBody`;
- change line order;
- add an introduction, description, or summary;
- convert a table into a list or the reverse;
- "improve readability".

### 8. Transition

If `transitionId` is provided, first try to execute transition by `transitionId`.
If `transitionName` is provided, first try to find an exact name match.

#### Transition Sequence

If `transitionSequence` is provided:
1. Fetch the current issue state through `jira_get_issue`.
2. Normalize status names only for comparison: trim, uppercase, and replace whitespace
   sequences and hyphens with `_`. For example, `IN_PROGRESS`, `In Progress`, and
   `in-progress` are treated as the same status. Do not change actual values sent to MCP.
3. If the current status already matches an element of the sequence, start from the next element.
   If it matches the last element, do not execute transitions; go directly to final verification.
4. For each remaining element:
   - call `jira_get_transitions`;
   - first find a transition by exact case-insensitive name;
   - if not found, find a transition whose target status `to.name` exactly matches the element
     case-insensitively;
   - execute the found transition through `jira_transition_issue`;
   - call `jira_get_issue` and verify that the current status matches the expected sequence element.
5. Do not skip elements and do not choose similar transitions by guesswork.
6. If an element is unavailable or the post-transition status does not match, fail and do not
   execute later transitions.
7. After the sequence, verify that the final status equals `expectedFinalStatus` when provided;
   otherwise, verify it equals the last sequence element.
8. Save executed transitions and steps skipped because they were already reached in output.

`transitionSequence` takes priority over `transitionMode`, `transitionName`, and `transitionId`.
Do not run additional transition scenarios after a sequence.

If `transitionMode = finalize`, the skill must:
1. Get transitions through `jira_get_transitions`
2. If `transitionId` or `transitionName` is provided, try it first as the priority option
3. Then search for a final transition by names such as:
   `Done`, `Completed`, `Resolve Issue`, `Resolved`, `Close Issue`
4. If a final transition is found, execute it
5. If not found, try an intermediate transition:
   `Start Progress`, `In Progress`
6. After a successful move to progress, get transitions again
7. Try the final transition again
8. If no final transition is found, fail
9. Do not retry indefinitely

If `transitionMode` is not provided but `transitionId` or `transitionName` is provided,
execute exactly one attempt for the requested transition.

If `transitionMode = resolved`, the skill must:
1. Get transitions through `jira_get_transitions`
2. If `transitionId` or `transitionName` is provided, try it first as the priority option
3. Search for a transition leading to status `Resolved`:
   a. Priority 1 - by target status (`to`): a transition whose target status is `Resolved`
   b. Priority 2 - by transition name: `Resolve Issue`, `Resolved`
4. If found, execute it
5. If not found, try an intermediate transition:
   `Start Progress`, `In Progress`
6. After a successful intermediate transition, get transitions again
7. Repeat search by priorities 3a and 3b
8. If found, execute it
9. If not found, fail
10. After executing the transition, verify with `jira_get_issue` that `status.name` is `Resolved`
11. If status is not `Resolved`, fail
12. Do not retry indefinitely

If `transitionMode = cancelled`, the skill must:
1. Get transitions through `jira_get_transitions`
2. If `transitionId` or `transitionName` is provided, try it first as the priority option
3. Search for cancel transitions by names such as:
   `Cancelled`, `Canceled`, `Reject`, `Rejected`, `Decline`, `Declined`, `Close Issue`
4. If found, execute it
5. If not found, try an intermediate transition:
   `Start Progress`, `In Progress`
6. After a successful intermediate transition, get transitions again
7. Try the cancel transition again
8. If no cancel transition is found, fail
9. Do not retry indefinitely

### 9. Existing Issue Update

If `taskKey` is provided:
1. First call `jira_get_issue`
2. Then perform required actions based on provided arguments:
   - `description` -> `jira_update_issue`
   - `additionalFields` -> `jira_update_issue`
   - `labelsToAdd` -> merge with current labels, update through `jira_update_issue`, then verify through `jira_get_issue`
   - `assignee` -> verify and apply fallback update if needed
   - `commentBody` -> `jira_add_comment`
   - `transitionSequence` / `transitionMode` / `transitionName` / `transitionId` -> transition scenario
3. After changes, verify the issue again if assignee, fields, or status changed

For `labelsToAdd`:
- preserve all existing labels;
- add only missing values;
- compare labels case-sensitively;
- do not delete or rename existing labels;
- the scenario succeeds only if all requested labels are present after update.

### 10. Operation Order

Operations run in this order:
1. select MCP server;
2. determine or create `taskKey`;
3. get initial issue state when working with an existing issue;
4. update fields;
5. add and verify `labelsToAdd`;
6. verify and apply assignee if needed;
7. add comment;
8. execute transition or `transitionSequence`;
9. get final issue state;
10. save the output file.

### 11. Success Criteria

The scenario is successful only if all mandatory requirements for the current run are met:
- issue was created or found;
- if `assignee` was provided outside search mode, it is actually assigned;
- if `commentBody` was provided, the comment was successfully added;
- if `description` or `additionalFields` was provided, the changes were applied;
- if `labelsToAdd` was provided, all labels are present and existing labels are preserved;
- if `transitionSequence` was provided, all required transitions were executed in order and the final status was verified;
- if `transitionMode`, `transitionName`, or `transitionId` was provided, the transition was executed or correctly failed according to skill rules.

If at least one mandatory condition is not met, set `"ok": false`.

### 12. Errors

Always save the output file on any error.
Do not invent a missing task key.
If execution is impossible without task key, project, summary, comment, transition, or valid assignee,
record this explicitly in `error`.

### 13. Observability

Add to `operations` only actions that were actually executed and only in execution order.
Each operation must contain a short normalized result.
Raw detailed MCP responses may be saved only when `debug = true`.

### 14. Issue Search

#### Mode 1: Raw JQL (`jql` argument)

If `jql` is provided:
1. Select MCP server by `environment`
2. Call `jira_search` with the provided JQL and `maxResults` (default `1`)
3. If there are no results, save `{"ok": true, "totalResults": 0, "issues": []}`,
   unless the flow node instruction requires exactly one issue
4. If results exist, save the `issues` array with attributes for each issue
   (key, summary, description, status, assignee, labels, priority, issuetype, custom fields)
5. When `maxResults = 1`, also write the first result to top-level fields
   (`taskKey`, `summary`, `description`, `status`, `assignee`, etc.) for compatibility
   with the rest of the skill rules
6. If the flow node instruction requires full issue data or comments, after selecting
   the single issue call `jira_get_issue` and enrich top-level fields and `issues[0]`
   with current `summary`, `description`, and `comments`. Save each found comment with
   at least `author`, `body`, and `created`. If there are no comments, save `comments: []`;
   this is not an error.
7. Do not perform modifying operations (creation, update, comment addition, transition) in search mode
8. If the flow node instruction explicitly requires exactly one issue and `totalResults != 1`,
   save `"ok": false`, fill `error`, and do not fill top-level `taskKey`

#### Mode 2: Structured Search (`project` + `assignee`, without `taskKey`, `summary`, and `jql`)

If `project` and `assignee` are provided without `taskKey`, `summary`, and `jql`:
1. Select MCP server by `environment`
2. Normalize `assignee` by the standard rules (trim leading/trailing spaces and remove
   spaces before and after `@`)
3. Build JQL:
   - `project = {project} AND assignee = "{normalizedAssignee}"`
   - If `searchStatuses` is provided and non-empty after trim, split by comma,
     trim each item, drop empty items, and add:
     `AND status in ("status1", "status2", ...)`
   - If `searchStatuses` is not provided or empty, do not add a status filter
   - `ORDER BY created ASC`
4. Call `jira_search` with the constructed JQL and `maxResults` (default `1`)
5. Save the result according to Mode 1 rules (steps 3-8)

***

## jira-task.json Format

Save the result to `jira-task.json` or to the file explicitly specified by the
flow node instruction using this structure:

```json
{
  "ok": true,
  "environment": "DELTA",
  "project": "TEST128",
  "assigneeRequested": "user@example.com",
  "assigneeApplied": "user@example.com",
  "taskKey": "TEST128-20",
  "summary": "Clarifying questions need answers",
  "description": "http://localhost:5173/#/run-console?runId=123",
  "labelsAdded": ["AIFIXED"],
  "transitionSequenceRequested": ["IN_PROGRESS", "Resolved"],
  "transitionSequenceApplied": ["IN_PROGRESS", "Resolved"],
  "comments": [
    {
      "author": "user@example.com",
      "body": "Clarification about expected behavior",
      "created": "2026-05-29T07:30:00Z"
    }
  ],
  "commentBodyExact": "h2. Questions to answer\n\n# *Question 1*\n** Option A\n** Option B",
  "commentAdded": true,
  "transitionRequested": "finalize",
  "transitionApplied": "Done",
  "finalStatus": "Done",
  "warnings": [],
  "startedAt": "2026-05-29T08:00:00Z",
  "finishedAt": "2026-05-29T08:00:03Z",
  "skillVersion": "1.0",
  "operations": [
    {
      "type": "create_issue",
      "ok": true,
      "result": {
        "taskKey": "TEST128-20"
      }
    },
    {
      "type": "get_issue",
      "ok": true,
      "result": {
        "status": "To Do",
        "assignee": "user@example.com"
      }
    },
    {
      "type": "add_comment",
      "ok": true,
      "result": {
        "added": true
      }
    }
  ],
  "error": ""
}
```

### Field Rules

- `ok`: overall execution result
- `environment`: environment used
- `project`: project key if provided or determined
- `assigneeRequested`: normalized assignee that was requested
- `assigneeApplied`: assignee that is actually set
- `taskKey`: issue key when known
- `summary`: issue summary when applicable
- `description`: issue description when applicable
- `issueType`: issue type from `issuetype.name` when applicable
- `labelsAdded`: labels that were added and confirmed
- `transitionSequenceRequested`: requested transition sequence
- `transitionSequenceApplied`: actually executed transitions; steps already reached may be noted as separate objects in `operations`
- `comments`: issue comments with `author`, `body`, and `created` when requested
- `commentBodyExact`: exact comment body that was sent
- `commentAdded`: `true` only when the comment was successfully added
- `transitionRequested`: original transition request when present
- `transitionApplied`: executed transition name or empty string
- `finalStatus`: final issue status when it can be determined
- `warnings`: non-fatal warning list; may be empty on full success
- `startedAt`: execution start time in ISO 8601
- `finishedAt`: execution finish time in ISO 8601
- `skillVersion`: skill contract version
- `operations`: only actions actually executed, in execution order
- `operations[*].result`: short normalized operation result; full raw response is allowed only when `debug = true`
- `error`: empty string on success; otherwise a clear error description
- `searchMode`: `true` when the run was in search mode (raw JQL or structured search)
- `jql`: original JQL query when search was performed
- `totalResults`: total number of issues found during search
- `maxResults`: result limit used during search
- `issues`: array of found issues with full attributes (search mode only)
