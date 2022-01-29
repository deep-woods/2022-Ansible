# 01 Basics

<br>

Repetitive real-world tasks

- Provisioning: sizing/creating new hosts or virtual machines 
- Configuration management: find configurations on them
- Continuous Delivery
  - patching on hundreds of servers
- Application deployment
  - migrations, deploying applications
  - security compliance audits

<br>

## Use case

<br>

**Scenario 1.**

- You have a number of hosts that you have to restart in a particular order.
- Some of them are web servers others are database servers

<br>

**Scenario 2.**

- a complex infrastructure that spans across public and private clouds with hundreds of VMs.

Ansible:

- Easy provisioning
  - VMs on public clouds like Amazon
  - private cloud environments like VMware,

- Configuration of...
  - application
  - connection between hosts
  - firewall rules


<br>

## Setup

<br>

**Scenario**

1. Create a Ansible control machine
2. Create 2 more target machines for Ansible to perform configuration changes on. 

<br>

### Setup 

Download the VM template from https://www.osboxes.org/



<br>

## `git help`

    git help

    start a working area (see also: git help tutorial)
    clone      Clone a repository into a new directory
    init       Create an empty Git repository or reinitialize an existing one

    work on the current change (see also: git help everyday)
    add        Add file contents to the index
    mv         Move or rename a file, a directory, or a symlink
    reset      Reset current HEAD to the specified state
    rm         Remove files from the working tree and from the index

    examine the history and state (see also: git help revisions)
    bisect     Use binary search to find the commit that introduced a bug
    grep       Print lines matching a pattern
    log        Show commit logs
    show       Show various types of objects
    status     Show the working tree status

    grow, mark and tweak your common history
    branch     List, create, or delete branches
    checkout   Switch branches or restore working tree files
    commit     Record changes to the repository
    diff       Show changes between commits, commit and working tree, etc
    merge      Join two or more development histories together
    rebase     Reapply commits on top of another base tip
    tag        Create, list, delete or verify a tag object signed with GPG

    collaborate (see also: git help workflows)
    fetch      Download objects and refs from another repository
    pull       Fetch from and integrate with another repository or a local branch
    push       Update remote refs along with associated objects

<br>

## `git-man`

- `git-man`: browse additional help on each command using the syntax `git help <command>`

      sudo apt-get install -y git-man

        Reading package lists... Done
        Building dependency tree       
        Reading state information... Done
        git-man is already the newest version (1:2.25.1-1ubuntu3.2).
        git-man set to manually installed.
        0 to upgrade, 0 to newly install, 0 to remove and 13 not to upgrade.


<br>

## Initialise a GIT repository

- `git init`: start a working area

        git init

        Initialised empty Git repository in /home/forest/Desktop/gitprojects/.git/

        ls -a
        .  ..  .git


<br>

- `git status`: show working tree status. 

        On branch master                <<--------------- Default branch

            No commits yet              <<--------------- Store changes in a local repository

            Untracked files: 
            (use "git add <file>..." to include in what will be committed)
                forest_story.txt

        nothing added to commit but untracked files present (use "git add" to track)

- `commit`: ssave the current state of the repository.
- `Untracked files`: GIT is waiting for your order on what to do with these files.
- `git add`
- `git commit -m "message"`

<br>

| Working area   | Staging Area   | Committed files   |
| -- | -- | -- |
| untracked files   | `git add` adds here.   | `git commit -m "Wrote a draft of the story"`   |

<br>

## GIT tips

- Make commits atomic: don't mix up multiple units of changes that are irrelevant to each other.
- Changes in one commit should be organic: the changes should be relevant to each other. 

Why is this important?

- In large projects, this keeps the development history clean. 
- GIT is a version control system. This helps you easily go back in time and revert to the version you want. 

<br>
<br>

## Hands-on practice 1 - initialise git
 
- `git init`

        mkdir forest-story-blog
        cd story-blog
        git init

        bash: cd..: command not found
        hint: Using 'master' as the name for the initial branch. This default branch name
        hint: is subject to change. To configure the initial branch name to use in all
        hint: of your new repositories, which will suppress this warning, call:
        hint: 
        hint:   git config --global init.defaultBranch <name>
        hint: 
        hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
        hint: 'development'. The just-created branch can be renamed via this command:
        hint: 
        hint:   git branch -m <name>

        Initialized empty Git repository in /home/sarah/learning-app-ecommerce/story-blog/.git/

<br>

- `.git`: hidden folder

        HEAD         branches     config       description  hooks        info         objects      refs

<br>

- `git status`: track the changes.

        On branch master

        No commits yet

        Untracked files:
        (use "git add <file>..." to include in what will be committed)
            forest_story.txt
            lion.txt

        nothing added to commit but untracked files present (use "git add" to track)

<br>

- `git add`: move working files to the staging area.

        git add forest_story.txt lion.txt
        git status
        
        On branch master

        No commits yet

        Changes to be committed:
        (use "git rm --cached <file>..." to unstage)
            new file:   forest_story.txt
           	new file:   lion.txt

<br>

- `git config user.email`: config user to make them the owner of the commits.
- `git config.user.name`


        git config user.email forest@gmail.com
        git config user.name forest

<br>

- `git commit -m "update message"`

        git status
        On branch master

        No commits yet

        Changes to be committed:
        (use "git rm --cached <file>..." to unstage)
            new file:   forest_story.txt
            new file:   lion.txt


        git commit -m 'added two stories'
        [master (root-commit) 88d9a67] added two stories
        2 files changed, 5 insertions(+)
        create mode 100644 forest_story.txt
        create mode 100644 lion.txt

<br>

- `git ignore`: intentionally untrack files. 

        git help ignore

        A gitignore file specifies intentionally untracked files that Git
        should ignore. Files already tracked by Git are not affected; see the
        NOTES below for details.

- `echo <file to ignore> >> .gitignore`

        ls -a
        .  ..  forest_story.txt  .git  lion.txt  personal_notes.txt

        echo personal_notes.txt >> .gitignore
        ls -a
        .  ..  forest_story.txt  .git  .gitignore  lion.txt  personal_notes.txt

- Check the status of the ignored file. 

        git status

        On branch master
        
        Untracked files:
        (use "git add <file>..." to include in what will be committed)
                .gitignore

        nothing added to commit but untracked files present (use "git add" to track)

<br>
<br>

### Team project

- identify the status of team repository.

        cd learning-app-ecommerce;ls
        README.md  assets     css        fonts      img        index.php  js         scss       vendors


        git status
        On branch master
        Your branch is ahead of 'origin/master' by 1 commit.
        (use "git push" to publish your local commits)

        Changes to be committed:                                 <---- In staging area
        (use "git restore --staged <file>..." to unstage)
                modified:   README.md

        Changes not staged for commit:                           <---- In working area
        (use "git add <file>..." to update what will be committed)
        (use "git restore <file>..." to discard changes in working directory)
                modified:   js/theme.js

- Commit team updates
  - 1 ready for commit, 1 modified.

        git status

        On branch master
        Your branch is ahead of 'origin/master' by 1 commit.
            (use "git push" to publish your local commits)

        Changes to be committed:
            (use "git restore --staged <file>..." to unstage)
                    modified:   README.md

        Changes not staged for commit:
            (use "git add <file>..." to update what will be committed)
            (use "git restore <file>..." to discard changes in working directory)
                    modified:   js/theme.js


  - `add` + `commit`

        git commit -m 'Add instructions for verification'

        [master b064afe] Add instructions for verification
        1 file changed, 1 insertion(+)        


        git add js/theme.js
        git commit -m 'Increase time from 400 to 500'
        
        [master a00718d] Increase time from 400 to 500
        1 file changed, 1 insertion(+), 1 deletion(-)
        
        
        git status
        On branch master
        Your branch is ahead of 'origin/master' by 3 commits.
        (use "git push" to publish your local commits)

        nothing to commit, working tree clean


- `.idea/`: .idea/ is a directory created by the IntelliJ Idea IDE. These are personal to the user so it is better to keep them from being tracked by git along with our code.


