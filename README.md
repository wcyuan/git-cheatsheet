<!--- (Converted from html with https://domchristie.github.io/to-markdown/) -->

### <a name="TOC-Table-of-Contents"></a>Table of Contents

*   [Most Common Commands](#mostcommon)
*   [Less Common Commands](#lesscommon)
*   [Git Config Suggestions](#config)
*   [Some introductory Git concepts](#concepts)
*   [Are you in the middle of a rebase?](#rebase)
*   [Importing a patch from another repo to your repo](#patches)
*   [Splitting a commit into two parts](#split)
*   [Resolving Pull + Rebase Conflicts](#conflicts)
*   Anther useful cheat sheet: <a>http://cheat.errtheblog.com/s/git</a>
*   For details about refspecs, use [git help gitrevisions](https://www.kernel.org/pub/software/scm/git/docs/gitrevisions.html)

* * *

<a name="mostcommon"></a>

### <a name="TOC-Most-Common"></a>Most Common

| Command | Description | Comment |
| ------- | ----------- | ------- |
| `git status` | Show which files are different from the last commit (both in the staging area, and the working tree) |
| `git add <files or directories>` | Add changes to those files to the staging area |
| `git commit` | Commit the changes in the staging area |
| `git pull` | Update your repo from the shared | All changes to your branch must be committed |
| `git push` | Push your commits to the shared repo |
| `git log [<file>]` | Show history [for this file] |
| `git diff [<file>]` | Show diffs for this file between the working directory and the staging area |
| `git show [-n 1][<rev>][<file>]` | Show the commit message and diff for a revision |
| `git cherry-pick [<rev>]` | Pull a commit from another branch into this branch. Doesn't just pull the changes into the working tree (like svn merge would), it actually commits those changes in this branch. The commit is still local, you still need a `git push` to push them into the shared repo |

* * *

<a name="lesscommon"></a>

### <a name="TOC-Less-Common"></a>Less Common

| Command | Description | Comment |
| ------- | ----------- | ------- |
| `git diff --staged <file>` | Show diffs for this file between the staging area and the last commit |
| `git diff --word-diff` | Show which parts of each line changed, instead of just showing which lines changed. |
| `git status -sb` | A more concise status. |
| `git branch` | List branches |
| `git branch -a` | List all branches, including remote branches which would otherwise be hidden. Use `-r` to only show remote branches. |
| `git branch -vv` | List local branches with the last commit in each branch, and the upstream branch that each branch is tracking. |
| `git branch <name>` | Create a new branch named <name> |
| `git branch -m <old_name> <new_name>` | Rename a branch |
| `git branch --track <branch_name> origin/master` | Create a new branch that tracks the origin master branch |
| `git branch -d <branch>` | Delete a branch, if all changes have been merged into the upstream branch (safe) |
| `git branch -D <branch>` | Delete a branch when all changes have not been merged into the upstream branch. This could throw away changes. But you need this because sometim es git can't figure out that all the changes actually have been pushed to other branches, though cherry-picking or whatever. |
| `git branch -rd <remote>/<remote-branch>` | Delete the local copy of a remote branch |
| `git branch --set-upstream master origin/master` | Set the local branch master to track the remote branch origin/master. |
| `git config --unset branch.<branch>.remote
git config --unset branch.<branch>.merge` | Remove whatever remote branch the local branch <branch> had been tracking |
| `git fetch <remote>` | Create a local copy of all the branches from a given remote repository. These will have names like `remotes/<remote>/<branch>`. This is the first step in a pull (before either a merge or a rebase) |
| `git fetch <remote> <remote-branch>:<local-branch>` | Create a local copy of one particular branch from a given remote repository. Call the local copy `<local-branch>` (usually want to make this the same as `<remote-branch>`) |
| `git remote show <remote-name>` | Show information about a remote repository |
| `git checkout <branch>` | Switch to a branch | All changes to your current branch must be committed |
| `git checkout <rev>` | Change working tree to the state of a specific commit (Use git checkout <branch> to get back to the current state) |
| `git checkout <file>` | Change a file in the working tree to its state in the last commit, wiping out any uncommitted changes you had made to it |
| `git show <revision>:<path/to/file>` | Print an old version of a file to stdout. The path to the file should start from the root of the repository. |
| `git blame <revision> -- <file>` | Print an old version of a file to stdout with annotations. Unlike `git show`, you don't need the full path to the file. |
| `git log --follow -- <file>` | Show history for a deleted file. This is essentially the same as the command for an existing file, except that you need the '--' to tell git tha t the file name isn't a malformed revision hash. You need the --follow in case the file wasn't just removed, but was renamed. [http://stackoverflow.com/questions/3197416/why-doesnt-git-log-foo-work-for-deleted-file-foo](http://stackoverflow.com/questions/3197416/why-doesnt-git-log-foo-work-for-deleted-file-foo) |
| `git log --pretty=format:"COMMIT %h (%ci) %s" --follow -p -U0 <file> | egrep 'COMMIT|<pattern>' | less` | Show all commits where the diff includes pattern. |
| `git log --all --oneline --graph` | Show commit history in a graph |
| `git log @{u}..` | Show local commits that haven't yet been pushed upstream yet. If you get the error `error: No upstream branch found for ''`, then it means you haven't set an upstream branch for this branch yet, or the upstream branch is set incorrectly (use `git branch -avv` to confirm this. You can set an upstream branch with `git branch --set-upstream <mybranch> <myremotebranch>` |
| `git cherry -v` | Show local commits that haven't yet been pushed upstream yet. |
| `git commit -a` | Add all changes to the staging area, then commit the changes in the staging area. Does not add new files, only commis changes to existing files. |
| `git commit <file>` | Add changes to this file to the staging area, then commit the changes in the staging area |
| `git commit --amend` | Redo previous commit, adding in changes you've staged in the meantime. This throws out the commit you just made and replaces it with a new commit -- it changes your history. |
| `git commit --amend -m <message>` | Redo previous commit, adding in changes you've staged in the meantime, and change the commit message. |
| `git revert <rev>` | Create a new commit where all the changes in the specified commit have been undone. This doesn't modify existing history, it only adds to it | All changes to your current branch must be committed |
| `git reset <files>` | Unstage changes to those files. This is the opposite of git add. That is, if you've made some changes, and you stage those changes (put them in the staging area) and having committed them, git reset will remove them from the staging area, so that they won't be part of the next git commit. This doesn't change the working directory |
| `git reset --hard @{u}` | Reset this branch to the shared repo, throwing out any changes you've made locally, including changes you've committed locally but haven't pushed. This works on the whole repo, not on one file at a time. |
| `git reset --hard HEAD^` | Undo your last commit -- throw it away entirely. Also throws away any changes you have in your working tree and staging area. |
| `git reset --mixed HEAD^` | Undo your last commit -- but leave the changes in the working directory | More on git reset: [http://stackoverflow.com/questions/2530060/can-you-explain-what-git-reset-does-in-plain-english](http://stackoverflow.com/questions/2530060/can-you-explain-what-git-reset-does-in-plain-english) |
| `git reset --soft HEAD^` | Undo your last commit -- but leave the changes in the index |
| `git reset --merge` | Abort a cherry-pick if there was a conflict. |
| `git config branch.master.rebase true` | Make `git pull` on master always use rebase instead of merge. This means your changes will always go at the end of the history, instead of to the side of it. |
| `git config --global branch.autosetuprebase always` | Setup all new branches to pull with rebase instead of merge. |
| `git config --global push.default tracking` | By default, `git push` will push all branches with the same name as the remote. This will change it to push to the upstream branch that you configured, as opposed to the branch with the same name. |
| `git stash` | Temporarily save local modifications |
| `git stash pop [stash@{<n>}]` | Reapply the <n>th stashed state, and drop that state. In most cases, if you just want one stash at a time, just do git stash and git stash pop and you won't have to worry about multiple stashes |
| `git stash list` | See a list of stashed states |
| `git stash apply [stash@{<n>}]` | Make the working tree look like the <n>th stashed state |
| `git stash show [stash@{<n>}]` | Show the changes in the <n>th stashed state |
| `git stash drop [stash@{<n>}]` | Delete the <n>th stashed state |
| `git stash clear` | Delete all stashed states |
| `git cherry-pick -n [<rev>]` | Pull the changes from a particular commit into this branch without committing those changes. |
| `git rebase -i` | Edit past commits. This has you edit a file listing all commits, where you can choose which to squash, which to delete, which to edit, etc. Then it makes the changes. While making the changes, you could end up with conflicts to resolve before it finishes. | This is done for you during a git pull to move your commits to the end of the shared history. You should use rebase to squash your changes into a single commit before pushing. |
| `git rebase --abort` | Stop a rebase. If a rebase finds conflicts, it will leave you in the middle of the rebase and wait for you to resolve the conflicts. This allows you to abort the rebase and go back to the state before the rebase started |
| `git grep` | Grep through the current version of all files in the repo |
| `git grep <commit>` | Grep through all files in the given version of the repo |
| `git rm <file>` | Tell git to stop tracking a file, and to remove it from the working directory. This will delete the file from the filesystem. Useful if a file is not longer needed. Note, the opposite of git add (add a file to the staging area) is not git rm, it's git reset |
| `git rm --cached <file>` | Tell git to stop tracking a file, but not to remove it from the working directory (i.e., the file should be there, git just shouldn't know about it). Useful if you are also adding the file to a .gitignore file somewhere |
| `git ls-files` | List all files in the repo |
| `git init` | Create a new, empty git repo |
| `git clone <existingrepo> [<localname>]` | Create a local copy of an existing git repo. Not just a copy of the files, a copy of all the revision history |
| `git config -l` | List your git configuration |
| `git config <option> <value>` | Modify your git configuration |
| `git bisect` | A way of finding in which commit a change first appeared. | I wouldn't recommend it, it's too hard to use and seem much less useful than you might hope. |
| `git mergetool` | A gui for resolving merge conflicts. Creates diffs and passes them to a third-party diff viewer, such as `meld` | I find it sort of useless. See details on merges below. |
| `gitk` | A gui for browsing commit history. Useful for performing diffs between neighboring nodes in the history graph. | Seems cool, but I haven't used it much. |
| `git difftool` and `git difftool --dir-diff` | Graphical diff viewer. This just composes the diffs and passes them to a third party diff viewer, such as `meld`. The `--dir-diff` option copies all diffs to a temporary directory before diffing them, so you can see all the diffs at once. Without `--dir-diff`, `git-difftool` will launch a separate `meld` process for each file, one after another, which means you can't go back and forth between files) | Seems cool, but I haven't used it much, partly because Eclipse's git plug-in has its own diff viewer. |
| `git cherry-push` | A custom command that creates a temp branch, cherry-picks a revision, and pushes it | [https://github.com/wcyuan/Scripts/blob/master/tools/git-cherry-push.pl](https://github.com/wcyuan/Scripts/blob/master/tools/git-cherry-push.pl) |

* * *

<a name="config"></a>

### <a name="TOC-Git-config-suggestions"></a>Git config suggestions

Also see the relevant [manual page](http://git-scm.com/docs/git-config.html) and [book chapter](http://git-scm.com/book/ch7-1.html).

*   If you are running over NFS, consider setting core.preloadindex=True. This is described further on the git config man page:

    <pre>Enable parallel index preload for operations like git diff

    This can speed up operations like git diff and git status especially on filesystems
    like NFS that have weak caching semantics and thus relatively high IO latencies. 
    With this set to true, Git will do the index comparison to the filesystem data in 
    parallel, allowing overlapping IO's.
    </pre>

*   push.default=tracking is useful
*   branch.autosetuprebase=always is also generally useful. This tells git that `git pull` really means `git pull --rebase`. The options are
    *   `never` - Do not use rebase for pulls by default.
    *   `local` - Use rebase for branches tracking a local branch
    *   `remote` - Use rebase for branches tracking a remote branch
    *   `always` - Use rebase for all tracking branchesTo set rebase to true for a branch that already exists, set `git config branch.*branch-name*.rebase true`
*   I usually also set core.pager = less -FRSX.
    *   F means "quit if one screen". If the entire file can be displayed on a single screen, just print the file instead of entering less.
    *   R shows raw control characters
    *   S chops long lines (instead of folding them)
    *   X disables termcap initialization and deinitialization. Deinitialization of the terminal often clears the screen. So this may mean that after you quit less, you will still see the last screen you were looking at in less.
*   If you do work on both Windows and Unix, you should probably consider the value to use for core.autocrlf, which controls file line endings. Here are some useful links: [https://help.github.com/articles/dealing-with-line-endings](https://help.github.com/articles/dealing-with-line-endings), [http://stackoverflow.com/questions/2825428/why-should-i-use-core-autocrlf-true-in-git](http://stackoverflow.com/questions/2825428/why-should-i-use-core-autocrlf-true-in-git).
    *   One option is:
        *   Make sure all files in the repository have Unix-style line endings
        *   Tell your git configuration on Windows to change all files to Unix style line endings on committing and convert all files to Windows style line endings on checking out (core.autocrlf=true)
        *   Tell your git configuration on Unix to change all files to Unix style line endings on committing (core.autocrlf=input)
    *   Another option is to use a `.gitattributes` file. This allows you to specify exactly which files have which line endings, and since the file is committed in the repo, it will affect all users of the repository, you don't have to assume that all users have their configuration the way you want.
    *   A third option is to just set core.autocrlf to false, and either live with mixed newlines, or hope that everyone happens to use the same conventions.

All the commands together:

<pre>git config --global core.preloadindex True
git config --global push.default tracking 
git config --global branch.autosetuprebase always
git config --global core.pager "less -FRSX"
</pre>

* * *

<a name="concepts"></a>

### <a name="TOC-Git-Concepts"></a>Git Concepts

*   **Distributed.** Everyone has their own copy of the entire repository. Not just every file, but all the history too. If one repository is deemed the "official" repository, it's only be convention, not any technical requirement. If there is an official version of the repository, then once you are ready to publish your changes, the equivalent of committing to the CVS or SVN repo is pushing your commit from your local Git repo to the official Git repo.
*   **Every commit is a snapshot of the whole tree.** This is similar to Subversion where each commit represents a state of the whole tree (this is pretty much required if you want to be able to group changes to multiple files in a single commit, or if you want file renames to be shown in the commit history). Subversion has a weird feature where different files in the same directory can be on different commits at the same time -- git doesn't have that (which seems good to me).
*   **Non-linear commits.** Since everyone has their own repository, each person can make changes to the same file independently. Person X can make a series of commits A, B, C, D, and person Y can make a series of commits M, N, O, P. Then, if X pulls Y's changes into his tree with a typical merge commit, git keeps both lines of history separately, then merges the ends together (D and P). So that means, in general, history is a tree, not a line, and each commit could have multiple parents.
    *   **Commits identified by hash.** Because history is not linear, it doesn't make sense to number commits with monotonically increasing numbers. Instead they use 40-character alphanumeric SHA-1 Hashes (usually git accepts the first 7 characters as an abbreviation, and that's enough to uniquely identify the commit. If it isn't, git will complain). The information being hashed encapsulates the entire state of the tree at that commit, plus the name of the parent commits, plus a timestamp and log message. So if two commits share all of that information, they hash to the same thing, and if any of that differs, they hash to different things.
        *   Git relies heavily on these hashes in other places too. The principle is **content-addressable**.
    *   If you use **rebasing commits** instead of merge commits:
        *   When you push your changes to the official repo, you can only push one commit at a time. **If the changes you want to publish were spread across many commits, you have to squash them into a single commit (using "rebase") before you push them**. Or if they represent many conceptually separate changes, each change should be a single commit and should be pushed separately.
        *   When you pull new changes from the official repository into your repo, instead of the result being two separate histories that get merged together at the end, you should do a "rebasing pull" which will take the official repository's history and replay your changes on top of those changes. I.e., all the official changes will go before your changes. This is described in more detail below.
            *   This means that you are routinely **rewriting history**, which would be highly discouraged in CVS or SVN!
    *   **Commit timestamps are when the change was made, not when it was published.** Each commit carries a timestamp from when it was first created, which is when you first commit the change in your local repository. When you push the change to the official repository, it keeps that original timestamp. So the commit's timestamp is not a good way to know when a change was published. Also, it means that though the official repository's history is linear, the timestamps are not necessarily in order.
    *   **No keyword expansion.** Git does not have the or $Id$, $Header$ keywords that RCS, CVS, and SVN have, which get expanded into information about the file's last commit. This would violate the principle of content-addressed storage.
*   **Staging area.** SVN essentially has two versions of every file: the commited version and version you are working on. git has three versions, it adds a staging area. So after you make a change, you git add the change to the staging area, then you can continue working on the file and the staging area will only remember the last thing you added. When you do a git commit, it only commits the things in the staging area.
*   **Commit often** and with impunity. Committing is almost like saving, you should do it a lot. However, you will have to clean it up later, before you publish.
*   **Branches are easy** to create and remove, so use them more often.
*   **You can't leave changes to your working directory** sitting around. You have to commit all those changes before you can do a git pull, or before you switch branches. If you really don't want to commit the changes, you can get around it with git stash.
*   **Git commands only work when you are in a git repository.** Meaning, your current working directory has to be in the git repository, not just the file that you are running on.
*   **Git commit messages should start with a short (60-character) summary of the change**, then a blank line, then the rest of the message. Because Git histories can be quite complicated (non-linear), there are many tools for visualizing histories, and those tools will often show many commits close together, plus the beginning of each commit message. Having the short summary at the beginning is useful for those tools.
*   **It's easy to add a new git subcommand**: when you say git , if is not a standard git command, it will look in your path for a command called git-<cmd>.
    *   Git provides separate APIs: a high level API (also called "Porcelain" commands) which is targeted towards users and a low level API (also called "Plumbing" commands) which are targeted towards developers and scripts. The high level API is for normal interactive use, so they are easier to use, but are also subject to change. The low level API is meant to be more stable, so suitable for scripts. More information at the [git manpage](http://schacon.github.io/git/git.html#_low_level_commands_plumbing)
*   With Git, you need to use the complete subcommand name. Unlike subversion, where "svn st" is the same as "svn status" and "svn ann" resolves to "svn annotate", with Git you generally can't take shortcuts with the subcommand name. However, you can easily define aliases or new subcommands.

#### <a name="TOC-Gripes"></a>Gripes

*   **Dangerous.** In Git, it is normal to change your repository's history. I think this is the worst part about Git. I think the whole point of version control is that you can never lose information. Even if you mess something up, you can always go back. But Git encourages you to "clean up your history", which means doing all sorts of history changing things, where the old history is just lost (unless you take special precautions to save it). If you mess up while "cleaning up", you may not be able to easily recover.
*   **Confusing.** Git is not a good example of "convention over configuration". Git has a lot of different versions of each file -- there's the working tree, the staging area, a version in each commit, versions in every branch, versions in the shared repository, etc. It is very flexible and accommodates many different types of workflows. But this also makes it confusing, and means that you have to figure out for yourself how you want to use it. It also means that the commands might not do what you would expect that they do. Here are some examples of commands that I find confusing.
    *   Depending on what argument you give it, git checkout does three things that I would consider completely separate
        1.  Checkout the version of a file in a past commit.
        2.  Switch branches.
        3.  Throw out uncommitted changes in a file. Delete the working version of the file and replace it with the version in the repository.
    *   git reset can be used to remove a file from the staging area, or to remove an entire commit. These seem like they should be two completely separate commands -- one is "safe" and the other isn't. It's confusing that there are so many different ways to "undo" a commit.
    *   The opposite of git add is git reset, not git rm. While git add and git reset (with no options) are pretty safe since they just change the staging area, git rm deletes the file -- it changes the working tree, not just the staging area. If you use git rm where you meant to use git reset, you will probably lose uncommitted changes.

* * *

<a name="rebase"></a>

### <a name="TOC-How-can-you-tell-if-you-are-in-the-middle-of-a-rebase-"></a>How can you tell if you are in the middle of a rebase?

If you are, the directory

<pre>.git/rebase-apply/
</pre>

will exist.

* * *

<a name="patches"></a>

### <a name="TOC-Importing-changes-from-another-repo-to-your-repo-using-patches"></a>Importing changes from another repo to your repo using patches

It may be slow to run meld on a foreign repo, so copy the patch to a local repo:

<pre>    cd <my_repo>
    git branch --track review remote/origin
    git checkout review
    ssh <hostname> 'eval cd <their_path> && git show -n 1 35ce39' | patch -p1
    git difftool
    git checkout master
    git branch -D review
</pre>

* * *

<a name="split"></a>

### <a name="TOC-Splitting-a-commit-into-two-parts"></a>Splitting a commit into two parts

from [http://stackoverflow.com/a/10096941/1808109](http://stackoverflow.com/a/10096941/1808109)

Plan:

1.  rebase interactive from one before splitme.
2.  edit splitme.
3.  Reset the files to split into a second commit.
4.  Amend commit, maintaining message, modify as necessary.
5.  Add back the files split out from the first commit.
6.  Commit with a new message.
7.  Continue rebase.
8.  The rebase steps (1 & 7) can be skipped if the splitme is the most recent commit.

<pre>git rebase -i splitme^
# mark splitme commit with 'e'
git reset HEAD^ -- $files
git commit --amend
git add $files
git commit -m "commit with just some files"
git rebase --continue
</pre>

* * *

<a name="conflicts"></a>

### <a name="TOC-Merge-Rebase-conflicts"></a>Merge (Rebase) conflicts

For some repos, when you pull from the shared repository, you do a rebasing pull. Suppose your history is like this

<pre>         A---B---C---D              shared repo
             \
             E---F---G              your repo
</pre>

The goal of the rebase is to move the commits that you made so that instead of hanging off of the common commit (B), you want them to hang off of the last commit (D), like so:

<pre>         A---B---C---D              shared repo
                      \
                       E---F---G    your repo
</pre>

So **git starts with the shared repository's history, then replays your commits on top of that history.** This means the names of things are backwards from what you would expect. One would normally think of "A-E-F-G" as "my" history and "A-B-C-D" as "their" history. But because the rebase works by starting with the shared repo and adding your changes, the shared repo's history is considered local and your extra commits are considered remote.

While it is doing the replay, HEAD will point to the end of the shared repo (D) and MERGE_HEAD will point to the commit that it is currently replaying (first E, then F, then G). Suppose there is a merge conflict while replaying commit F. Then git will pause the rebase with this state:

1.  HEAD will point to the shared repo, and MERGE_HEAD will point to your commit:

    <pre>                       HEAD
                          /
             A---B---C---D              shared repo
                          \
                           E---F        your repo
                               \
                                MERGE_HEAD
    </pre>

2.  You are not on any branch, and you can't do much with git until you finish the rebase.
3.  Files that have conflicts will be modified (similar to CVS and SVN conflicts). For example, they will have

    <pre><<<<<<< HEAD
    Changes from the shared repo
    =======
    Changes from F
    >>>>>>> F's commit message
    </pre>

4.  You can print out the version from the shared repo with
    `git show :2:<file>`
    You can print out the version from your commit with
    `git show :3:<file>`
    You can print out the version from the last common commit (A in the graph above) with
    `git show :1:<file>` When you use these, you may have to give the full path to the file.

To resolve the conflict you can either:

1.  Manually modify the files as necessary. Then, run:
    `$ git add <file>
    $ git rebase --continue`
    1.  For a cherry-pick conflict, instead of `git rebase --continue`, use `git commit`.
    2.  Note, you might be tempted to do a `git commit` after the `git add`. You won't be able to do that since you aren't on a branch. If you do manage it, then when you do the `rebase --continue`, you might get an error like this:

        <pre>    $ git rebase --continue
            Applying: This change will create a conflict during rebase
            No changes - did you forget to use 'git add'?
            If there is nothing left to stage, chances are that something else
            already introduced the same changes; you might want to skip this patch.
        </pre>

    3.  As mentioned above, you can use something like `git show :2:<file> > file` to choose one version or the other.
2.  Skip your commit (F in the graph above) and accept the version from the shared repo. Run:
    `git rebase --skip` This is for all the changes in the commit, so you can't use this if you want to use the shared repo's version for some files and your version for some files.
3.  Abort the rebase. This means you will not have pulled in the changes from the remote repo, so you'll just have to redo the pull later.
    `git rebase --abort` Note that the equivalent command for aborting a cherry-pick is `git reset --merge`.
4.  A lot of sites seem to recommend git mergetool, which shows you a three-way diff with your changes on one side, the changes from the shared repo on the other side, and the current file (with changes from both comments separated by "<<<<<" and ">>>>>") in the middle. Maybe it's just because I don't understand how to use it, but I find mergetool useless for a few reasons:
    1.  My version of the is put in a temporary file called "REMOTE" and the shared repo version is in a temporary file called "LOCAL". That's just confusing.
    2.  I don't know how to tell the mergetool (I've only tried meld) to select the shared repo version and modify the current file. Since I don't know how to change the working file, mergetool is useless.
