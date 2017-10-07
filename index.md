## Introduction

The intention of this article is to provide basic details about Git workflow model implementation. 

## Git installation - Windows                          

1. Download Git. 
    
    There are few ways to install git on windows
    
    * Offical Git Website: [Download Link] (http://git-scm.com/download/win) - The download will start automatically.

    * Git for Windows - Separate from Offical Git: Download the latest [Git for Windows installer](https://git-for-windows.github.io/).
        
        
2. When you've successfully started the installer, you should see the Git Setup wizard screen. Follow the *Next* and *Finish* prompts to complete the installation. The default options are pretty sensible for most users.

    Recommendation: Elect to use Git Bash during installation.

3. Verify the Git version using below command.

    ```sh
    $ git --version
    ```

## Setup Git Environment                          

This setup is required only once on a give computer; they’ll stick around between upgrades. You can also change them at any time by running through the commands again.

1. Setup Identity

    The first thing you should do when you install Git is to set your user name and email address. This is important because every Git commit uses this information, and it’s immutably baked into the commits you start creating:
    
    ```sh
    $ git config --global user.name "Dupree Susan"
    $ git config --global user.email "susan@mail.com"
    ```
2. Setup Editor

    Vim, Emacs and Notepad++ are popular text editors, which can be configured as default text editor for Git.
    
    For Example: Configuring emacs text editor as default editor
    
    ```sh
    $ git config --global core.editor emacs
    ```

## Naming Convention                          

### General Guidelines

* Choose short and descriptive
* Use dashes to separate words.
* Use lower case.
* Do not use bare numbers as leading parts. Example: BUG198483
* Avoid long descriptive names.

### Branch Name Guidelines

* Use *slash* to mention supporting branch type.
* Pattern to follow: 
    < supporting-branch-type > / < reference-id > - < short-description >
    
    For examples:

```shell

    feature/e4823-login-signup
    feature/e19423-login-forgot-pwd
    hotfix/ms1023-xss-patch
    hotfix/bz2131-login-sqlchange
    release/v1.0.12
    bug/bz29823

```

## Git branching model                       

There are many ways to implement workflow in Git. We are going to discuss about the variant of [nvie's git branching workflow](http://nvie.com/posts/a-successful-git-branching-model/).

Note: Please refer [naming convention page](https://github.com/SarvM/git-work/blob/master/documentation/git-naming-convention.md) for more details on branch naming conventions.

### Main branch

These branches have an infinite lifetime

1. master branch
    
    * This branch always points to latest stable release (production-ready state). 
    * It's equivalent to release candidate in release process.
    
2. develop branch
    
    * This branch always points to latest development code.
    * It's equivalent to alpha state in release process.
        
### Supporting branch

These branches have limited life-time with specific purpose and it will be removed eventually once it's merged with corresponding branches.

1. feature branch
    
    * This branch is used to develop new features / enhance existing feature.
    * This branch should branch off from: *develop*.
    * This branch should merge back into: *develop*.

    Note: The feature branch should be merged into *develop* branch only after peer review, code review and QA Sign-off.
        
2. bug branch
    
    * This branch is used to fix the bugs in existing system.
    * This branch should branch off from: *develop*.
    * This branch should merge back into: *develop*.

    Note: The feature branch should be merged into *develop* branch only after peer review, code review and QA Sign-off.
        
3. release branch
    
    * It's equivalent to beta state in release process.
    * This branch is used for UAT.
    * Issues will be fixed in this branch. 
    * This brach should branch off from: *develop*
    * This branch should merge back into: *master* and *develop*
    * The master branch merge commit will be tagged with the new version number.
        
4. hotfix branch

    * This branch is used to fix P1 issues.
    * This branch should branch off from: *master*
    * This branch should merge back into: *master* and *develop*

## Git commands - Workflow                          

Below commands may be used to achieve the branching model explained in previous topic.

### Scenario

1. Below freelancers are working for an experimental project called 'pratice-git'

    * Developers: Lisa and Rachel
    * Developer Lead: Sophie
    * Admin: Erika 

2. Below task is assigned to each of them:

    * Erika - Setup existing project in git, create *develop* branch
    * Lisa - Signup API -- api change; Enhancement Reference # IME02384
    * Sophie - Signup API -- code reviewer and responsible for releasing the task to QA
    * Rachel - Fixing bug on login page; Bug Reference # BZ2948
    
3. Release Process - Tag this enhancement, feature and bug fix in single release with tag name v1.0.1


#### Git commands

Please refer [git useful commands](https://github.com/SarvM/git-work/blob/master/documentation/git-useful-commands.md) for more details on below commands.

##### Initialize the project

This command is used by admin to initialize the project in server and to setup *develop* branch. 

Note: This section is not required for developers.

```sh

Erika used below commands to finish her task.

$ git init 

$ git commit --allow-empty -m "Initial commit"

$ git checkout -b develop master

$ git push origin develop

```

##### Clone the project 

To copy a git repository. First, clone a remote Git repository and cd into it:

```sh

Lisa and Rachel copies a git repository to their local to finish the work assigned to them.

$ git clone -b develop --single-branch http://immidart-git.azurewebsites.net/pratice-git.git

$ cd pratice-git/

$ git config user.name "lisa"

$ git config user.email "lisa@mail.com"

```

Note: The config entry *user.name* and *user.email* is used to set user name and user email for this repository. 

Next, look at the local branches in your repository:

```sh

$ git branch
    
  *develop

```

There are remote branches hiding in your repository. Use below command to view it. 

```sh

$ git branch -a

 *develop

  remotes/origin/develop

```

##### Create branch in local and push to remote for tracking

This branch is used by developer to fix the bug or enhance the feature without disturbing the develop branch. 

```sh

$ git checkout -b feature/signup-api develop

$ git push -u origin feature/signup-api

```

Now, the branch is available in both local and remote for tracking.

* Best Practice: 

    * The developer should update local develop branch with remote branch.
    * The feature branch should be pushed periodically to remote -- it acts like a backup.
    
Back to story. While lisa enhancing the feature, Rachel has fixed the login page issue. Hence, Sophie has to review it and merge it to develop so that it will be available for Lisa.


##### Clone and create branch for review

* Sophie need to copy the repository. This step is not required if the repository is already setup.
* The repository should be updated with latest changes

```sh

$ git clone http://immidart-git.azurewebsites.net/pratice-git.git

$ cd pratice-git/

$ git checkout -b  bug/bz2948 origin/bug/bz2948

```

The branch bug/bz2948 should have all changes committed by Rachel. 

Sophie modified few lines of code and committed to the same branch for Rachel to take it further. 

```sh

$ git status // To identify the files modified by her

$ git add . // To add the files for stracking

$ git commit -m "Code Reviewed. Modified few lines" 

$ git push origin bug/bz2948

```

##### Pull the latest code and Merge the files if required.

Now, Rachel need to pull the latest code and do merge if required and push it to develop. 

```sh

$ git pull // To pull the latest code 

$ git log --oneline -5 // To understand the commit 

```

Time to merge the changes with develop branch. 

```sh

$ git checkout develop // This is local branch

$ git merge --no-ff bug/bz2948 // Merge the changes to local develop branch

$ git push origin develop // All changes are pushed to develop branch

$ git branch -d bug/bz2948 // To delete the local branch

$ git push origin --delete bug/bz2948 // To delete the remote branch from server

```

* Best Practices:

    * It is recommended to use the option * --no-ff * while merging. 
    * Delete the feature/hotfix/bug/release branch once it is merged with corresponding main branches.
    
Well, Now Lisa need to update the branch to have the changes done by Rachel. 

```sh

$ git checkout develop // switch from feature branch to develop branch to pull latest changes

$ git pull origin develop // All latest changes available in develop branch is pulled from server to local

$ git checkout feature/signup-api // switch to feature branch

$ git merge --no-ff develop // all changes available in local develop branch is merged with future branch

```

Now, Lisa has done with the work. Hence, she will follow the same process:

1. Push the code for review
2. Code review from developer lead
3. Pull the latest changes
4. Merge with local develop branch
5. Push the changes to remote develop branch
6. Delete feature branch from local and remote

```sh

$ git status // To know the status of tracked files

$ git add . // Stage the modified files.

$ git commit -m "Feature enhanced and reviewed by developer lead"

$ git checkout develop

$ git merge --no-ff feature/signup-api

$ git push origin develop // push the changes to remote develop branch

$ git branch -d feature/signup-api  // delete the local branch

$ git push origin --delete feature/signup-api  // delete the remote branch 

```

##### Create Release

Now, Sophie is going  to create Release branch with all the bug fixes and enhancement for QA / Integration testing. 

```sh

$ git checkout -b develop origin/develop // incase the *develop* branch is not available in release admin system

$ git pull origin develop // just to make sure the *develop* branch is having latest code available.

$ git checkout -b release/v1.0.12 develop // create release branch from local develop branch

$ git push origin release/v1.0.12 // push this release branch to remote for tracking -- deploy this branch for testing. 

$ git commit -m "Commit the version v1.0.12 // committing all bug fixes and make it ready for production ready. 


```

##### Create tag for release

The code is now ready for production release. Hence, perform below:

1. Merge the release branch with Master and develop branch. 
2. Tag the master branch for release point.
3. Delete the release branch.

```sh

$ git checkout master

$ git pull origin master // just to make sure the master branch is up-to-date

$ git merge --no-ff release/v1.0.12 // merge the release branch with master branch locally

$ git push origin master // push the changes to remote master

$ git checkout develop

$ git merge --no-ff release/v1.0.12  // to merge the release branch with develop branch locally

$ git push origin develop  // push the change to remote develop branch -- so that other developer can push the latest code

$ git checkout master  // switch to master branch to tag 

$ git tag -a v1.0.12 -m "enterprise version v1.0.12 ready"

$ git push origin v1.0.12  // push the tag to remote/server

$ git branch -d release/v1.0.12  // delete the branch locally

$ git push origin --delete release/v1.0.12  // delete the remote release branch 

```

Graphical representation of git commits -- used gitk to produce this image.

![gitk - releasev1.0.12](/documentation/img/gitk-release-v1.0.12.png?raw=true "gitk - releasev1.0.12")


## Useful Git Commands 

1. git init

    * Create an empty Git repository or reinitialize an existing project
    * It is safe to run this command on existing directory
    
2. git commit --allow-empty -m "Initial commit"

    * Just to have initial commit to server before we create a *develop* branch
    * Option *--allow-empty* allow us to override safety commit because git won't allow us to commit without changes to the project
    
3. git checkout -b develop master

    * Create and checkout develop branch from master. 
    
4. git push origin develop

    * This command is used to push develop branch to remote *origin* for remote tracking. 
    
5. git status --short

    * This command is used to output the working status of the tree. 
    * --short is used to output the result in short format.

6. git clone *[url]*

    * If you need to collaborate with someone on a project, or if you want to get a copy of a project so you can look at or use the code, you will clone it. You simply run the git clone [url] command with the URL of the project you want to copy.
    
7. git log --oneline -2

    * This command is used to display the commits
    * The output can be altered by using sub commands. *--oneline* is used to display the output in single line. 
    * sub command *-2* is used to display last 2 commits.
    
8. logout

    * This command is used to exit the bash command line. 
    
9. git branch -d {the_local_branch}

    * To remove local branch from your machine.
    * Use -D instead to force deletion without checking merged status
    
10. git push origin --delete {the_remote_branch}

    * To remove remote branch from your server.
    
11. git merge --abort 

    * To backout of the merge
    
12. git reset --hard *< commit number >*

    * To rever the repository back to commit mentioned.
    
13. rm .git/index.lock

    * Above command can be used to fix below issue, which occurs while merging. 
    
    ```sh 
    
    fatal: Unable to create '/path/to/repo/.git/index.lock': File exists.

    If no other git process is currently running, this probably means a
    git process crashed in this repository earlier. Make sure no other git
    process is running and remove the file manually to continue.
    ```
    
14. git clean  -d  -fx

    * To delete untracked files
    
15. git rm --cached -r [folder/file name]

    * To clean tracked file mentioned in .gitignore file 
    
16. Use below commands to squeeze all intermediate commit to single commit along with message while merging it with actual branch (not feature or bug branch)

    ```sh
    git checkout develop
    
    git merge --squash feature/f10293-data-grid-css // This command will merge all the changes
    
    git add . // Stage all the files merged from feature branch
    
    git commit -m "Give meaningful commit message"
     
    ```

Enjoy!!
