# Git

## Version Control

- Track changes of files(blobs) as **snapshots**
- Checkpoint / Tracking
- Collaborative Development

## Git Graph

- DAG of Commits
- HEAD pointer: currently looking commit or branch
    - usually points branch
    - Detached HEAD: HEAD pointing commit

## Commit

- Snapshot of the project
- Metadata: author, hash, message, etc.

## Staging

- Intermediary before commit

## 3 States of (Tracked) Files

- Modified
- Staged
- Committed

## Checkout / Switch

- Change HEAD to specified commit/branch
- Checkout: both commit & branch / switch: `-d` option for commit

## Stashing

- **Save / Restore** modification on / from stack
- `git stash` / `git restore`

## Reset (Delete) / Revert

- Reset: deletes commit from DAG
- Revert: **add commit** that applies **reverse patch**
    - `git revert <commit-id>`

## Branch

- Pointer to commit
- When creating new commit: **HEAD** and **branch pointed by HEAD** points new commit

### Creating Branch

- `git switch -c <branch>` / `git checkout -b <branch>`: create branch + checkout
- `git branch <branch>`: no checkout

## Merge

- Integrate two branches by creating **merge commit**
- By default, **Fast-Forward** (`—-no-ff` for always create merge commit)

## Rebase

- Extract **patch** from current branch
- Delete commits
- Re-apply patches to base branch
- Simple **linear history**
- Delete original commit ⇒ **metadata changes**

## Remote

- Remote repository in git host
- Local could also be host

## Remote Commands

- Clone: Copy from remote to local
- Push: Reflect changes on the branch to remote
- Fetch: Download changes from remote
- **Pull: Fetch then merge**
- **Blame: Show last modified commit and author for each line**

## Collaboration w/ Git

- Concise Commits & Branch handles single feature
- Good commit messages
- Good issues
- Git Branching Strategies
