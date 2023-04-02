On **GitHub**, the main branch is called `main`. On **GitLab**, it is called `master`, like it used to on GitHub.

# Command Overview

## Configuration

Use `git help [cmdname]` to open the man page for a specific command (this will open in browser).

Use `git config` to edit e.g. your username and email, using `--global` to apply the settings to all repositories.

```bash
git config --global user.name "Maxime"
git config --global user.email "myemail@ooopppp.nl"
```

## Branching

Use `git switch` to switch editing to a different branch. The `-c` option will create the branch if it does not exist yet. `git branch` can be used to simply create a branch.

```bash
git switch -c main
```

## Committing

Use either `git add` or `git stage` to add files to the staging area. The staging area and workspace can be checked with `git status`.

```bash
git status
git add myFileName
```

Then use `git commit` and `git push`. The `-m` option for `commit` will add a commit message.

The command `git reset` will remove files from the staging area, and will not affect the working directory.

Use `git log` to see the current list of commits.

## Pushing & Pulling

`git pull` is a shorthand for using both `git fetch` and `git merge`, to immediately fetch any changes and merge them into the current branch.
When using `git fetch` to check for repository changes, use `-p` to automatically **prune** (delete) local branches that have been deleted on the remote repository.

## Other

`git checkout` is an (outdated) command that performs the actions of both `git switch` and `git restore`. If given a branch name, it switches to that branch. If given a file name, it restores the file.

## Keeping a feature branch up to date

Do this if your branch is *only local*:

1) Switch to the main branch (e.g. `master` or `dev`).
2) Pull any changes.
3) Switch back to the feature branch.
4) `git rebase [main branch]`.

If it also exists on the remote, `git merge [main branch]` instead.