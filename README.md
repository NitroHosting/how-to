# About

GitHub is an amazing tool for collaborating on projects. It allows people to review code, stage code, and publish code. In NitroHosting, all our projects will be managed by version control like Git. Because of how easy and rich-featured GitHub is, we chose to use GitHub as  one of our daily project tool.

A lot of IDEs and editors offer graphical solution to Git management. But I recommend you to read this guide carefully as you will understand how Git works.

In below, you will learn what you need to do or should do to keep our projects organized. Feel free to check out this repository whenever you feel lost and needed a guide.

# Notice

This guide is written for general linux environment. For commands that is not available on Windows/MacOS, I will be including an alternative command for different operating system at the bottom of this guide. However on MacOS, **most** commands will work as normal. As for Windows, it simply sucks *(I wouldn't even bother to code on Windows)*. So deal with it.

Please open an issue if you feel like any command is missing or should be included.

# Before we continue

You are assumed to have `Git` and `OpenSSH` installed. If you do not have one, please stop reading and install them immediately.

> **Debian/Ubuntu:** sudo apt install git openssh-client
> 
> **Old CentOS/RHEL:** sudo yum install git openssh-client
> 
> **New CentOS/RHEL:** sudo dnf install git openssh-client
> 
>  **Arch Based Linux:** sudo pacman -S git openssh
> 
>   **MacOS:** sudo brew install git openssh-client

For windows, please follow the instructions in the links below:

> Git: [https://git-scm.com/download/win](https://git-scm.com/download/win)
> 
> OpenSSH: [https://pureinfotech.com/install-openssh-client-windows-10/](https://pureinfotech.com/install-openssh-client-windows-10/)

Now that you have installed `Git` and `OpenSSH`, check if they are working, type in the commands below:

> git --version
> 
> ssh -V

If they responded nothing or returned command not found, it means you did it incorrectly. This is a very basic installation process requiring zero skill level, we will not provide further assist on debugging your installation if they failed. Please search for solution on the internet according to your situation. Or just search for installation document on their official website.

# Adding SSH key to your GitHub

Git has a verification process when pushing code to a repository, making sure you have access to the repository. You can use password to login **everytime** you want to do something, but wouldn't it be too much of a trouble? SSH keys provides better encryption and verification strength, and you don't have to input your password at all. Here is what we are going to do:

1. Open your console/terminal and run `ssh-keygen` to generate a pair of SSH key. It will ask you to save in where, we suggest to save in your default ssh folder with the name "github". 
2. Go to GitHub settings, then click "SSH and GPG keys", and click "New SSH key" on the top right corner. Or directly use [this link](https://github.com/settings/keys). *(Ctrl click to open in new tab to continue reading this guide)*.
3. Then, run `cat ~/.ssh/github.pub` in your console, copy the content to your clipboard and paste it to the "Key" section on the previous step. Remember, the file path depends on your installation. So basically copy the public key file content.
4. Give it a title, you can name it whatever you want. For proper key management, you should name it so you can regconize it of which computer/server of yours.
5. Click the "Add SSH key" button and you should see your key added on GitHub.

In some situation, git will not use the key file for verification without manually setting it in git config. But we will not be covering git config today, instead we offer you a different solution:

Run `vim ~/.ssh/config` or `nano ~/.ssh/config`, and paste this in the head of the file:
```
HOST github.com
        User git
        Hostname github.com
        PreferredAuthentications publickey
        identityFile /home/USERNAME/.ssh/github
```
Replace the `identityFile` part with your SSH private key location.

This is the easiest solution, however it limits your machine to only use that specific key when connecting to GitHub. Search for another solution on the internet if you need to manage multiple accounts on GitHub.

**WARNING: DO NOT USE THE SAME KEY ON MULTIPLE GITHUB ACCOUNT**

# Cloning the repository to local

To start, we need to clone the repository with the command `git clone`. Head to the repository page on GitHub, and click the green button "Code". You should now see a panel dropped down. If the title is "Clone with SSH" then simply copy the link there. If the title is "Clone with HTTPS", just click "Use SSH" on the right side and do the same above.

Now you have the SSH link copied, head over to the folder you want to work on *(The project's parent folder, not the project folder)*. In this example, I will be using `~/Projects`. Run `git clone <url>`, replace `url` with the content you just copied. Now if I clone this how-to repository, my local copy of the repo would be `~/Projects/how-to/`.

# Preparing your work environment
*Assuming you curent console directory is your local copy of a repository, you can do that via `cd <folder>` replacing `folder` with a path.*

If you did not clone the repository via `git clone`, then you must add a remote for git. Do `git remote add <name> <url>`, we suggest naming it `origin` (We will use `origin` as the remote name in this guide). As always, url is the SSH link to the repository. If you are unsure, you can always use `git remote` to view the list of name of the remotes. And `git remote -v` to view the name and url of the remotes *(-v stands for Verbose if you would like to know)*.

> **To remove a remote, simply use** `git remove <name>`.
> 
> **To rename a remote, use** `git rename <old_name> <new_name>`.

# Everytime you start working

### Don't do it in master, use something else

We generally use the `master` branch for verified changes. Anyone that has actual experience on working with a large team should know that they shall never directly commit/push to the master branch. We usually create another branch when working on a feature, then we use that submit a pull request on our newly created and finished branch for admins to review and add your code to `master`.

> **To create a branch, you can use** `git branch <branch_name>`.
> 
> **To switch to a branch, use** `git checkout <branch_name>`.

If you feel like it is so annoying to run two commands just to create and switch branch, you can do `git checkout -b <branch_name>`. This would basically create a branch and switch you to the new branch. 



### Fetching latest remote commits

Everytime you are going to work on something, you wouldn't want to mess up the repository and ended up not being able to push your code right?

So first, you want to pull all changes made on remote to your local machine, so you won't have any conflict when pushing your code to it. Normally, you would only need to run `git fetch`, but you can also use the commands below:

> **Fetch from a remote (all branches):** `git fetch <remote>`.
> 
> **Fetch from a branch:** `git fetch <remote> <branch>`.
> 
> **Fetch from all remotes (POWER MOVE):** `git fetch --all`.

`git fetch` will **ONLY** fetch the latest meta-data from the remote, it **WILL NOT** update any of your file. It is very harmless and it is for us to see if there is any changes made since our last commit/push. But that is not enough, isn't it?

### Aggressive mode to fetch/pull changes
If you don't care about your local **uncommitted** changes at all *(which is fine by us since you are the only one who will lose stuff)*, you can run `git pull`. The `git pull` command is basically `fetch and merge`. This will make git try to merge all remote changes with your local one. **HOWEVER**, "merge conflict" can occur in the situation if you have any uncommitted local change. You can use `git stash` to save your local changes temporary.

### Stashing your changes temporary

`git stash` will copy all your local changes to another place and then remove all local changes from your local files. Restoring a stash can be as easy as running `git stash apply <stash_name>`. Running `git stash pop` will restore your **latest** stash and will clear that stash from your stash history. Do not confuse this with any other command, as this is only a **stash**, meaning it is temporary and can be wiped with a single command `git stash clear`and it will be gone forever. `git stash` is useful when checking out a different branch or even when pulling/merging/rebasing remote changes. You should not use `git stash` too often as it is a bad practice.

# Merging changes to your branch

When someone has done something on the branch you forked from, like the master branch, you would most likely want your code to stay up to date. Like you are working an additional feature, but someone has pushed an API change to `master`, then you would need to merge `master` into your branch making sure you can use the code and the history will not conflict. For this, you need `git merge <branch>`. Before you run that, you want to make sure you are really on the branch you are working on by running `git branch`, the starred branch is the branch you are currently on. If I do `git merge master`, it will merge changes in `master` to my branch, so I can easily push it in the future without any conflict.

In very rare situation, `git merge` just don't work for you. Then you can use `git rebase`. Rebasing means you will be **rewriting the history of the branch**. If I do `git rebase master`, then it would create a new commit for **every** commit `master` had, and completely merge the `master` branch to yours. I wouldn't suggest anyone to use `git rebase` if they don't understand it well. You can do almost everything you need with `git merge` (and it wouldn't cause anything serious), but `git rebase` can cause catastrophic damage to the entire repository and workflow. Also rebasing loses the context provided by a merge commit, and you won't be able to see when upstream changes were incorporated into the branch.

I would suggest everyone to use `git merge` whenever it is possible. `git rebase` is **EXTREMELY DANGEROUS AND AGGRESSIVE** and it will be hard to fix. So, please do not use `git rebase` if you have no idea what you are doing.

# How to see which branch I am on?
Actually, if you know how to check branches, it means you actually read the stuff I wrote above. But I am still going to write it one more time for people who want quick access by browsing through titles.

To list branches *(Starred branch is the branch you are working on currently)*:

> `git branch`

To change your current working branch, run:

> `git checkout <branch>`

To create a branch then change your current branch to that (By default it base your new branch off your current `HEAD`, you can specify which branch it base off of with an additional argument):

> `git checkout -b <new_branch>`
> 
> `git checkout -b <new_branch> <old_branch>`

### What is HEAD? And why I see "detached HEAD"

`HEAD` is just a way for Git to refer to the current snapshot. Think of `HEAD` as the "current branch" *(In case you want to know, the actual meaning, `HEAD` is a reference to a commit object.)*. For example, the `git checkout` command is just updating the `HEAD` to point to a branch/commit.

`detached HEAD` will occur when you check out a commit. It means that everything you are doing from that point is "detached" from the rest of the project. If you are on `detached HEAD`, there will be no branch for you to get back to it, aka when you check out another branch to merge, there will be no way to reference your feature as there is no branch for it. You should never develop on a `detached HEAD`, or else you will have no reference to your new commits. But if you are just looking at old commits, this warning doesn't matter as you are not making changes. 

# Start working on the project

Once you have made sure all remote changes are pulled or merged to your local, and made a new branch for your new feature, you can finally start working. 

After hours and hours of coding...

### Shit! Why can't I commit my new files???
Simply because in most version control system, saving a file isn't just as simple as hitting the save button or ctrl+s. You have to stage (add) the file to Git. The following commands might seem similar, but they are actually very different:

> **Stage one file/folder only:** `git add <path>`
> 
> **Stage all changes:** `git add -A`
> 
> **Stage all changes except file deletions:** `git add .`
> 
> **Stage all changes except new files:** `git add -u`

Most modern and professional IDEs like JetBrains will have a feature called `File Watcher` which will watch for any file changes. They usually implement a full Git solution where you don't have to stage changes, they will stage it for you whenever you create/delete/modify a file.

### Now how do I commit?

You can do `git commit`, after doing that you will see your console pop up bunch of text and your code. It is the changes you made, after verifying, you have to type in a commit message to commit it. Sure you can read it, but isn't it just too much? Lucky for you, there are some important options for this command that can help.

#### Not specifying a commit message is stupid

I have seen people not being able to write a changelog or a description of what they did in that commit. It is a big no no for collaborating, please explain what your commit does, unless you are incompetent of typing.
Also remember to surround your message with a pair of quotes as the `message` argument takes a string.

> `git commit -m "<message>"`

#### Don't track/commit that file!

Sometimes you just need a file to be excluded, create a file called `.gitignore` in your project root, and add the file/folder path to it. It takes relative path, so `randomFile.php` in `.gitignore` will be `/projectRoot/randomFile.php`

"Wait, after I've done that... `git commit` is still committing the file?"
Like all modern apps, Git has cache. You need to clear the cache on specific file by doing this:

> `git rm --cache <file_name>`

#### Time for the long awaited push!

After committing files, you can push the file to remote finally! You can push commits by doing `git push <remote> <branch>`. In our example, the `remote` would be `origin`, and `branch` would be the branch you are currently working on. This will push the branch to remote.

Pushing the code to a branch doesn't mean it can't be changed anymore. You can commit and push again to the same branch over and over again.

#### Pull request? How does it work?

Pull request is basically you requesting the admin/manager to pull/merge your code to `master` in most situations. After finishing a part of the code, maybe a feature, you can push the code and submit a pull request on the GitHub repository page. You can find the section on the repository navigation bar, go in and click `New Pull Request` on the top right corner (Big green button, hard to miss). You can choose the `base` and the `compare` branches. For example, if I choose `**base:** master` and `**compare:** feature-17`, it means to pull changes in branch `feature-17` to `master`, aka adding "Feature 17" to `master`.

# Short sum up on how you should work

 1. Fetch/Pull the repository.
 2. Edit files.
 3. Commit to local repository *(`git commit` won't commit to remote except when it is pushed)*.
 4. Fetch/Pull it again (If there are any conflict you will be notified), in that case you can solve it by merging.

# Extra tips

### Setting an upstream branch

In theory, local and remote branches in Git are completely separate items. Thing will start to makes lots of sense when you see them as counterparts - connected in a so-called "tracking connection". Setting a upstream branch will mainly benefit you by saving your time, allowing you to pull/push without typing the `remote` and `branch`. [This article](https://www.git-tower.com/learn/git/faq/set-upstream) will let you understand more about tracking and upstream branch. 

# Alternative command list

### cat
> **Windows:** type
> 
> **MacOS:** *Same with Linux*

### vim / nano
> **Windows:** edit / notepad
> 
> **Mac:** *Same with Linux*
