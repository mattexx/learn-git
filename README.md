# learn-git

This repository is for learning git.

## Prerequisites

1. [Git](https://git-for-windows.github.io/) installed. We'll access it with Git BASH.
2. Set up git config

        git config --global user.name "John Doe"
        git config --global user.email johndoe@example.com

## Git basics

Have a look at [Git basics](https://git-scm.com/book/en/v1/Getting-Started-Git-Basics) for
background relevant to the the following section.

## Create a repository

Go to [github](http://github.com/new) and create a repository. You can name it anything you want.
This one is called `learn-git` so that's what I'll use in the examples. Your new repository is
empty, so github will give show you instructions to get started.

    echo "# learn-git" >> README.md
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin git@github.com:mattexx/learn-git.git
    git push -u origin master

Whoa, what's all that? That's bash code. You can run it in Git BASH. (If you haven't set up git
config from the prerequisites, do it now.)

First create a folder for your project and make it the working directory. Name it the same as your
repository. This will be your local git repository.

    mkdir learn-git
    cd learn-git

The first command from github creates a file called `README.md` (a markdown format) with contents
"# learn-git" which is markdown for `<h1>learn-git</h1>` (substitute your project name for
learn-git).

    echo "# learn-git" >> README.md

The second command initializes a local git repository.

    git init

Now you can start using git! Let's see what `git status` tells us.

    On branch master

    Initial commit

    Untracked files:
      (use "git add <file>..." to include in what will be committed)

        README.md

So we are on branch master, we don't have any commits, and we have an untracked file. Let's add it.

    git add README.md

Now let's see what `git status` says.

    On branch master

    Initial commit

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)

        new file:   README.md

Changes to be committed are _staged_, meaning that they have changes that will be included in a
commit. Let's commit the changes.

    git commit -m "first commit"

Now with `git status`.

    On branch master
    nothing to commit, working tree clean

We made a commit! A commit is basically a record of a change. Think of it as a save button that
keeps a permanent record every time you hit it, rather than just saving the latest state. You can
browse your commits with `git log`. So far there's just one:

    commit 497796e0fa0e10258c281e3044224ac39e22b1ea
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Wed Feb 8 23:37:55 2017 -0600

        first commit

At this point we have made a commit to our local repository and could keep working locally, but why
not push it to github so we can share it? First we have to tell our local repository about the
remote repository on github.

    git remote add origin git@github.com:mattexx/learn-git.git

Now we can push our commit to github. We're pushing to "origin" which is defined in the command
above as our remote github repo. We pass the `-u` flag to tell git to create a new branch on origin,
and we're calling the brach "master." There isn't really anything special about the name "master,"
but by convention it's the branch that most projects consider the main branch. More on branching
later! For the moment let's push our commit to github and see what happens.

    git push -u origin master

Now refresh the project page on github, and the setup instructions should be replaced by your new
code! The readme displays rendered on the page. This is where you can put instructions for users and
devs (or perhaps a tutorial on using git!).

That's it! You've created a repository and made a commit. Congrats!

## Git branching

Have a look at [Git branching](https://git-scm.com/book/en/v1/Git-Branching-What-a-Branch-Is) for
background relevant to the following section.

## Using branches

A branch in git is like a branch on a tree. It's connected to the rest of the anatomy, but it can
grow independently. The purpose of branches is to allow developers to work on the code without
interfering with others and without letting others interfere with their work. Branches can be
"merged" back into the main branch (usually master) once the developers on the branch have completed
their work and are ready to share it with the rest of the project. Let's work through a short
example of this process. In this example we'll create a branch to modify the readme. Once we've
modified the readme independent of the master branch, we'll merge our branch back into master.

### Creating a branch

First let's check `git status`.

    On branch master
    nothing to commit, working tree clean

So our current branch is master. That means that any commits we make will go to that branch. We want
to work independently for a bit, so we'll create a branch. We can create a new branch and check it
out using git checkout:

    git checkout -b update-readme

The above code tells git to create a new branch called "update-readme" and check it out. Since we
were in branch master up to this point, it's equivalent to

    git checkout -b update-readme master

where master is the source branch. The source branch is assumed to be the current branch, so you can
leave that argument out if you are already on the source branch.

Now let's check `git status` again.

    On branch update-readme
    nothing to commit, working tree clean

We're on a new branch! Checking the output if `git log` will reveal that we're on the same commit.
In fact nothing has changed in our code, git is just tracking branch "update-readme" instead of
"master" now. Any commits we make will go into "update-readme" while master will remain untouched by
us for now. Add some text to `README.md` and save the file so we can commit the changes. Verify the
changes are there with `git status`.

    On branch update-readme
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README.md

We can also see the changes we made with `git diff`.

    diff --git a/README.md b/README.md
    index 41b7318..0ba0d4c 100644
    --- a/README.md
    +++ b/README.md
    @@ -1 +1,3 @@
     # learn-git
    +
    +This is a project to learn git.

### Brief interlude on `git diff` and `HEAD`

Now is a good time to talk about `HEAD`. `HEAD` is a pointer to the latest commit on the current
branch. Remember the hash that represented our commit? `HEAD` is basically a pointer to that hash,
since we haven't made any commits since. When we make a new commit, `HEAD` will point at that.

`git diff` without any arguments shows the difference between your current code and any staged or
committed code. That's what we just looked at, the unstaged changes we made to `README.md`.

`git diff HEAD` will show the difference between your current code and `HEAD`, so it will include
any changes that are staged but not committed. In this case it's equivalent to `git diff
497796e0fa0e10258c281e3044224ac39e22b1ea`.

You can also refer to immediately prior commits by appending `^` characters to `HEAD`. `HEAD^` is
the parent commit to `HEAD`. `HEAD^^` is the parent of `HEAD^` and so on. We'll use this
nomenclature pretty often since it's easier to type and more intuitive than hashes.

Finally, you can also use git diff to compare two commits regardless of the current state of your
code. For example, `git diff HEAD^ HEAD` will show the diff between your last commit and its parent
commit, regardless of the current state of the repository.

### Commiting to our branch

Now that we're armed with `git diff` and `HEAD`, let's finish the work on our branch. Remember we've
made a change to a file, but it's unstaged. Let's stage our changes.

    git add README.md

Now that we've staged our changes, to see the diff we'll have to specify `git diff HEAD` to see
them. (`git diff` won't show anything since it diffs to the staging area.)

    diff --git a/README.md b/README.md
    index 41b7318..0ba0d4c 100644
    --- a/README.md
    +++ b/README.md
    @@ -1 +1,3 @@
     # foobar
    +
    +This is a project to learn git.

Now we can commit our changes.

    git commit -m "update readme"

We have a branch with a new commit in it, Woot! Let's see our new commit with `git log`.

    commit 8d49123d7f01490df7a49dfbd909d670423373f1
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Thu Feb 9 13:39:55 2017 -0600

        update readme

    commit 497796e0fa0e10258c281e3044224ac39e22b1ea
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Wed Feb 8 23:37:55 2017 -0600

        first commit

Remember that this commit is only in branch "update-readme." It's only local right now, so let's
push it to github so other devs can help with it:

    git push -u origin update-readme

Now github has the branch with the new commit. You can browse the repository on github to see the
two branches and inspect the code for each one.

### Merging

A branch can contain as many commits as we want. Once we're happy with our branch, we merge it back
to master so it's part of the main project. This is a two step process. First we check out master to
merge to:

    git checkout master

Remember master is still at the initial commit, which you can verify with `git log`.

    commit 497796e0fa0e10258c281e3044224ac39e22b1ea
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Wed Feb 8 23:37:55 2017 -0600

        first commit

Then we merge in our branch:

    git merge update readme

We now have merged our code from our branch back to master. Excellent! We can confirm this with `git
log`.

    commit 8d49123d7f01490df7a49dfbd909d670423373f1
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Thu Feb 9 13:39:55 2017 -0600

        update readme

    commit 497796e0fa0e10258c281e3044224ac39e22b1ea
    Author: Matt Eckerle <meckerle@climate.com>
    Date:   Wed Feb 8 23:37:55 2017 -0600

        first commit

We can confirm the changes we just merged with `git diff HEAD^` which shows the difference between
current commited or staged code and the parent commit to `HEAD`.

    diff --git a/README.md b/README.md
    index 41b7318..0ba0d4c 100644
    --- a/README.md
    +++ b/README.md
    @@ -1 +1,3 @@
     # foobar
    +
    +This is a project to learn git.

At this point we have successfully branched, updated the code in the branch, pushed our pranch to
github, and merged our branch into master locally. The last thing for us to do is push our merge
commit to github's master branch.

    git push

At this point your code is in the master branch on the remote repository for anyone who wants it.
You've branched and merged in git. Woot!!!

### Deleting your branch

Even after merging, you can continue to work in your branch and merge as desired. Or if you are done
with your branch you can delete it. To delete your local branch:

    git branch -d update-readme

To delete your branch on the remote repository:

    git push origin :update-readme

## To be continued...

