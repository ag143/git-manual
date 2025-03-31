The content of the README.md file in the repository ag143/git-manual contains extensive information on various Git commands. Here is the translation of the content from Chinese to English:

<p align="center"> <img src="media/poster.png" width="300" /> <br /> <b>Git Common Commands Reference Manual</b> <p align="center">Covers the git commands used in development, satisfying daily needs</p> <p align="center">Easy-to-understand examples, quick start in 30 minutes</p> <p align="center"> <a href="https://github.com/xjh22222228/git-manual/stargazers"><img src="https://img.shields.io/github/stars/xjh22222228/git-manual" alt="Stars Badge"/></a> <img src="https://img.shields.io/github/license/xjh22222228/git-manual" /> <a href="https://hits.dwyl.com/xjh22222228/git-manual"> <img src="https://hits.dwyl.com/xjh22222228/git-manual.svg" /> </a> </p> </p>
Note: As of October 2020, GitHub has renamed the default branch from master to main.

Table of Contents
Configuration
Initialize Repository
Clone Repository
Manage Repository
Stage Files
Commit Files
Push to Remote
View Branches
Switch Branches
Create Branches
Delete Branches
Rename Branches
Cherry-pick
Stash
File Status
Logs
Blame
Merge
Delete Files
Restore
Pull
Move-Rename
Diff
View Commit History
Revert Version
Undo
Tags
Rebase
Workflow
Submodules
Subtree
Bisect
Archive
Format Logs
Clear Commit History
Help
Commit Convention
Resolve Conflicts
Repository Migration
Tips and Tricks
GUI Clients
Generate SSH Key
Others
Remember Password
Clear Account
Speed Up
Mind Map
Configuration
bash
# View global configuration list
git config --global -l
# View local configuration list
git config --local -l

# View all configurations and their files
git config --list --show-origin

# View the globally set username/email
git config --global --get user.name
git config --global --get user.email

# Set the global username/email
git config --global user.name "xiejiahe"
git config --global user.email "example@example.com"

# Set the local username/email for the current workspace repository
git config --local user.name "xiejiahe"
git config --local user.email "example@example.com"

# Delete configuration
git config --unset --global user.name
git config --unset --global user.email

# Modify the default text editor, such as nano
# Common editors: emacs / nano / vim / vi
git config --global core.editor nano

# Set the default diff tool to vimdiff
git config --global merge.tool vimdiff

# Edit the current repository configuration file
git config -e  # Equivalent to vi .git/config

# Changes in file permissions also count as changes, can be ignored by the following configuration
git config core.fileMode false

# Set file case sensitivity, git ignores case by default
git config --global core.ignorecase false

# Configure to pull all submodule content by default when using git pull
git config submodule.recurse true

# Remember the commit account password, next operation without account password
git config --global credential.helper store # Permanent
git config --global credential.helper cache # Temporary, default 15 minutes
Command Alias Configuration
Git can use aliases to simplify some complex commands, similar to the alias command.

bash
# git st is equivalent to git status
git config --global alias.st status

# If added before, you need to add --replace-all to overwrite
git config --global --replace-all alias.st status

# Execute external commands, just add ! at the front
git config --global alias.st '!echo hello';
# Add "!" to execute external commands for a complex merge process, for example:
git config --global alias.mg '!git checkout develop && git pull && git merge master && git checkout -';

# Delete st alias
git config --global --unset alias.st
Configure Proxy
bash
# Set
git config --global https.proxy  http://127.0.0.1:1087
git config --global http.proxy  http://127.0.0.1:1087

# View
git config --global --get http.proxy
git config --global --get https.proxy

# Cancel proxy
git config --global --unset http.proxy
git config --global --unset https.proxy
Initialize Repository
git init creates an empty Git repository or reinitializes an existing one.

In fact, the git init command is not often used, usually operated on the GUI.

bash
# Generates .git in the current directory
git init

# Create in quiet mode, only prints errors or warnings
git init -q

# Create a bare repository in the current directory, containing only the files under .git
git init --bare
Clone Repository
bash
# Clone using https protocol
git clone https://github.com/xjh22222228/git-manual.git

# Clone using SSH protocol
git clone git@github.com:xjh22222228/git-manual.git

# Clone a specific branch, -b specifies the branch name, actually clones all branches and switches to the develop branch
git clone -b develop https://github.com/xjh22222228/git-manual.git

# --single-branch completely clones only the specified branch
git clone -b develop --single-branch https://github.com/xjh22222228/git-manual.git

# Specify the folder name after cloning
git clone https://github.com/xjh22222228/git-manual.git git-study # If the latter is . create in the current directory

# Recursively clone, useful if the project contains submodules
git clone --recursive https://github.com/xjh22222228/git-manual.git

# Shallow clone, clone depth is 1, only clone the specified branch and keep only the latest history record, usually used to reduce clone time and project size
git clone --depth=1 https://github.com/xjh22222228/git-manual.git
git clone --depth=1 --no-single-branch https://github.com/xjh22222228/git-manual.git # --no-single-branch clones all other branches as well

# Bare clone, no working directory content, cannot commit changes, usually used to replicate repositories
git clone --bare https://github.com/xjh22222228/git-manual.git

# Mirror clone, also bare clone, the difference is it includes upstream version library registration
git clone --mirror https://github.com/xjh22222228/git-manual.git
Clone Specific Folder
Some repositories include client, server, and other multiple ends of the code, but you don't want to clone the entire project, just a specific folder. In this case, you need to use sparse checkout.

Sparse checkout must meet 2 conditions:

core.sparsecheckout set to true
.git/info/sparse-checkout file lists the directories to be checked out
This repository has a media folder, let's use it for demonstration.

bash
# 1. Create a directory and enter it
mkdir hello-git && cd hello-git

# 2. Initialize the repository
git init

# 3. Set the repository address
git remote add origin https://github.com/xjh22222228/git-manual.git

# 4. Enable sparse checkout
git config core.sparsecheckout true

# 5. Edit the .git/info/sparse-checkout file, default is not present so you need to create it manually
# You can also use a command to write the directories to be checked out into it
echo "media" >> .git/info/sparse-checkout

# 6. Pull the content, here specifying the master branch
git pull origin master
<details> <summary>Demonstration of cloning a specific folder.gif</summary> <img src="media/gitclone-sparsecheckout.gif"> </details>
Manage Repository
The git remote command is used to manage remote repositories.

Usually, a project corresponds to multiple repositories which require the use of git remote, for example, to push to github / gitee / gitlab, you can use git remote to manage multiple repository addresses.

bash
# View remote repositories, usually prints origin, which is the default name Git gives to the cloned repository server
# Usually only shows origin unless you have multiple remote repository addresses
git remote

# Specify -v to view the current remote repository addresses
git remote -v

# Add a remote repository address, example is a custom name
# After adding, you can see example by running git remote
git remote add example https://github.com/xjh22222228/git-manual.git

# View information of a specified remote repository
git remote show example

# Rename remote repository
git remote rename oldName newName # git remote rename example simple

# Remove remote repository
git remote remove example

# Modify the remote repository address, change from HTTPS to SSH
git remote set-url origin git@github.com:xjh22222228/git-manual.git

# Subsequent pushes can specify the repository name
git push example
Stage Files
bash
# Stage all files
git add -A

# Stage a specific file
git add ./README.md

# Stage all changed files in the current directory
git add .

# Stage a series of files
git add 1.txt 2.txt ...
Commit Files
bash
# -m for commit message
git commit -m "changes log"

# Commit only a specific file
git commit README.md -m "message"

# Commit and show diff changes
git commit -v

# Allow empty messages, usually must specify -m parameter
git commit --allow-empty-message

# Rewrite the last commit message, ensure the current workspace has no changes
git commit --amend -m "new commit message"

# Skip verification if tools like husky are used
git commit --no-verify -m "Example"
Modify Commit Date
When executing git commit, git will use the current default time, but sometimes you may want to modify the commit date using the --date parameter.

Format: git commit --date="Month Day Time Year +0800" -m "init"

Example: git commit --date="Mar 7 21:05:20 2021 +0800" -m "init"

Month abbreviations are as follows:

Month Abbreviation	Description
Jan	January
Feb	February
Mar	March
Apr	April
May	May
Jun	June
Jul	July
Aug	August
Sep	September
Oct	October
Nov	November
Dec	December
Push to Remote
bash
# Push the current branch by default
# Equivalent to git push origin, actually pushes to a repository named origin by default
git push

# Push to the main branch
git push -u origin master

# Push local branch to remote branch, localBranch:remoteBranch
git push origin <branchName>:<branchName>

# Force push, --force shorthand
git push -f
View Branches
bash
# View all branches
git branch -a

# View local branches
git branch

# View remote branches
git branch -r

# View local branches associated with remote branches
git branch -vv

# View the creation time of the local master branch
git reflog show --date=iso master

# Search branches, use grep command to search for branches containing the keyword dev
git branch -a | grep dev
 Public code references from 1 repository
Add Notes to Branches
Sometimes there are too many branches to determine what each branch did by name alone.

bash
# Command
$ git config branch.{branch_name}.description Note

# Add a note to the hotfix/tip branch
$ git config branch.hotfix/tip.description Fix details
Switch Branches
bash
# Switch to the master branch
git checkout master

# Switch to the previous branch
git checkout -

# Force switch, but be careful, if files are not saved, changes will be overwritten
git checkout -f master

# -t, switch to remote branch, if you have added a new repository with git remote, you need to use -t to switch
git checkout -t upstream/main
When cloning with --depth=1, switch to other branches, such as switching to the dev branch:

bash
git clone --depth=1 https://github.com/xjh22222228/git-manual.git

# Switch to the dev branch
git remote set-branches origin 'dev'
git fetch --depth=1 origin dev
git checkout dev
Besides using git checkout, there is another way to switch which is git switch, introduced in Git version 2.23, mainly for switching and creating branches.

bash
# Switch to the develop branch
git switch develop

# Switch to the previous branch
git switch -

# Force switch to the develop branch, discarding all local changes
git switch -f develop

# Create a branch and switch
git switch -c newBranch

# Force create a branch
git switch -C newBranch

# Create a new branch from the previous 3 commits
git switch -c newBranch HEAD~3 

# -t, switch to remote branch, if you have added a new repository with git remote, you need to use -t to switch
git switch -t upstream/main
Create Branches
bash
# Create a local branch named develop
git branch develop

# Force create branch, no warnings or information
git branch -f develop

# Create a local develop branch and switch
git checkout -b develop

# Create a remote branch, actually create a local branch and then push to remote
git checkout -b develop
git push origin develop

# Create an empty branch, does not inherit from the parent branch, history is empty, usually requires at least 4 steps
git checkout --orphan develop
git rm -rf .  # This step is optional if you really want to create a branch without any files
git add -A && git commit -m "commit" # Add and commit, otherwise the branch is hidden (ensure the current workspace retains at least one file before executing this step, otherwise it cannot be committed)
git push --set-upstream origin develop # Push to remote
Delete Branches
Note: You cannot delete the current branch, switch to another branch before deleting.

bash
# Delete the local branch
$ git branch -d <branchName>

# Capital D to force delete an unmerged branch
# Equivalent to git branch --delete --force <branchName>
$ git branch -D <branchName>

# Delete remote branch
$ git push origin :<branchName>
$ git push origin --delete <branch-name>  # >= 1.7.0
Rename Branches
bash
# Rename the current branch, usually requires 3 steps
# 1. Change the branch name
# 2. Delete the remote old branch
# 3. Push the renamed branch to remote
git branch -m <branchName>
git push origin :old_branch
git push -u origin new_branch

# Rename a specific branch
git branch -m old_branch new_branch
Cherry-pick
git cherry-pick can be used to transfer a commit from one branch to the current branch.

Assuming there are 2 branches, dev and main, the dev branch has 10 commit records, and the main branch wants to merge the 5th commit record from the dev branch to the current branch. This is the usage scenario for this command.

You can also understand it as re-submitting a previous commit.

bash
# Can be a commit_id or branch name
# If it is a branch name, it is the last commit
git cherry-pick <commit_id>|branch_name

# Supports transferring multiple commits, each will create a separate commit record
git cherry-pick <commit_id1> <commit_id2>

# Retain the original author information for the commit
git cherry-pick -x <commit_id>

# Re-edit the commit message, otherwise it will use the previous commit message
git cherry-pick -e <commit_id>

# Abort the current operation and return to the initial state
git cherry-pick --abort

# When a conflict occurs, resolve the conflict, add to the staging area, and then use the following command to continue
git cherry-pick --continue
<details> <summary>Demonstration of cherry-pick.gif: Transfer the third commit of the `dev` branch to the current `master` branch.</summary> <img src="media/cherry.gif"> </details>
Stash
The stash operation uses the git stash command.

The git stash command is useful if you are working on a feature and suddenly need to switch branches to fix a bug, but you don't want to commit (since switching branches requires a clean working directory, otherwise it cannot be switched).

bash
# Save the current changes in the working directory
git stash

# Save with a comment, recommended to use this command
git stash save "
