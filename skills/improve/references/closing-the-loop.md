# Execute, reconcile, or publish plans

## Execute

Read the plan and prerequisites. Confirm the current source still supports the plan's assumptions and preserve unrelated working changes. Implement within the user's scope; ordinary implementation does not require another agent. If delegation is authorized and useful, give each worker clear ownership and the relevant plan context. Use the user's configured model unless explicitly directed otherwise.

Verify observable completion criteria and inspect the diff for unintended behavior, weak tests, and scope drift. Re-run checks after relevant changes or failures, not solely to repeat another agent's successful run. Explain material deviations and update plan status from evidence. Commit, publish, merge, or deploy only within the user's authorization.

## Reconcile

Check the requested plans against current source and execution evidence. Mark completed work, refresh assumptions, record unresolved dependencies, and retire findings fixed elsewhere. A status label is not proof of completion. Preserve useful rationale without rereading every historical plan.

## Publish issues

Publish only when explicitly requested, including an explicit `--issues` invocation. Check for existing issues before creating duplicates. Use the selected repository and existing labels. Review public issue content for sensitive data; ask for a decision only if the proposed publication exceeds the user's authorization or exposes material they have not approved.

Prepare a concise, self-contained body with exact newlines and use structured tool arguments or a body file. Record the returned issue link in the plan. Do not send separate messages to people unless requested.
