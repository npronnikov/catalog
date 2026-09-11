# Flow Authoring Notes

## FTL in `instruction` for AI nodes

AI node `instruction` supports FreeMarker Template Language (FTL). Use it when a later node depends on data from previous `step-summary.json` outputs.

- Context shape:
  - `step.<nodeId>.<jsonPath>`
  - `run.id`, `run.flow`, `run.target_branch`, `run.feature_request`, `run.created_by`
- Source of values: latest `SUCCEEDED` attempt of the node in the same run
- Missing values fail in strict mode with `TEMPLATE_VAR_UNRESOLVED`

Example:

```yaml
instruction: |
  <#if step.ai_review.verdict == "rework">
  Fix: ${step.ai_review.rework_instruction!"(empty)"}
  <#else>
  Proceed.
  </#if>
```

Use guard operators for optional fields:

- `value??` checks existence
- `${value!"default"}` provides fallback

Recommended pattern:

```ftl
${step.ai_review.rework_instruction!"No rework notes provided"}
```

## Repository aliases and runtime paths

Repository aliases are safe folder names and are unique within a project. Existing projects may use the legacy alias `main`, but `main` is not required for new projects.

Single-repository flows use ordinary project paths in flow YAML. At run creation, the runtime requires exactly one selected repository and stores snapshot paths as `<selectedAlias>/path`.

Multirepository flows use `alias:path` in flow YAML. At run creation, flow aliases are mapped to selected project repository aliases and stored in the run snapshot as `<selectedAlias>/path`.
