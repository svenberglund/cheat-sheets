
# Table of Contents
1. [Basics/Misc](#Basics)

   1.1 [Branching, merging and commit control](#branching-merging-commit)

   1.2 [Initiation](#Initiation)

2. [Inspection and diagnosing](#inspect-diagnose)
3. [Squashing and merging](#squash-merge)
4. [Maintenance and housekeeping](#maintain-hosekeep)
5. [Configuration](#config)



# Basics


## Branching, merging and commit control<a name="branching-merging-commit" /> 


### Create new local feature branch (example):

```
git checkout -b feature_myAwsomeFeature
```

### Create new local feature branch from specific branch (example): 

```
git checkout -b feature_myAwsomeFeature teamDevelopBranch
```

You can then do git push and it will be created at origin aswell 
however you have to do a set-upstream explicitly to connect the two branches (see below)


### Make sure to track from upstream branch (Set tracking of local branch that does not track properly from origin).

The default behaviour is to always push to remote branch with same name but then when you want to pull for the first time you normally get this error:
> "you asked me to pull without telling me which branch ...." 


Can be resolved like this: 


The old syntax (example, the new branch is named *bucket-4*):

```
git branch --set-upstream bucket-4 origin/bucket-4
```

The `--set-upstream` flag is deprecated and will be removed. Consider using `--track` or `--set-upstream-to`

from git 1.8

```
git branch -u origin/bucket-4 bucket-4
```
or just

```
git branch -u origin/bucket (if you are already in bucket-4 local branch)
```


### Add all (unstaged, modified, deleted) from current place in working tree path:

```
git add -A .
```




### Unstage added files (remove file from version control without removing it locally)

```
git rm --cached <filename>

# ex: all files in Debug folder

git rm --cached **/Debug/*

# ex: all .cache files

git rm --cached *.cache
```


### Delete and retrack (delete and pull down a certain branch, ex develop, if you have screwed it up hard locally and wan to start fresh):

```
git branch -D develop
git pull
git checkout -t origin/develop
```


### Delete (local and) remote branch 'origin/myBranch'


First delete it locally if you have one:

```
git branch -D myBranch
```

Then this weird syntax says: take nothing on my side and make it origin/myBranch

```
git push origin :myBranch
```

Done!



### Create patch from latest commmit (e.g. for mirgrating to other repo)

```
git show HEAD > feature_6010.patch
```


### Rebase (workflow to rebase feature branch):

Start by switching to base branch (i.e. our-base-branch)

```
git checkout our-base-branch
git pull
git chekout <my-feature-branch>
git rebase our-base-branch
```


## Initiation

### Create local repo from non empty dir

```
cd <local-or-remote-dir>

git init 
git add .
git commit -m 'message'
```

Now if you want to add it to a remote repo, try this:

```
git remote add origin <remote-repo-url>
git push -u origin master
```


### Create remote repo (e.g. on network drive) and clone it into a working directory:

```
cd /<path-to-remote-repos>/
mkdir new_repo
cd new_repo
git --bare init 

pwd
```

...now copy the path to use in the following:

```
cd ~/<wherever-you-want-it>/
git clone /<paht-to-remote-repo>/new_repo
```



### Create new empty branch (e.g. if you for some reason want a totally or partially separate project in the same repo where you already have other stuff)

```
git checkout --orphan newbranch
```

Then remove all stuff lingering from previous branches:

```
git rm -rf .
```




# Inspection and diagnosing<a name="inspect-diagnose" />



## Log all commits in *&lt;feature-branch>* that do not emanate from *&lt;dev>*

```
git log <feature-branch> ^<dev> --no-merges
```


## Diff specific file with specific version (example: models.py)

```
git diff HEAD~5:models.py models.py
```


## Diff or log changes on one specific file


Use --- before file paht (note: with trailing blank as well) example:

```
git diff 44cde6aeaf061e8cd69f2a2e8cc3b6729bd152f5 a6bdf8b767a13df801861a534fbfd25731d77e93 -- <paht-to-file>/FileToDiff.java

git log  -- <path-to-file>/FileToDiff.java
```

## Diff bewtween branches

Diff master branch and myFeature branch
```
git diff master..myFeature
```

Diff current branch with myFeature branch
```
git diff myFeature
```


## List branches that were merged into master:

```
git branch -r --merged
```



## Other good commands

```
git status -s
git branch -a
git remote -v
```

Like pull but will not imply merges:
```
git fetch origin

gitk --all
```
TODO: explain the above



## Working with sub repos
Find branch only present in some submodule/subrepo.

Run git submodule foreach (any bash command can be appended)
then invoke branch list and grep to branch to search for (in this case *needle-branch*):
example:

```
git submodule foreach 'git branch --all | grep -B 7 needle-branch'
```



# Squashing and merging<a name="squash-merge" />

## Squash all local commits - on feature branch (a branch that was created from 'dev' branch)

```
git checkout yourBranch
git reset $(git merge-base dev yourBranch)
git add -A
git commit -m "One commit message to rule them all on yourBranch"
```
...then the branch will have diverged from upstream obviously (if there is an upstream), so do the following:

```
git push origin :yourBranch
git push
```

Note, for the sake of having a clean master/dev branch the above is not necessary, instead just do a sqashed merge: `git merge --squash yourBRanch`. The above is done if you want the feature branch itself to have less commits. 

## Merging

```
git merge --no-ff feature-xx
```

Use the `--no-commit` flag to disable autocommit if you want to do some special tweaks(like some row you don't want to merge)

TODO: Write explaination and example with merge `--squash` (difference with `--no-ff` )






# Maintenance and housekeeping<a name="maintain-hosekeep" />


## List all untracked files

```
git ls-files --others --exclude-standard
```


## Remove all untracked files form working copy

```
git clean -fd
```

...if it doesnt work go to your locla git repo root level and do:

```
rm -rf *   
```

(Should delete all except the .git stuff, so the git checkout state shall still be there)

```
git reset --hard HEAD

```


## Remove local refs to remote branches that are not any longer present at origin

```
git remote prune origin
```



## Branch cleanup - removing stale branches

This is the epic housekeeping task, removing all junk branches from the remote repo.
If you are responsible of the repo there is a good chanse that this is something that you have to do reuglarly in big projects.
 
In the below workflow we use &lt;dev> as a example of a merge-to branch. Everyting that was merged to &lt;dev> can be removed.


### Easiest workflow to achieve an up-to-date repo without lotsa junk - do it in 3 or 4 steps:


#### (1) Locally remove branches that have been merged into branch &lt;dev>

List

```
git branch --merged <dev>
```

Remove
```
git branch --merged <dev> | xargs git branch -D
```


#### (2) Remove local refs to remote branches that are gone


```
git fetch --prune
```


#### (3) Take care of remote branches that have been merged into &lt;dev>


List (provided we have already removed the local ones this command outputs the remote ones)


```
git branch -a --merged <dev>
```


Remove them one by one 


```
git push origin :<my-feature-branch>
```

...or in batch by some clever scripting... (be careful not to remove the <dev> branch itself that is probably listed first)


#### (4) Check the remaining branches

As a fourth step you might want to check the remaining branches, check with your collegues if they are still active. 

Check who 'owns' the remaining branches i.e. who are committing on them... What are the unique commits on them...?

See section *Log all commits in &lt;feature-branch> that do not emanate from &lt;dev>*.






# Config

## Merge tool

Set up (your linux box) to use p4merge, meld, kdiff3 or whatever you have installed. Example:
```
git config --global merge.tool p4merge
```


## Misc


### Windows

#### Setup mergetool on Cygwin 

Use p4merge or kdiff3

Ex: 

```
git config --global merge.tool myp4merge
git config --global mergetool.myp4merge.cmd 'p4merge.exe "$(cygpath -wla $BASE)" "$(cygpath -wla $LOCAL)" "$(cygpath -wla $REMOTE)" "$(cygpath -wla $MERGED)"'
git config --global diff.tool myp4diff
git config --global difftool.myp4diff.cmd 'p4merge.exe "$(cygpath -wla $LOCAL)" "$(cygpath -wla $REMOTE)"'

git config --global difftool.prompt false
```












