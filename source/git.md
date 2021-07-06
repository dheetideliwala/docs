## Git

### Setup

- Create account and then repo at github.com

- Setup ssh keys on mac

  1. Check if ssh keys are available on my mac: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/checking-for-existing-ssh-keys. If found, go to step 3.

  2. If no keys are available, then generate new keys: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

  3. Add the keys to ssh-agent: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent

  4. Add the keys to github account: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

  5. Validate my ssh setup:

     ```bash
     ssh -T git@github.com
     ```

  6. Make a copy of the ssh keys elsewhere

     ```bash
     ls ~/.ssh/id*
     ```

### Basics

- `origin `: Default remote pointer to the repo on github.com

- `master `: Default branch on the repo

- `origin/master `: a remote tracking branch on local. It is hidden and is supposed to stay in sync with `master ` on remote `origin `. Tracking branch is a local branch that has a direct relationship to a remote branch. If you are on a tracking branch, the `git pull` knows which server to fetch from and which branch to merge in.

- Git has a staging area! There is both a repo and a staging area (also called index)

- Working directory -> Staging -> Head.

  - `add ` updates Staging from the working directory.
  - `commit` puts staging into the local copy of the repo.
  - `reset ` overrides Staging with last commit on Head. You undo an `add `with a `reset `.
  - `checkout ` overwrites working directory with a copy from staging. You undo an edit with `checkout `.

- `push ` works only if you cloned from a server to which you have write access and nobody has pushed in the meantime. 

- File status: `untracked `, `unmodified `, `modified `, `staged `.

  - `untracked ` is a new file not in previous commit snapshot. `untracked ` gets tracked only after `git add`.
  - `git add` moves file from `untracked ` to `staged`
  - edit: moves file from `unmodified ` to `modified`
  - add: moves file from `modified ` to `staged`
  - commit: moves file from `staged ` to `unmodified`
  - remove: moves file from `unmodified ` to `untracked`

- Bash alias

  ```bash
  grbi 	- git rebase -i master
  git rebase -i --autosquash
  gcane - git commit -amend --no-edit
  grbc	- git rebase --continue
  push	- git push origin `git rev-parse --abbrev-ref HEAD`
  gll 	- git log --oneline --decorate
  gcf 	- git commit --fixup <commit>
  ```

- Do not forget to create `.gitignore`

  ```.gitignore
  # ~/.gitignore_global can have common .gitignore entries
  
  # ignore all .a files
  *.a
  # but do track lib.a, even though you're ignoring .a files above
  !lib.a
  # only ignore the TODO file in the current directory, not subdir/TODO
  /TODO
  # ignore all files in any directory named build
  build/
  # ignore doc/*.txt, but not doc/server/*.txt
  doc/*.txt
  # ignore all .pdf files in the doc/ directory and any of its subdirectories
  doc/**/*.pdf
  ```

### My experiment

```bash
# create a remote repo sample 
git clone <repo address>
touch <filename>
# add files to be tracked
git add <filename>
# add files to be committed
git commit -m "message"
# push files to remote
git push
# see the status of all the files
git status

# create a branch under main
git branch <branchname>
# move to new branch
git branch checkout <branchname>
# see the branches, and current branch in green
git branch
# merge new branch into current branch
git branch merge <branch to be merged>
# delete branch on local
git branch -d <branchname>
# delete branch on remore
git push origin --delete <branchname>
```



### Working with local

>  This assumes that you do not have a remote repo on github.com. You are just using git on your local.

```bash
# create a local repo
mkdir demo
cd demo
git init # turns an existing directory into git repository

# ... make some changes

# status
git status # shows which files changed in working directory compared to staging
git status -s # short version

# add all changes from working directory to staging
git add . # will only add files in current directory
git add <file-1> <file-2> <file-3> # you can add multiple files
git add -all # includes updated tracked files everywhere as well as new files everywhere
git add -A # includes updated tracked files and new files
git add --update # includes updated tracked files only, not new files
git add -i # interactive

# commit - creates a snapshot inside the staging
git commit -m 'some comment'

# Combine add and commit
git commit -a # implicitly add files already existing in the last commit

```

#### Reference

- https://training.github.com/git-guides/git-init
- https://training.github.com/git-guides/git-status
- https://training.github.com/git-guides/git-add
- https://training.github.com/git-guides/git-commit
- https://training.github.com/git-guides/git-branch

### Working with remote

> Remote repo must exist before hand on Github. There is no cli to create a remote.

```bash
# clone a remote repo
git clone <your.remote.git> # will create directory
git clone <your.remote.git> <your.output.directory> # create repo inside specified directory

# Connect a local to a remote repo.
# Scenario: You have an empty repo on Github, and you never cloned it.
# But on local you have a repo that you wish to connect to this empty repo.
cd existing_local_repo
git remote add origin https://your.remote.git

# Connect a local to a remote repo.
# Scenario: You fork someone's repo into your repo.
# 1. You fork original repo to your repo
# 2. You clone your repo to local. A default remote "origin" is created
# 3. You want to add another remote to your local - lets call is upstream
git remote add upstream https://original.repo.git

# Commands below are same. They create the remote tracking branch on local
git push -u origin master # -u is neccessary the first time, and subsequently just git push
git push --set-upstream origin master

# example: setup upstream branch using git push
git checkout -b new_branch
git branch -vv # won't show a tracking branch for new_branch
git push -u origin new_branch # or..
git push -u origin HEAD
git branch -vv # now shows a tracking branch for new_branch

# fetch and pull
git fetch <remote_repo_name> # fetch and pull from remote into staging
git pull # fetches and merges into working directory

# show remote servers configured on local
git remote # by default it would show origin
git remote -v
git remote -vv # also include tracking branches on local
git remote show <remote> # inspecting a remote
git remote show origin
```

### Branching

```bash
# creates the 'master' branch in the 'origin' repo that will be created on local
git init

# create a branch (two commands)
git branch new_branch_name # create
git checkout new_branch_name # switch to it

# create a branch (single command)
git checkout -b new_branch_name # create and switch
git branch -vv # check tracking branches on local

# example: simple flow
# feature branch
git checkout master
git checkout -b dev_branch # new feature branch
# make changes
vim index.html
git commit -a -m "create new footer"

# example: hotfix
git checkout master
git checkout -b hotfix # new hotfix branch
# make changes
vim index.html
git commit -a -m "fix email address"
git checkout master
git merge hotfix # merge hotfix branch into master
git branch -d hotfix
# continue on feature branch
git checkout dev_branch
# make changes
vim index.html
git commit -a -m "finish the new footer"
git checkout master
git merge dev_branch # no merge conflict

# if merge conflict, update index.html on master
git add index.html
git commit -m "Final commit"
git branch -d devbranch

# Merge A into B
git checkout B
git fetch A
git merge A
# or
git checkout B
git pull A # does the fetch and merge

# example: create a branch from master and push the branch to remote
git pull origin master # Make sure local repo matches with remote
git checkout -b <new-branch-name> # create branch and switch to it
git push origin <new-branch-name>

# management
git branch # list of current branches
git branch -all
git branch -v # last commit on each branch
git branch --merged # list of branches merged into the branch you're on.
git branch --no-merged # list of branches you haven't yet merged in.
git branch --no-merged master # list of branches not merged into the master
git branch -av
git branch -avv # which remote branches are linked to your local branch
git remote -v # what remote branches exist

# rename branch
git branch --move old_branch_name new_branch_name # rename branch on local
git push <remotename> <localbranchname>:<remotebranchname>
git push --set-upstream origin new_branch_name # add new branch on remote
git push origin --delete old_branch_name # delete old branch on remote

# delete branch
git branch -d my-branch # local
git push <remotename>:<branchname> # remote
git push origin:my-branch 	# remote
git push origin:branch1 :branch2 :branch3

# create a branch and push to remote
git checkout -b my-branch	# create branch and switch to it

# make changes, git add, git commit -m etc...
# push the branch to remote for others to see
git push <remotename> <branchname> # first time
git push -u origin my-branch <can be master>

# remote branches
git ls-remote <remote> # list of remote branches
git remote show <remote>

# synchronize local from remote, will only change S, not WD
git fetch <remote>
git fetch origin

# push branch to remote
git push <remote> <local_branch>
git push origin my_branch

# someone else
git fetch origin # will get origin/my_branch on local
git checkout -b copy_of_my_branch origin/my_branch # now has a local branch

# delete remote branch
git push origin --delete mybranch

# copies last commit to both Staging and WD
git checkout HEAD -- files

# pushing local dev branch to master
git push origin dev:master

#merge another branch to your branch
git merge <branch>

# pulling a different branch to local
git pull origin master:dev

# rebasing - integrate changes from one branch into another
# use merge or rebase which provides cleaner history
```

### Standard practice

- Two branches: `origin `, and `develop`
- Multiple supporting branches: `features `, `release-* `, and `hotfix-*`

```bash
# create feature branch from develop
git checkout -b myfeature develop

# feature is now stable and ready to move forward
git checkout develop
git merge --no-ff myfeature

# push develop
git push origin develop # pushes branch develop to remote origin
git push origin dev:master # optionally pushes local branch to remote's master branch

# create release branch on develop
git checkout -b release-1.2 develop

# release is now stable
git checkout master
git merge --no-ff release-1.2
git tag -a 1.2

# the changes done in release need to be available to develop
git checkout develop
git merge --no-ff release-1.2

# delete release 1.2
git branch -d release-1.2 # delete on local
git push origin --delete release-1.2 # delete on remote

# create hotfix on master
git checkout -b hotfix-1.2.1 master

# do some work on hotfix
git commit -a -m "some hot fix into 1.2.1"
git checkout master
git merge --no-ff hotfix-1.2.1
git tag -a 1.2.1
git tag 1.2.1 1b2e1d63ff # first 10 of the commit id

# changes done in hotfix need to be available to develop
git checkout develop
git merge --no-ff hotfix-1.2.1

# delete local branch of hotfix
git branch -d hotfix-1.2.1
# delete remote branch of hotfix
git push origin --delete hotfix-1.2.1
```

### Diff

```bash
git diff # diff between working directory and staging
git diff --cached # diff between Staging and HEAD
git diff --staged # what's in Staging which will go to Head on next commit. This will not show all changes, only what is staged.

# diff between some-branch and working directory
git diff <source_branch> <target_branch>
git diff <some-branch>
git diff HEAD

# diff between two commits
git diff first-commit last-commit
```

### Log

> By default, `git log `will only show commit history of the branch you have checked out.

```bash
git log 			# long output
git log -p -2 # shows last 2 commit details
git log --since=2.weeks # list of commits in last two weeks
# you can specify "2008-01-15", "2 years 1 day 3 minutes ago"

git log --stat
git log --pretty=oneline 			# compressed log view
git log --oneline --decorate 	# shows where the branch pointers are pointing
git log --pretty=format:"%h %s" --graph
git log --oneline --decorate --graph --all
git log --pretty=format # shows format help
git log --pretty=format:"%h - %an, %ar : %s"
git log --author=bob # commits by certain author
git log --graph --oneline --decorate --all # decorated
git log --name-status # which files have changed
git log --since=2.weeks # --before, --after, --until
git log --since="2 years 1 day 3 minutes ago"

# commits but not merged
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" --before="2008-11-01" --no-merges -- t
```

### Checkout

When dealing with a branch (say, `master `) copied from a remote repository (say, `origin `), there are *three* branches one must consider:

1. The copy of `master `in the local repository
2. The copy of `master `in the remote repository
3. The local tracking branch `origin/master `that records the last known position of the remote branch. This  branch tracks the remote branch and is a local copy of the remote branch.

The `git fetch ` copies branch [2] to  [3]:

```
git fetch origin master
```

The `git checkout ` copies branch [3] to [1].

```
git checkout master
```

Copy files from Head or Staging to the Working Directory, and to optionally switch branches.

- If the commit name is provided, the files are copied from commit to staging and wd.
- If commit name is not provided, the files are copied from staging to wd.

The `push `command copies branch [1] to [2], and incidentally updates [3] to match with [2].

```
git push origin master
```

### Remove files

To remove a file from Git, you have to remove it from your staging area and then commit. The `git rm ` command does that, and also removes the file from your working directory so you don’t see it as an untracked file the next time around.

```bash
rm <file> # removes untracked files or files in working directory, but not from staging
git rm <file> # removes from staging and working directory
git rm --cached <file> # remove from staging, but retain in working directory
git commit -f # file is gone

git rm log/\*.log # removes all files with .log extension
git rm \*~ # removes all files ending in ~
```

### Moving files

Git does not explicitly track file movement. If you rename a file in Git, no metadata is stored in Git that tells it you renamed the file. However, Git is pretty smart about figuring that out after the fact.

```bash
git mv <file_from> <file_to>
# is equivalent to following
mv <file_from> <file_to>
git rm <file_from>
git add <file_to>
```

### Undo things

#### You committed early

If you commit and then realize that you forgot to stage the changes in a file you wanted to add to this commit, you can:

```bash
git commit -m 'Initial commit' # some file(s) forgotten.
git add <forgotten_files>
git commit --amend -m

#or undo the last commit
git reset --soft Head^
git add .
git commit -m
```

#### Unstage a staged file

Let’s say you’ve changed two files and want to commit them as two separate changes, but you accidentally type `git add * ` and stage them both. How can you unstage one of the two?

```bash
# file-a and file-b added to Staging
git add *

# unstage file-b
git reset HEAD file-b

# file-a is committed
git commit -m

# stage file-b
git add file-b

# commit file-b as a separate commit
git commit -m
```

#### Unmodify a modified file

You realize that you don’t want to keep your changes to the `file-b `? How can you revert it back to what it looked like when you last committed

```bash
git checkout --<file-b>
```

#### Undo commits permanently

```bash
git commit ... # mistake(s)
# The last three commits HEAD, HEAD-1, HEAD-2 are bad and you don't want to commit them again.
git reset --hard HEAD~3
```

#### Drop all changes in local

```bash
git fetch origin # fetch latest
git reset --hard origin/master # switch local master to it
```

#### Undo a commit and redo

```bash
git commit ... # mistake
git reset --soft HEAD^
# make the edits
git commit -a -c ORIG_HEAD
```

#### Update local with latest on remote

```bash
git pull # this does fetch and merge
# same as
git fetch
git merge

# switches Working directory and Staging to last commit in HEAD
git checkout HEAD
```

#### Squashing commits

```bash
# you are in <my-feature>
git fetch origin
git reset --soft origin/master # Moves HEAD to last commit of Remote
git commit
git push origin feature/<my-feature> -f # fix the error
```

#### Push fails because remote is ahead of local

Since remote is a moving branch, its possible that the push to include your local changes on remote may fail. Here is how you address it:

```
# Refresh the tracking branch. This is a remote tracking branch on local
git fetch origin master

# Integrate our changes with remote changes
git rebase origin/master

# The above two commands can be combined into one command
git pull --rebase origin master

# Finally attempt the push to remote again
git push origin master
```

### Rebase

There are two ways to integrate changes from one branch into another: the `merge ` and the `rebase `. The `rebase ` offers a cleaner history, while the `merge ` will offer full transparency.

```bash
# experiment has C4 made on C2
# master has C3 made on C2
# C2 is the common ancestor
git checkout experiment
git rebase master # Add experiment to master on C3

# above two commands can be replaced with
git rebase master experiment

# merge
git checkout master # new changes are on existing commit C3
git merge experiment # master + experiment are now on C4
```

### Squash Commits

Take some smaller commits and combine them into larger ones.

> Only do this on commits that have not been pushed to remote repo.

Example: Rebase using the last 4 commits from where the `HEAD `is with `HEAD-4 `.

```bash
# assume you have 4 commits on local.
git rebase -i HEAD 4 # Rebase using last 4 commits from where HEAD
```
