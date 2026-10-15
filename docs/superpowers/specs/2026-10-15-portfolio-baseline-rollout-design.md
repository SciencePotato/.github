# SciencePotato Portfolio Baseline Rollout

## Scope

- Target the 55 active, owned, non-fork repositories visible to `SciencePotato`.
- Do not modify the four forks.
- Update repository descriptions, topics, and justified homepage links directly through GitHub because metadata changes cannot use pull requests.
- Create file-change pull requests only where the repository benefits from a concrete baseline addition.
- Append new pull requests behind the existing punctuation and copy queue.

## Repository Changes

### Shared defaults

Create a public `SciencePotato/.github` repository containing default community health files and templates for public repositories. Keep security reporting generic and direct reporters to private GitHub security advisories rather than publishing contact details.

### Universal metadata

Give every repository a concise description and relevant topics derived from its README, language, manifests, and current status. Add a homepage only when a verified live site, package page, or documentation page already exists.

### File baselines

- Preserve existing README content and add only missing purpose, status, setup, validation, or deployment information that can be verified from the repository.
- Add `.editorconfig` to code repositories that lack one.
- Add `.gitignore` only when the stack produces generated files or uses local secrets.
- Add Dependabot only where a supported package manifest exists, matching the repository's actual package manager and target branch.
- Add CI only when the repository exposes real install, lint, type-check, test, or build commands. Never create a workflow that claims tests pass when no test exists.
- Add `.env.example` only when environment-variable names can be derived without copying secret values.
- Do not add licenses automatically.
- Historical or educational repositories receive lightweight status documentation, not product-grade ceremony.

## Pull Request Model

- Use one baseline pull request per repository, with a unique `automation/portfolio-baseline-2026` branch.
- Base each pull request on that repository's actual default branch.
- Validate changed files locally before pushing and record repository-specific validation commands in the merge queue.
- Existing campaign pull requests remain ahead of the new entries.
- The daemon may complete at most one successful squash merge per local calendar day.

## Conflict Remediation

When a queued pull request reports a conflicting merge state:

1. Attempt remediation only once per repository, PR number, base SHA, and head SHA tuple.
2. Clone the repository into an ephemeral daemon cache and check out the pull request head branch.
3. Merge the latest target branch into the pull request branch without rebasing or force-pushing.
4. Capture the exact conflicted file list and refuse automated remediation when it includes workflows, agent instructions, MCP configuration, dependency lockfiles, infrastructure, migrations, secrets, generated assets, or other protected control surfaces.
5. Invoke GitHub Copilot CLI programmatically with sandboxing enabled, remote export and built-in MCP disabled, no custom instructions, no user questions, no shell tool, and only local read/search/edit tools. The prompt permits edits only to the captured conflicted files.
6. Reject the result if conflict markers remain, unmerged paths remain, non-conflicted files were changed by the agent, protected files changed, or validation fails.
7. Commit the merge resolution and push normally to the existing pull request branch. Never force-push.
8. Re-query GitHub. Merge only when the exact repaired head SHA is open, non-draft, clean, mergeable, and all checks are successful or neutral.
9. Persist the attempt and outcome atomically. Failed or ambiguous remediation is escalated in logs and is not retried for the same SHA tuple.

The Copilot process never receives credentials through its prompt or logs and never performs GitHub writes. The daemon owns clone, fetch, commit, push, status verification, and merge operations.

## Failure Handling

- Authentication, clone, agent, validation, push, and GitHub API failures leave the pull request open.
- A failed conflict repair does not consume the day's successful-merge allowance.
- A successful repair with pending checks remains queued for a future run.
- State uses a lock plus atomic file replacement, preserving the existing crash-recovery model.
- Logs include repository, pull request, SHA tuple, decision, and validation result, but no tokens or file contents.

## Validation

- Unit-test queue validation, conflict-attempt identity, protected-path matching, state recovery, and Copilot command construction.
- Use local throwaway Git repositories to test clean merges, resolvable conflicts, protected conflicts, agent failure, validation failure, non-conflict edits, push failure, and successful repair.
- Run the production daemon in dry-run mode and verify no GitHub or state mutation.
- Load the updated LaunchAgent without kickstarting it and confirm the registered 10:00 AM schedule.

## Rollout Order

1. Create shared defaults and metadata.
2. Generate and validate baseline pull requests in small stack-aware batches.
3. Extend the queue only with verified open pull requests.
4. Install and authenticate GitHub Copilot CLI.
5. Add conflict remediation and pass local integration tests.
6. Reload launchd without an immediate production run.