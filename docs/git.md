## What is Git?

- Git is a **distributed version control system** — each person has a full copy of the repository on their own computer.
- Git allows multiple people to work on the same project **without needing to be constantly connected to each other or to a central server.**
- Git keeps **track of the entire history of changes**, making it easy to **go back to previous versions** if needed.
- **Repository**: Stores the full history of commits and tracks all changes made to the codebase.

## Using Git

- The command line
- Code editors & IDEs (e.g., VS Code)
- Graphical user interfaces ([GUI tools](#gui-tools)) like Git Cola, gitk, GitKraken, SourceTree, etc.

## Git Workflow

- **🗂️ Working directory**: Your local workspace where you make changes directly to files.
- **🗒️ Staging area (Index)**: A middle layer that holds changes you've prepared to commit. You can selectively add or remove changes before committing, giving you precise control over what gets saved to version history.
- **💻 Local repository**: A version of the repository stored on your local machine. It contains the full history of commits and allows you to work offline.
- **🌐 Remote repository**: A version of the repository hosted on a server (e.g., GitHub, GitLab, Bitbucket). It enables collaboration by letting multiple people clone the code, push their changes, and pull updates from others.

![Git Space](/lib/images/gitWorkflow.png)

## Git Branches

Git has 3 main branch layers:

| Level | Description |
| ---------------------------- | --------------------------------------------------------------------------- |
| 🌐 **Remote branch** | Branch on the actual server (`github.com`, `gitlab.com`, `bitbucket`, etc.) |
| 👀 **Remote-tracking branch** | Local references like `origin/main`, `origin/feature` |
| 💻 **Local branch** | Branches you work on locally, e.g., `main`, `feature` |

## Configuring Git

Git settings are stored in config files at different scopes:
  - **System**: Applies to all users and repositories on the machine (e.g., `/etc/gitconfig`).
  - **Global**: Applies to all repositories for the current user (e.g., `~/.gitconfig`).
  - **Local**: Applies only to the current repository (e.g., `.git/config`).

Git looks for config values in the order: local → global → system. If a value is set at multiple levels, the one at the highest precedence (local > global > system) will be used.

```bash
# Example: Set VS Code as the default editor for Git commit messages
# --wait: waiting for your editor to close the file before Git continues
git config --global core.editor "code --wait"
# Open the global config file in your default editor
git config --global -e

# View all config values (system, global, local)
git config --list
git config --list --system # view only system config
git config --list --global # view only global config
git config --list --local # view only local config

# Set global username and email (used for commits across all repositories)
git config --global user.name "havs"
git config --global user.email "vosonha1997@gmail.com"

# See more config options: https://git-scm.com/docs/git-config
git config --help
```

## Steps for Working on a Project

### Create SSH Key

Generate an SSH key ([GitHub docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)):

```bash
ssh-keygen -t ed25519 -C "<email or any name>"
```

Add the public key to GitHub: https://github.com/settings/keys

Configure `~/.ssh/config`:

```
Host github_havs          # alias — used when cloning
  HostName github.com     # actual domain of the git server
  User git                # default, no need to change
  IdentityFile ~/.ssh/havs/id_rsa_havs  # path to private key
```

**Note: Multiple SSH keys**

- Generate separate keys and store them in sub-folders (e.g., `~/.ssh/havs/`, `~/.ssh/nus/ken/`, `~/.ssh/nus/tom/`).
- Add each public key to the corresponding GitHub account.
- Add a `Host` entry for each key in `~/.ssh/config`.

### Clone Repo and Configure Git

Clone the repo:

```bash
git clone git@github_havs:sonha-min/RoR-Training.git
```

Set your name and email:

- **Local** (applies only to the current repository):

  ```bash
  git config --local user.name <name>
  git config --local user.email <email>
  ```

- **Global** (applies to all repositories on your machine):

  ```bash
  git config --global user.name <name>
  git config --global user.email <email>
  ```

- **Remove a global config value:**

  ```bash
  git config --global --unset <key>
  ```

- View all config: `git config --list`
- Git settings folder: `.git/`

### Working on a Branch

- **View branches:** `git branch` (`-a` for all branches including remote, `-r` for remote only)

- **Create a new branch:** `git checkout -b <branch-name>` (`-B` to create or reset an existing branch)

- **Make changes → stage → commit → push:**

  ```bash
  git add <files>       # or: git add .
  git commit -m '<message>'
  git push [-u] <remote> <local-branch>:<remote-branch>
  ```

  Push variants:

  | Command | Effect |
  | ----------------------------------------------- | ------------------------------------------- |
  | `git push` | Push to upstream (error if not set) |
  | `git push origin` | Push to upstream on `origin` |
  | `git push origin feature2` | Push local `feature2` to remote `feature2` |
  | `git push origin feature1:feature2` | Push local `feature1` to remote `feature2` |
  | `git push origin :feature1` | Delete remote branch `feature1` |

  Set / unset upstream:

  ```bash
  git branch --set-upstream-to=<remote>/<branch>
  git push -u <remote> <branch>   # shorthand: set upstream and push

  git branch --unset-upstream     # remove upstream tracking
  ```

- **Pull code:**

  `git pull` = `git fetch` + `git merge` (or `git rebase`)

  > **Note:** `git pull` raises an error if no upstream branch is set. If upstream is set, it pulls from the tracked remote branch.

  ```bash
  git pull                          # pull from upstream
  git pull origin <branch-name>     # pull from a specific remote branch
  ```

  Or fetch first, then merge/rebase manually:

  ```bash
  git fetch
  git merge <branch-name>   # or: git rebase <branch-name>
  ```

  > **Note:** Use `git rebase` only on private branches (when working alone). Avoid it on shared branches or when rebasing would cause repeated conflicts.

### git merge vs git rebase

Reference: https://blog.git-init.com/differences-between-git-merge-and-rebase-and-why-you-should-care/

Starting history:

```
A---B---C  (main)
    \
     D---E---F  (feature)
```

**`git merge feature`** (run from `main`):

```
A---B---C-------G   (main)
    \          /
     D---E---F     (feature)
```

`G` is a merge commit that ties both histories together.

**`git rebase main`** (run from `feature`):

```
A---B---C---D'---E'---F'  (feature)
```

`D'`, `E'`, `F'` are replayed commits — no merge commit is created, and history stays linear.

## Common Commands

- **`git remote`** — manage remote connections:
  - `git remote -v` — view remote list
  - `git remote add <name> git@<alias>:<repo-path>` — add a remote
  - `git remote remove <name>` — remove a remote

- **`git stash`** — temporarily store uncommitted changes ([docs](https://git-scm.com/docs/git-stash#_description)); does not include untracked files by default (LIFO order):
  - `git stash list` — view stash list
  - `git stash apply` — apply changes, keeping them in the stash
  - `git stash pop` — apply and remove the latest stash entry
  - `git stash drop` — remove a specific stash entry
  - `git stash clear` — remove all stash entries

- **`git checkout`**:
  - `git checkout -b <new-branch>` — create and switch to a new branch
  - `git checkout <branch>` — switch to a branch
  - `git checkout <commit-id>` — switch to a specific commit (detached HEAD)
  - `git checkout <commit-id>|<branch> <file>` — restore a file to a specific commit or branch version
  - `git checkout .` — restore all files to the last committed version
  - `git checkout -- <file>` — same as `git checkout HEAD <file>`
  - `git checkout -` — switch to the previous branch

- **`git status`** — show current branch, untracked/modified files, staged changes, and conflicts.

- **`git log`** — view commit history:
  - `git log <branch>` — history for a specific branch
  - `git log <commit-id>` — history from a specific commit

- **`git reset`**:
  - `--mixed` (default) — removes commit(s), keeps changes unstaged
  - `--soft` — removes commit(s), keeps changes staged
  - `--hard` — removes commit(s) and discards all changes

- **`git config`**:
  - `git config [scope] <key> <value>` — set a config value
  - `git config --unset <key>` — remove a config value
  - `git config --list` — view all config values

- **Manage merged branches:**
  ```bash
  git branch --merged | grep -v "\*"                              # list merged branches (excluding current)
  git branch --merged | grep -v "\*" | xargs -n 1 git branch -d  # delete them
  git branch --no-merged                                          # list unmerged branches
  ```

## Notes

- Always run `git status` before committing or pushing to review what has changed.
- Use `git log` to inspect commit history and verify saved changes.
- When collaborating, pull frequently to reduce the chance of conflicts.
- If a conflict occurs, resolve it before continuing any other work.

### Resolving Conflicts

Conflicts occur when merging or rebasing causes the same file to have diverging versions. You have three options:

1. Keep your changes (discard theirs)
2. Keep their changes (discard yours)
3. Keep both (edit as needed)

If you are unsure, find the person whose commit caused the conflict and discuss the correct resolution together.

**Steps to resolve:**

```bash
# 1. Edit the conflicting file(s) to resolve the conflict markers
# 2. Stage the resolved files
git add <file(s)>
# 3. Commit the resolution
git commit -m "<message>"
# 4. Push
git push
```

## GUI Tools

- **Git Cola** ([git-cola.github.io](https://git-cola.github.io/)) — view branch status, supports full commands (commit, push, pull, etc.), review changes before committing, and stage parts of a file.
- **gitk** ([docs](https://git-scm.com/docs/gitk)) — visual commit history browser.
- **GitKraken** ([gitkraken.com](https://www.gitkraken.com/)) — visual commit history, drag-and-drop branch management, built-in code editor, and integration with GitHub, GitLab, Bitbucket.
- **SourceTree** ([sourcetreeapp.com](https://www.sourcetreeapp.com/)) — visual commit history, branch management, and integration with GitHub, GitLab, Bitbucket.

## Resources
- **Git cheat sheet** — [education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)
