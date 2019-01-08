---
title: git Notes
---

Chances are that you may need to know some git when using fastai - for example if you want to contribute to the project, or you want to undo some change in your code tree. This document has a variety of useful recipes that might be of help in your work.




## How to Make a Pull Request (PR)

While this guide is mostly suitable for creating PRs for any github project, it includes steps specific to the `KONE` project repositories. 

Focus is exlaining how use the Automation scripts

Disclaimer: Whereever we could, we 'stole' from fast.ai: https://docs.fast.ai/dev/git.html#how-to-make-a-pull-request-pr

The following instructions use `USERNAME` as a github username placeholder. The easiest way to follow this guide is to copy-n-paste the whole section into a file, replace `USERNAME` with your real username and then follow the steps.

Also don't get confused between the `fastai` github username, the `fastai` repository, and the `fastai` module directory, where the python code resides. The following url shows all three, in the order they have been mentioned:

```
https://github.com/Ukko/SFDC-MAIN/tree/master/awesomecode
                     |       |                  |
                 username reponame        modulename
```

### Automation

There is a smart [program](https://github.com/INSERTURL) that can do all the heavy lifting for you. Then you just need to do your work, commit changes and submit PR. To run it:

```
Copy the script KONE-make-pr-branch to your local machine
Ensure you have curl and Github (...) installed
## curl -O https://raw.githubusercontent.com/INSERTURL
chmod a+x fastai-make-pr-branch
./KONE-make-pr-branch https Ukko SFDC-MAIN awesome-feature
```

For more details run:
```
./KONE-make-pr-branch
```



#### Subsequent times

If you make a PR right after you made a fork of the original repository, the two repositories are aligned and you can easily create a PR. If time passes the original repository starts diverging from your fork, so when you work on your PRs you need to keep your master fork in sync with the original repository.

You can tell the state of your fork, by going to https://github.com/Ukko/SFDC-MAIN and seeing something like:

```
This branch is 331 commits behind fastai:master.
```

So, let's synchronize the two:

1. Place yourself in the `master` branch of the forked repository:

   * Either you go back to a repository you checked out earlier and switch to the `master` branch:

   ```
   cd KONE-fork
   git checkout master
   ```

   * or you make a new clone

   ```
   git clone git://github.com/Ukko/SFDC-MAIN.git KONE-fork
   cd KONE-fork
   git remote add upstream git@github.com:konecorp/SFDC-MAIN.git
   ```

     ```

   Use the https version https://github.com/Ukko/SFDC-MAIN if you don't have ssh configured with github.

2. Sync the forked repository with the original repository:

   ```
   git fetch upstream
   git checkout master
   git merge --no-edit upstream/master
   git push
   ```

   Now you can branch off this synced `master` branch.

   Validate that your fork is in sync with the original repository by going to https://github.com/Ukko/SFDC-MAIN and checking that it says:

   ```
   This branch is even with fastai:master.
   ```
   Now you can work on a new PR.


### Step 2. Create a Branch

It's very important that you **always work inside a branch**. If you make any commits into the `master` branch, you will not be able to make more than one PR at the same time, and you will not be able to synchronize your forked `master` branch with the original without doing a reset. If you made a mistake and committed to the `master` branch, it's not the end of the world, it's just that you made your life more complicated. This guide will explain how to deal with this situation.


1. Create a branch with any name you want, for example `new-feature-branch`, and switch to it. Then set this branch's upstream, so that you could do `git push` and other git commands without needing to pass any more arguments.

   ```
   git checkout -b new-feature-branch
   git push --set-upstream origin new-feature-branch
   ```

### Step 3. Write Your Code

This is where the magic happens.

Create new code, fix bugs, add/correct documentation.

### Step 4. Push Your Changes

1. When you're happy with the results, commit the new code:

   ```
   git commit -m"#12345 commit message for awesome code"
   ```
   where 12345 is the targy number of your task. With the # targy can identify the change and show it in the code section

   If you created new files, first tell git to track them:

   ```
   git add newfile1 newdir2 ...
   ```
   and then commit.

2. Finally, push the changes into the branch of your fork:

   ```
   git push
   ```

### Step 5. Submit Your PR

1. Go to github and make a new Pull Request:

   Usually, if you go to https://github.com/Ukko/SFDC-MAIN github will notice that you committed to a new branch and will offer you to make a PR, so you don't need to figure out how to do it.

   If for any reason it's not working, go to https://github.com/Ukko/SFDC-MAIN/tree/new-feature-branch (replace `new-feature-branch` with the real branch name, and click `[Pull Request]` in the right upper corner.

   If you work on several unrelated PRs, make different directories for each one, ideally using the same directory name as the branch name, to simplify things.


### How to Keep Your Feature Branch Up-to-date

If you synced the `master` branch with the original repository and you have feature branches that you're still working on, now you want to update those. For example to update your previously existing branch `my-cool-feature`:

   ```
   git checkout master
   git pull
   git checkout my-cool-feature
   ```

### How To Reset Your Forked Master Branch

If you haven't been careful to create a branch, and committed to the `master` branch of your forked repository, you no longer will be able to sync it with the original repository, without resetting it. And when you will want to create a branch, it'll have issues during PR, since it will be made against a diverged origin.

Of course, the brute-force approach is to go to github, delete your fork (which will delete any of the work you have done on this fork, including any branches, so be very careful if you decided to do that, since there will be no way to recover your data).

A much safer approach is to reset the `HEAD` of your forked `master` with the `HEAD` of the original repository:

If you haven't setup up the upstream, do it now:
   ```
   git remote add upstream git@github.com:konecorp/SFDC-MAIN.git
   ```

and then do the reset:
   ```
   git fetch upstream
   git update-ref refs/heads/master refs/remotes/upstream/master
   git checkout master
   git stash
   git reset --hard upstream/master
   git push origin master --force
   ```

### Where am I?

Now that you have the original repository, the forked repository and its branches how do you know which of the repository and the branch you are currently in?

* Which repository am I in?

   ```
   git config --get remote.origin.url | sed 's|^.*//||; s/.*@//; s/[^:/]\+[:/]//; s/.git$//'
   ```
   e.g.: `Ukko/SFDC-MAIN`

* Which branch am I on?

   ```
   git branch | sed -n '/\* /s///p'
   ```
   e.g.: `new-feature-branch7`

* Combined:

   ```
   echo $(git config --get remote.origin.url | sed 's|^.*//||; s/.*@//; s/[^:/]\+[:/]//; s/.git$//')/$(git branch | sed -n '/\* /s///p')
   ```
   e.g.: `Ukko/SFDC-MAIN/new-feature-branch7`

But that's not a very efficient process to constantly ask the system to tell you where you are. Why not make it automatic and integrate this into your bash prompt (assuming that use bash).

#### bash-git-prompt

Enter [`bash-git-prompt`](https://github.com/magicmonty/bash-git-prompt), which not only tells you which virtual environment you are in and which `username`, `repo`, `branch` you're on, but it also provides very useful visual indications on the state of your git checkout - how many files have changed, how many commits are waiting to be pushed, whether there are any upstream changes, and much more.

[EXAMPLE]


## hub

hub == hub helps you win at git

[`hub`](https://github.com/github/hub) is the command line GitHub. It provides integration between git and github in command line. One of the most useful commands is creating pull request by just typing `hub pull-request` in your terminal.

Installation:

There is a variety of [ways to install](https://github.com/github/hub#installation) this application (written in go), but the easiest is to download the latest binary for your platform at https://github.com/github/hub/releases/latest, un-archiving the package and running `./install`, for example for the `linux-64` build:

```
wget https://github.com/github/hub/releases/download/v2.5.1/hub-linux-amd64-2.5.1.tgz
tar -xvzf hub-linux-amd64-2.5.1.tgz
cd hub-linux-amd64-2.5.1
sudo ./install
```

You can add a prefix to install it to a different location, for example, under your home:

```
prefix=~ ./install
```


```
curl https://api.github.com/repos/github/hub/releases/latest
```
identifying user's platform, retrieving the corresponding to that platform package, unarchiving it, identifying the conda base as shown above, and running `install` with that prefix. If you work on it, please write it in python, so that windows users w/o bash could use it too. It'd go into `tools/hub-install` in the `fastai` repo.


