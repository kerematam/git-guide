# 1 Basics

## 1.1 creating repository

- go to target project root directory

```bash
git init
```

- create some file eg. some-file.md :

```bash
git add some-file.md
git commit -m "first commit"
git remote add origin https://github.com/YOUR_USER_NAME/YOUR_REPO_NAME.git
git push -u origin master
```

## 1.2 Clone
```bash
git clone https://github.com/YOUR_USER_NAME/YOUR_REPO_NAME.git
```

* If you dont need project files but only git history data you may use --mirror or --bare flags. This will only bring .git folder with repository data without any files or folders that exists in working tree :  
```bash
git clone --mirror https://github.com/YOUR_USER_NAME/YOUR_REPO_NAME.git
```

## 1.3 configs

- local and global configs reside :

```yml
global: ~/.gitconfig
local: .git/config
```

- common configs you do in company networks

```bash
git config --global http.sslVerify false
git config --global http.proxy http://proxyUsername:proxyPassword@proxy.server.com:port
```

- passing config on command

```bash
git -c http.proxy=someproxy clone https://github.com/user/repo.git
git clone -c core.autocrlf=false -c core.filemode=false  https://github.com/user/repo.git
```

- unset config

```bash
git config --unset core.editor
```

- edit config file manually

```bash
git config --edit
```

- list configs

```bash
git config --list # list all configs
git config --global --list # lists only global configs
```

* Default local config file will look like this : 
```yml
repositoryformatversion = 0 # about git version compatilibity
filemode = false            # permission changes considered as change by git
bare = false                # working tree is not included
logallrefupdates = true     # enable reflog
ignorecase = true           # ignore file name changes for case
```


## 1.4 Adding ssh key

* Create ssh key: https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

* Adding key : https://help.github.com/en/enterprise/2.15/user/articles/adding-a-new-ssh-key-to-your-github-account

* If you dont want to add ssh key and want to git remember password 
```bash
git config --global credential.helper store # (saved into : ~/.git-credentials); password can be read for this file!!!
```

## 1.5 Basic Commands :

### 1.5.1 Stage and Commit

* Stage
```bash
git status
git add path/to/file.md # stage file
git reset path/to/file.md # undo
git commit -m "Add file"
```


### 1.5.2 Stash

```bash
git stash # save the staged tracked files
git stash list # list stashed changes, last one is on top
git stash push -m "with message" # stash with message
git stash push -m "single file" path/to/file.txt  # stash single file
git apply # applies the first but does not pop it from list
git pop   # applies and deletes it from stash stack  
```
**Untracked files :**

You can start to track them
```bash
git add <untracked-file> # add untracked file
```

Or stash them with -a or -u flag
```bash
git stash -a
git stash -u
```

### 1.5.3 Patch
* Create patch from stash list
```bash
git stash show -p stash@{0} > mychanges.patch
```

* Create patch file from commit (this will have commit author data as well) :
```bash
git format-patch -1 <commit-hash>
```

* Apply Patch :
Check which files will be patched :
```bash
git apply --stat mychanges.patch
```

* Check if patch file can be applicable :

```bash
git apply --check mychanges.patch
```

* Just apply :
```bash
git apply --3way mychanges.patch
```
Note: You better use this option if you create your patch from diff file where no commit info is included.

Apply as a commit (with author etc.) :

```bash
git am --3way < mychanges.patch
```

# 2 Workflows 

## 2.1 Basic
* Just commit and push if you are working alone in repo
```bash
git add FEATURE.md
git commit -m "Add FEATURE.md"
git push
```

## 2.2 Feature (Topic) Branch

<img 
   src="images/feature-branch.png"
   alt="feature branch"
   style="width: 500px; float: left; margin-right: 40px;" />

```bash
git checkout -b feature-branch
git add FEATURE.md
git commit -m "Add FEATURE.md"
git push origin feature-branch
```

* Beware we haven't merge to master yet. You may open a pull request (aka merge request) to master on your browser (Github, Gitlab, Bitbutcket or whatever UI you use). You may also merge it by yourself if you are authorised to do so. Follow command below to merge with master : 

```bash
# if you dont merge with pull request you may use commands below :
git checkout master
git pull # be updated to remote
git merge feature-branch
git push
```
Read more : https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow

### Solving Conflict
* After creating merge request; lets say we have a conflict

* option 1 : merge
```bash
git checkout feature-branch
git merge origin/master # solve the conflict
git commit
git push
```

* option 2 : rebase
```bash
git checkout feature-branch
git rebase origin/master # solve conflicts
git push -f # now history has changed on this branch, so we need to use force flag (-f)
```
### Tradeoffs of Rebase 
* Pros. of Rebase : 
   1. It keeps your feature branch cleaner as you don't need to put few merge commits
* Cons of Rebase : 
   1. Rewriting history. Do not use it for infinite development branches;meaning do not rebase at master.
   2. Difficult to apply with branch having so much commits. Eg. rebasing development branch with 100 commits to master 30 commits behind. It does not make sense; just merge it.

Read more : https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing

## 2.3 GitFlow WorkFlow

<img 
   src="images/gitflow_1.png"
   alt="gitflow branch"
    />

Model proposed by Vİncent Driessen 2010 : https://nvie.com/posts/a-successful-git-branching-model/


There are 5 kinds of branches : master, develop, release, feature and hotfix.

**1. Master Branch** (Immortal)
* It is supposed to be always stable.
* **Feature branches** never directly merges to master.
* Only merges with **Release branch** and **Hotfix branch**.
* It has Tags (ideally for all commits) for version numbers.

**2. Develop Branch** (Immortal)
* It is immortal like master branch, meaning this branch won't be deleted after merge.
* It is NOT supposed to be stable.
* **Feature branches** always directly merges to **Develop branch**.
* **Hotfix branches** also applied to **Develop branch**.

<img 
   src="images/gitflow_3.png"
   alt="feature branch"
    />


**3. Release Branch**
* It is last step before merging to master
* It is forked from **develop branch**
* Some bug fixes can be applied here but NOT feature.
* It merges to **master** and **develop branch** at same time.



<img 
   src="images/hotfix.png"
   alt="feature branch"
   style="height: 500px; float: left; margin-right: 40px;" />

**4 Hotfix Branch**
* Quick and critical fixes that need to be in production immadiately.
* Merges to **master** and **release branch**.

```bash
# Create hotfix branch
git checkout -b hotfix-1.2.1 master
```
 
```bash
# run a script that edits version number on files if needed
./bump-version.sh 1.2.1

# commit version bump
git commit -a -m "Bumped version number to 1.2.1"

# Do your fix, commit and push
git add fixed-file.md
git commit -m "Fixed severe production problem"
git push origin hotfix-1.2.1

```

**5 Feature Branch**
* Forked and Merged only to **develop branch**
```bash
# branch from develop
git checkout develop
git checkout -b myfeature

# ready to develop your feature
git add my-feature.md
git commit -m "feat: add my feature"

# push your changes to remote and open pull request for review
git push origin myfeature
```

If you don't apply pull request then you can merge with commands : 

```bash
git checkout develop

# merge to develop
# --no-ff option will create merge commit in all cases. Because for example if there is no conflict; merge commit won't be created in fast forward merge.
git merge --no-ff myfeature

# delete feature branch
git branch -d myfeature
git push origin develop
```

## 2.4 Triangular Workflow

<img 
   src="images/triangularflow.png"
   alt="triangular flow"
   style="width: 500px; float: left; margin-right: 40px;" />


* It is used in open source project
* take fork from a canonical repository

```bash
git clone https://github.com/YOUR-USERNAME/atom
```

* set upstream (or name it whatever you like)
```bash
git remote add upstream https://github.com/atom/atom
git fetch upstream
```


* Checkout your feature branch
```bash
git fetch upstream
git checkout -b feature-1 upstream/master
```

```bash
# push to your fork
git add change.md
git commit -m "feat: add my changes"
git push origin branch-name
```

```bash
git config remote.pushdefault origin
git config push.default current

# now no need push with origin branch-name
git push
```

To fix or change remote 
```bash
git remote set-url origin https://hostname/USERNAME/REPOSITORY.git
```

### During Conflict
```bash
# merge
git merge upstream master

# or rebase
git rebase upstream master
```

Check where you push
```bash
git push --dry-run
```


## 3 Cheat sheet

### 3.1 Reset vs Checkout
* Reset : moves the HEAD does not leave the branch
```bash
git reset <commit-hash> # soft reset
git reset --hard <commit-hash> # clean working tree
```
* Move N commit into history
```bash
git reset HEAD~4 --hard
```
* Checkout : moves the HEAD and leave the branch; so eventually you fall into detached HEAD state.
```bash
git checkout path/to/file.md
```

### 3.2 Cleaning
* Clean untracked files (you files will be removed, use it carefully)
```bash
git clean -f -d
```

* Have clean remote master
```bash
git fetch upstream
git reset --hard upstream/master 
git clean -df
```

* Safe cleaning with stash
```bash
git add --all
git stash push -m "I am cleaning my repo"
```
If you want to take it back
```bash
git stash list # check your cleaning
git stash apply
```

### 3.3 Rewrite History
### 3.3.1 Amending
* Change last commit :
```bash
git commit --amend
# usage -f flag to change on remote repo
git push -f origin your-branch
```

# Commit Naming
* **feat:** New feature, MINOR in semantic versioning 
* **feat!:** Add exclamation mark when breaking change, MAJOR in semantic versioning
* **fix:** Bug fix, **PATCH** in semantic versioning
* **refactor:** refactoring production code
* **perf:** A code change that improves performance
* **ci:** Changes to our CI configuration files and scripts (example scopes: Travis, Circle, BrowserStack, SauceLabs)
* **docs:** changes in documentation
* **style:** formatting, missing semi colons, etc; no code change
* **test:** adding missing tests, refactoring tests; no production code change
* **chore:** updating grunt tasks etc; no production code change


**Resources :**
* https://www.conventionalcommits.org/
* https://seesparkbox.com/foundry/semantic_commit_messages
* http://karma-runner.github.io/1.0/dev/git-commit-msg.html
* https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines

# TODO
### 3.3.2 Revert Commit
### 3.3.3 Interactive rebase
* cherry pick
* reflog usage 

# Resources
* https://github.com/tiimgreen/github-cheat-sheet
* https://www.atlassian.com/git/tutorials
