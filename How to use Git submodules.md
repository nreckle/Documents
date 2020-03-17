## How to use Git submodules  

**Git submodules are a way of linking to a repository inside another**. For example, if your project needs to use someone else's project that's hosted on Github then you can use it as a submodule rather than  including all the code from that project. This has the added benefit of  being able to track and include changes from the submodule repo, rather  than having to update project files yourself.

*This tutorial will show you:*

- *How to add a submodule to your project*
- *How to clone repositories with submodules*
- *How to update submodules*
- *How to manage submodules within submodules (recursive submodules)*
- *How to remove submodules (or what to do if it all goes wrong)*

I know that last point is slightly ominous, but we'll get to that.

## Setting Up

For this tutorial you'll need a Github account. You probably do  already, otherwise you wouldn't be reading this far in. You can either  create a repository, or you can just fork my example project, which you  can do at  https://github.com/joonty/example. Skip the rest of this section if you are comfortable with forking a repository and cloning into a local copy.

Otherwise, click on the "Fork" button at the top right of the screen, which looks like this:

![Github fork repository](http://blog.joncairns.com/wp-content/uploads/2011/10/github-fork.jpg)

This will create a copy of the example repo in your account. Then, clone the repo, replacing *(username)* with your actual username:

```
jcairns$ git clone git@github.com:(username)/example.git submodule-example
```

This will clone your version of the project into the directory  'submodule-example'. Move into this directory and you are ready to go.

## Adding a submodule

My example project is a high-powered web application. It's range of features include:

1. Printing out "Hello, World" as a header

Hmm. Seems to be all it does. Anyway, this high-powered web  application is going to need a good ORM to help it function efficiently, so we'll use PHP Doctrine. Doctrine2 is [available on github](https://github.com/doctrine/doctrine2), so it's the perfect example. To add a submodule you use the command `git submodule add`:

```
jcairns$ git submodule add (repository) (directory)
```

This would register the repository as a submodule under the directory specified. Therefore, in the context of this example, we want to add  the Doctrine repository as a submodule in the directory 'doctrine',  using `git submodule add`:

```
jcairns$ git submodule add git://github.com/doctrine/doctrine2.git doctrine
```

This will register doctrine as a submodule within your project and  will clone the data into the 'doctrine' directory. The command `git submodule status` will tell you that the submodule has been registered, and which commit it is pointing to in the Doctrine repository:

```
jcairns$ git submodule status
-15877e14430a316a7576918bc7c996e52d91105d doctrine
```

Also, to see what changes have been registered in the parent repository, use the `git status` command:

```
jcairns$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD ..." to unstage)
#
#   new file:   .gitmodules
#   new file:   doctrine
#
```

This shows us that two "files" have been modified: '.gitmodules',  which holds information about the repository's submodules; and  'doctrine', which is the submodule itself. Git doesn't track files  inside the submodule when you're in the parent repository, it just sees  it as a single file. We'll touch on that a bit more later. First, we  want to save our changes and push it to the remote repository:

### Commit Changes

```
jcairns$ git commit -m 'Added Doctrine submodule'
[master 37f40bb] Added Doctrine submodule
 2 files changed, 4 insertions(+), 0 deletions(-)
 create mode 100644 .gitmodules
 create mode 160000 doctrine
```

### Push to Remote

```
jcairns$ git push
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 433 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:joonty/example.git
   223e6ba..37f40bb  master -> master
```

If you look at your repository on Github you will be able to see a  link to the Doctrine repository in your parent repository, which will  look like this:

![Github submodule link](http://blog.joncairns.com/wp-content/uploads/2011/10/github-submodule.jpg) 
 *Doctrine submodule link in Github*

Congratulations! You have added a submodule. Now let's look at cloning repositories that contain submodules.

## Cloning a repository with submodules

Let's try cloning the repository you've been working on into a  different directory. Move to a directory above the one you're currently  in, and run:

```
jcairns$ git clone git@github.com:(username)/example.git submodule-clone-example
```

Again, make sure you replace *(username)* with your actual  username. This will clone the example project to the directory  'submodule-clone-example' - move into this directory. If you take a look inside the 'doctrine' directory, which is meant to contain our  submodule, you'll see that it's empty. What's happened? Has git given  up?

No. By default, a **git clone** won't pull the submodules along with the parent repository. To pull the Doctrine submodule, you need to run `git submodule init` and then `git submodule update`:

### Submodule Init

```
jcairns$ git submodule init
Submodule 'doctrine' (git://github.com/doctrine/doctrine2.git) registered for path 'doctrine'
```

This registers the submodule in the correct path, 'doctrine', but doesn't clone the code.

### Submodule Update

```
jcairns$ git submodule update
Submodule 'doctrine' (git://github.com/doctrine/doctrine2.git) registered for path 'doctrine'
Jonathan-Cairns-iMac:submodule-example jcairns$ git submodule update
Cloning into doctrine...
remote: Counting objects: 63339, done.
remote: Compressing objects: 100% (16009/16009), done.
remote: Total 63339 (delta 44444), reused 62836 (delta 43982)
Receiving objects: 100% (63339/63339), 16.16 MiB | 712 KiB/s, done.
Resolving deltas: 100% (44444/44444), done.
Submodule path 'doctrine': checked out '15877e14430a316a7576918bc7c996e52d91105d'
```

This actually pulls the code for the registered submodules. You have  now got a working copy of your repository, submodules intact.

## Updating a submodule

As I've said before, submodules are simply Git repositories inside  another: there's nothing magical about them. Therefore, as long as you  are in the submodule directory, all the normal Git actions like `push`, `pull`, `reset`, `log`, `status`, etc. work as normal. If you want to make sure that your submodule is bang up to date with the remote repository, simply do a `git pull` (don't use `submodule`!) when you're in the submodule directory:

```
jcairns$ cd doctrine
jcairns$ git pull
Already up-to-date.
```

If it looks like you're getting an error message saying that you aren't on any branch, do a `git checkout master` to fix it.

If your submodule did have some updates when you did a pull then your parent repository will be telling you that there are some changes to  commit. The submodule is pointing to a particular commit, and if the  commit changes then the parent repository registers the change. If your  submodule needs to be at a particular commit to work with your  repository you can set it using `git reset`:

```
jcairns$ git reset --hard (commit hash)
```

For example, if we want to fix our version of Doctrine to the most recent tag (currently 2.1.2), we would just need to do:

```
jcairns$ cd doctrine
jcairns$ git reset --hard 144d0de
HEAD is now at 144d0de Release 2.1.2
```

We can then go back to the parent directory, and commit it:

```
jcairns$ cd ..
jcairns$ git commit -am 'Set doctrine version to 2.1.2'
```

When this is pushed to the remote repository the submodule will be  associated with that particular commit. It's worth mentioning that, if  you have others working on the same project, someone else could pull on  the submodule and commit the change, thereby changing the submodule  commit. But that's easily fixed - just do the reset again.

It's also worth noting that, if your submodule is a repository that  you have push access to, you can edit, commit and push submodule content completely independently from your parent repository. The only thing  that affects the parent is when the submodule is pointing to a different commit, as mentioned previously.

## Submodules within submodules

One great feature about submodules is that they can contain  submodules themselves. For instance, the Doctrine repository has several submodules, including [doctrine-common](https://github.com/doctrine/common) and [dbal](https://github.com/doctrine/dbal).

Let's go back to the example repository. Currently, Doctrine is  registered as a submodule, but none of its submodules have been pulled.  In the *doctrine* directory, type `git submodule status`:

```
jcairns$ git submodule status
-3762cec59aaecf1e55ed92b2b0b3e7f2d602d09a lib/vendor/Symfony/Component/Console
-c3e1d03effe339de6940f69a4d0278ea34665702 lib/vendor/Symfony/Component/Yaml
-ef431a14852d7e8f2d0ea789487509ab266e5ce2 lib/vendor/doctrine-common
-f91395b6f469b5076f52fefd64574c443b076485 lib/vendor/doctrine-dbal
```

This shows that there are four submodules in the doctrine repository. The "-" sign at the beginning of each line shows that the submodules  haven't been downloaded yet. We can run `git init` and then `git update`, which would clone all the repositories and put them into the right  place. But what about when we're cloning our repository to another  machine? It would get pretty tedious to have to go through all the  submodules, performing an `init` and `update` as deep as we need.

Fortunately, we can get round this at the point of cloning. We can just use the `--recursive` option when doing a `git clone`:

```
jcairns$ git clone --recursive (repository)
```

This initialises and pulls all submodules, recursively. Simple!

## Removing submodules

Sometimes you will decide that you no longer need a submodule. Or you may find, as I have on several occasions, that a submodule seems to get corrupted, and the best thing may be to remove it and start again.

Unfortunately, this isn't as simple as doing something like `git rm -rf [submodule directory]`. If you do that, git will complain that it can't match any files. To remove a submodule, you have to edit the *.gitmodules* file that's sitting in the root folder of the project (not the submodule directory).

Open this file in your favourite editor, and you will see the following:

```
[submodule "doctrine"]
    path = doctrine
    url = git://github.com/doctrine/doctrine2.git
```

If you have multiple submodules then you will see multiple entries,  where each entry takes up three lines. For instance, since the doctrine  project has submodules it also has a *.gitmodules* file, which looks like this:

```
[submodule "lib/vendor/doctrine-common"]
    path = lib/vendor/doctrine-common
    url = git://github.com/doctrine/common.git
[submodule "lib/vendor/doctrine-dbal"]
    path = lib/vendor/doctrine-dbal
    url = git://github.com/doctrine/dbal.git
[submodule "lib/vendor/Symfony/Component/Console"]
    path = lib/vendor/Symfony/Component/Console
    url = git://github.com/symfony/Console.git
[submodule "lib/vendor/Symfony/Component/Yaml"]
    path = lib/vendor/Symfony/Component/Yaml
    url = git://github.com/symfony/Yaml.git
```

To remove a submodule, simply delete the three lines associated with  it. In our case, as there is only one submodule, you can delete the  entire contents of the file or the file itself. When you next commit,  you will be asked to commit your changes to the *.gitconfig*.  Doing this will remove the submodule from the remote repository, and  anyone else who clones it from that point won't ever see that submodule.

However, the submodule is still hanging around in your local repository. To demonstrate this, if you run `git submodule status` you get a warning:

```
jcairns$ git submodule status
No submodule mapping found in .gitmodules for path 'doctrine'
```

Uh-oh, looks like it's screwed up. Well it is, but by no means beyond repair. The problem is that the *.gitmodules* file isn't the only place where there's a reference to the doctrine submodule. Submodules are also recorded in the file *.git/config*, which looks like this:

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    fetch = +refs/heads/*:refs/remotes/origin/*
    url = git@github.com:joonty/example.git
[branch "master"]
    remote = origin
    merge = refs/heads/master
[submodule "doctrine"]
    url = git://github.com/doctrine/doctrine2.git
```

The reference to the submodule is sitting at the bottom of the file.  Delete those two lines to remove it from your local repository (you  might need to do this as the administrator, e.g. sudo).

Have we finished yet? Almost. You now need to remove the git cache of the submodule, which you can do with `git rm --cached `. In our case that will be:

```
jcairns$ git rm --cached doctrine
```

**There is no trailing slash on the submodule directory**. The submodule will now be completely removed. Remember to commit and push to apply the changes to the remote repository.