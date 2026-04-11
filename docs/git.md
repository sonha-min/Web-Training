## What is Git?

- Git is a **distributed version control system** — each person has a full copy of the repository on their own computer.
- Git allows multiple people to work on the same project **without needing to be constantly connected to each other or to a central server.**
- Git keeps **track of the entire history of changes**, making it easy to **go back to previous versions** if needed.
- **Repository**: Stores the full history of commits and tracks all changes made to the codebase.

## Git Workflow

- **🗂️ Working directory**: It’s your local workspace, where you make changes directly to files.
- **🗒️ Staging area (Index)**: A middle layer that holds changes you’ve prepared to commit. You can selectively add or remove changes in the staging area before committing —> giving you precise control over what gets saved to version history.
- **💻 Local repository**: A version of the repository stored on your local machine. It contains the full history of commits and allows you to work offline.
- **🌐 Remote repository**: A version of the repository hosted on a server (e.g., GitHub, GitLab, Bitbucket). It enables collaboration by letting multiple people clone the code, push their changes, and pull updates from others.

![Git Space](https://github.com/vosonha/RoR-Training/blob/main/lib/images/gitWorkflow.png)

## Git branch

Trong Git có 3 lớp chính:
| Mức | Mô tả |
| ---------------------------- | -------------------------------------------------------------- |
| 🌐 **Remote branch** | Branch on the actual server (`github.com`, `gitlab.com`, `bitbucket` v.v.) |
| 👀 **Remote-tracking branch** | `origin/main`, `origin/feature` on working directory |
| 💻 **Local branch** | `main`, `feature` on working directory |

## Steps for working on a project

### Create SSH key

- Generate ssh key: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

  ```bash
  ssh-keygen -t ed25519 -C "<email or any name>"
  ```

- Add public key to github: https://github.com/settings/keys

- Config file `~/.ssh/config`:

  ```bash
  Host github_havs => alias, choose as you like, it must be used when cloning code
    HostName github.com => actual domain of the git server
    User git => default, does not need to change
    IdentityFile ~/.ssh/havs/id_rsa_havs => file private key pair path
  ```

**Note:** Configure multiple ssh keys

- Generate keys => should create sub folder for each key (e.g., `~/.ssh/havs/.`, `~/.ssh/nus/ken/.`, `~/.ssh/nus/tom/.`)

- Add public keys to github accounts

- Config file `~/.ssh/config`

### Clone repo and configure git

- Clone repo: `git clone <git-url>`

  ```bash
  git clone git@github_havs:sonha-min/RoR-Training.git
  ```

- Git config user name and email:
  - Local config -> applies only to the current repository.

    ```bash
    git config --local user.name <name>
    git config --local user.email <email>
    ```

  - Global config -> applies to all repositories on your machine.

    ```bash
    git config --global user.name <name>
    git config --global user.email <email>
    ```

  - Remove global config:

    ```bash
      git config --global --unset <key>
    ```

- View config: `git config --list`
- Git settings folder: `.git/`

### Working on branch

- View local branches and current branch: `git branch` (`-a` to view all branches including remote branches, `-r` to view remote branches only)

- Create new branch: `git checkout -b <branch name>` (`-B` create new branch or reset existing branch)

- Make changes -> add + commit -> push:
  - `git add <files>` or `git add .`

  - `git commit -m '<your message>'`

  - `git push [-u] <remote name> <local branch>:<remote branch>`
    - `git push` -> push to upstream (it will raise error if upstream is not set)
      - Set upstream branch for current branch:

        ```bash
          git branch --set-upstream-to=<remote>/<branch>
          git push -u <remote> <branch>
        ```

      - Unset upstream branch for current branch:

        ```bash
          git branch --unset-upstream
        ```

    - `git push origin` -> push to remote branch on remote origin

    - `git push origin feature2` -> push local branch feature2 to remote branch feature2 on remote origin

    - `git push origin feature1:feature2` -> push local branch feature1 to remote branch feature2 on remote origin => ko khuyến khích

    - `git push origin :feature1` -> delete remote branch feature1 on remote origin

- Pull code:
  - `git pull` = `git fetch` + `git merge/rebase`

    **Note:**

    ```bash
      - raise error if local branch is not set upstream branch.
      - if local branch is set upstream branch, it will pull code from remote branch to local branch.
    ```

  - `git pull origin <branch name>` -> pull code from remote branch to local branch

  - `git fetch` -> `git merge <branch name>` or `git rebase <branch name>`
    - **Note:** Use `git rebase` only on private branches (when working alone). Do not use it on shared branches, or on your own branch if rebasing causes conflicts more than once.

### git merge vs git rebase

https://blog.git-init.com/differences-between-git-merge-and-rebase-and-why-you-should-care/

Example:

- History commit:

  ```bash
    A---B---C  (main)
        \
          D---E---F  (feature)
  ```

- `git merge feature` (from `main`)

  ```bash
    A---B---C-------G   (main)
        \          /
          D---E---F     (feature)
  ```

  `-> G is a merge commit`

- `git rebase main` (from `feature`)

  ```bash
    A---B---C---D'---E'---F'  (feature)
  ```

  `-> D', E', F' is the new commit history for feature branch, does not create a merge commit`

## Common commands

- `git remote`:
  - `git remote -v`: view remote list
  - `git remote remove <remote name>`: remove remote
  - `git remote add <remote name> git@alias_name:git_repo_path`: add remote

- `git stash`: temporarily store changes that are not yet committed (does not include untracked files by default) -> (First In, Last Out)
  - `git stash list`: view stashed list
  - `git stash apply`: apply changes, keeping code changes in stash
  - `git stash pop`: apply and pop code change from stash
  - `git stash drop`: remove a specific stash
  - `git stash clear`: remove all stash
    https://git-scm.com/docs/git-stash#_description

- `git checkout`:
  - `git checkout -b <new_branch_name>`: create and switch to a new branch
  - `git checkout <branch_name>`: switch branch
  - `git checkout <commit_id>`: switch to specific commit
  - `git checkout <commit_id>|<branch_name> <file_name>`: restore file to a specific commit or branch version
  - `git checkout .`: restore all files to the last committed version (in working directory)
  - `git checkout -- <file_name>` == `git checkout HEAD <file_name>`: Restore file to the last committed version (in working directory)
  - `git checkout -`: switch to the previous branch

- `git status` -> provide info: current branch, untracked files, modified files, files ready to commit, conflict.

- `git log` -> view commit history
  - `git log <branch_name>`: view commit history of a specific branch
  - `git log <commit_id>`: view commit history from a specific commit

- `git reset`:
  - `--mixed` (default): removes commit history, keeps code changes, but does not add changes to the index (staging area)
  - `--soft`: removes commit history, keeps code changes, and adds changes to the index (without untracked files)
  - `--hard`: removes all changes and commit history

- `git config`:
  - `git config [scope] <key> <value>`: set config
  - `git config --unset <key>`: remove config
  - `git config --list`: view all config

- `git branch --merged | grep -v "\*"`: view merged branches (except current branch)
  - `git branch --merged | grep -v "\*" | xargs -n 1 git branch -d`: delete merged branches (except current branch)
  - `git branch --no-merged`: view unmerged branches

## Notes

- Luôn kiểm tra trạng thái của repository bằng `git status` trước khi thực hiện commit hoặc push.
- Sử dụng `git log` để xem lịch sử commit và kiểm tra các thay đổi đã được lưu.
- Khi làm việc với nhiều người, hãy thường xuyên pull code để tránh conflict.
- Nếu xảy ra conflict, hãy giải quyết trước khi tiếp tục làm việc.

### Resolve conflict (conflict xảy ra khi merge/rebase mà có code trong 1 file khác version):

- Lựa chọn:

  ```bash
    - Bỏ code mình (1)
    - Bỏ code người ta (2)
    - Giữ cả 2 (có thể modify) (3)
  ```

=> Đọc code tự tin thì chọn (1) or (2) or (3). Không tự tin thì kiếm người tạo ra commit conflict với mình để discuss và giải quyết.

- Giải quyết:

  ```bash
    - Sửa file(s) bị conflict
    - git add <file(s)>
    - git commit -m <message>
    - git push
  ```

## GUI tool

- Git cola: https://git-cola.github.io/
  - View the status of branches and supports full commands (commit, push, pull, etc.)
  - Review changes before committing
  - Easily commit parts of a file or revert changes

- gitk: view commit history (https://git-scm.com/docs/gitk)

- Git cheat sheet: https://education.github.com/git-cheat-sheet-education.pdf
