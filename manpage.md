<!---
generate a manpage with pandoc
pandoc manpage.md -s -t man -o ms.1

to view edits more efficiently run
pandoc manpage.md -s -t man | /usr/bin/man -l -
-->
% DURA(1) dura 21.11
% Tim Kellogg
% 2022 

# NAME
dura - You shouldn't ever lose your work if you're using Git

# SYNOPSIS
**dura command**

# DESCRIPTION
Dura is a background process that watches your Git repositories and commits your uncommitted changes without impacting
HEAD, the current branch, or the Git index (staged files). If you ever get into an "oh snap!" situation where you think
you just lost days of work, checkout a `dura` branch and recover.

Without `dura`, you use Ctrl-Z in your editor to get back to a good state. That's so 2021. Computers crash and Ctrl-Z
only works on files independently. Dura snapshots changes across the entire repository as-you-go, so you can revert to
"4 hours ago" instead of "hit Ctrl-Z like 40 times or whatever". Finally, some sanity.

## How to use  <br>


**dura serve**
  :  This starts the dura daemon. You can run **dura serve &** to run it in the background




**dura watch**
 :   add the current directory to dura's list of git repositories to   


Right now, you have to `cd` into each repo that you want to watch, one at a time.

If you have thoughts on how to do this better, share them [here](https://github.com/tkellogg/dura/issues/3). Until that's sorted, you can
run something like `find ~ -type d -name .git -prune | xargs -I= sh -c "cd =/..; dura watch"` to get started on your existing repos.

Make some changes. No need to commit or even stage them. Use any Git tool to see the `dura` branches:

```bash
$ git log --all
```

`dura` produces a branch for every real commit you make and makes commits to that branch without impacting your working
copy. You keep using Git exactly as you did before.


Let `dura` know that it should stop running in the background with the `kill` command.

```bash
$ dura kill
```

The `kill` can happen in any directory. It indicates to the `serve`
process that it should exit if there is a `serve` process running.

## How to recover

The `dura` branch that's tracking your current uncommitted changes looks like `dura/f4a88e5ea0f1f7492845f7021ae82db70f14c725`.
In $SHELL, you can get the branch name via:

```bash
$ echo "dura/$(git rev-parse HEAD)"
```

Use `git log` or [`tig`](https://jonas.github.io/tig/) to figure out which commit you want to rollback to. Copy the hash
and then run something like

```bash
# Or, if you don't trust dura yet, `git stash`
$ git reset HEAD --hard
# get the changes into your working directory
$ git checkout $THE_HASH
# last few commands reset HEAD back to master but with changes uncommitted
$ git checkout -b temp-branch
$ git reset master
$ git checkout master
$ git branch -D temp-branch
```

If you're interested in improving this experience, [collaborate here](https://github.com/tkellogg/dura/issues/4).

# BUGS
https://github.com/tkellogg/dura/issues

# COPYRIGHT
Copyright 2022 Tim Kellogg
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

