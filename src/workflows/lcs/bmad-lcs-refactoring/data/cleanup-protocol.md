# Cleanup Protocol — Worktree & Branch

## Purpose

Standard cleanup procedure to remove a git worktree and its associated temporary branch after a refactoring cycle completes (merge, rollback, or failure). This protocol MUST be executed on every exit path to prevent orphan worktrees and ghost branches.

## Variables (provided by caller)

- **worktree_path:** Path to the git worktree to remove
- **branch_name:** Name of the temporary branch to delete

## Cleanup Sequence

### 1. Remove Worktree

```
git worktree remove {worktree_path} --force
```

- If worktree does not exist (already removed): skip silently
- If removal fails: attempt `git worktree remove {worktree_path}` without `--force` first, then retry with `--force`
- Verify worktree is removed: `git worktree list` should not contain `{worktree_path}`

### 2. Delete Temporary Branch

```
git branch -D {branch_name}
```

- If branch does not exist (already deleted): skip silently
- If branch is the current branch: switch to the main branch first (`git checkout main` or `git checkout master`)
- **NEVER** delete branches that don't match the pattern `lcs-refactor/*`

### 3. Verify Clean State

After cleanup, verify:
- `git worktree list` does not contain the worktree path
- `git branch --list {branch_name}` returns empty
- No leftover lock files in `.git/worktrees/`

### 4. Report

Return cleanup status to caller:
- `cleanup: success` — both worktree and branch removed
- `cleanup: partial` — one of the two failed (report which)
- `cleanup: failed` — both failed (report errors)

## Orphan Detection (for step-01b)

To detect orphan worktrees from interrupted sessions:

```
git worktree list
```

Any worktree matching the pattern `*/.lcs-worktrees/refactor-*` that is not referenced by `currentCycle` in sidecar-state is an orphan.

**Orphan cleanup:**
1. Remove the worktree (same as section 1)
2. Delete the associated branch (same as section 2)
3. Report orphan cleanup to user
