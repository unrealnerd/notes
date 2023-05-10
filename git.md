# GIT NOTES

| Use   | Command  |
| ---|---|
|Save Stash |`git stash save <nameofstash>`
|Clone New|`git clone http://myrepo.com/repo/proj.git`
|Clone Recursive Submodule|`git clone --recurse-submodules http://myrepo.com/repo/proj.git`
|Recursive Pull Submodule|`git pull --recurse-submodules`
|List Stash|`git stash list`
|Backup stash|`git stash show -p > stash.diff`
|Apply specific Stash|`git stash apply stash@{1}`
|Clean|`git clean -fdx`
|Create Patch|`git diff stash@{0} > mypatch.diff`
|Stage all and patch|`git diff --cached > mypatch.patch`
|Undo Commit|`git reset HEAD~` <br/>`git reset --hard origin/Develop`
|Undo all changes|`git reset --hard`
|Cherry Pick|`git cherry-pick <commitId>`
|Rebase|`git checkout master git pull --rebase origin`
|Change username and email of old commits|`git filter-branch --env-filter ' if [ "$GIT_COMMITTER_NAME" = "Shetty, Arjun" ]; then GIT_COMMITTER_NAME="unrealnerd"; GIT_AUTHOR_NAME="unrealnerd"; GIT_COMMITTER_EMAIL="arjunshetty2020@gmail.com"; GIT_AUTHOR_EMAIL="arjunshetty2020@gmail.com"; git commit-tree "$@"; else git commit-tree "$@"; fi' HEAD`
|set username and email|`git config user.name "unrealnerd"`<br/>`git config user.email "arjunshetty2020@gmail.com"`
|Pull Specific PR|`git fetch origin pull/PR#/head:branchname`<br/>`git checkout -b mybranch develop`<br/>`git pull http://myrepo.com/repo/proj.git branchname`<br/>`git fetch upstream pull/##/head:prs/pr##`
|Set push remote|`git remote set-url --push origin http://myrepo.com/user/proj.git`
|Undo a file in a commit|`git checkout <COMMIT SHA>~1 -- filepath`<br/> OR<br/>`git checkout HEAD^ /path/to/file`<br/>`git commit --amend`<br/>`git push -f`
|when gitignore is not working as expected|`git rm -r --cached .`
|generating ssh|`ssh-keygen -t rsa -b 4096 -C "arjunshetty2020@gmail.com"`<br/> copy pub key to github from `C:\Users\arjun_shetty\.ssh\id_rsa.pub`
|Deleting a remote branch|git push origin --delete test
|get one file form a stash|git checkout stash@{0} README.md
|reset to specific branch in origin|

## hold multiple ssh keys

```shell
    host github.com
    IdentityFile ~/.ssh/id_rsa_personal
    host gitlab.com
    IdentityFile ~/.ssh/gitlab.id_rsa
    host git-cd.isus.emc.com
    IdentityFile ~/.ssh/id_ed25519
```

- use it this way `git clone git@personal:<path>`

## Splitting a repository

```shell
    git clone git@github.com:bitsmonkey/oldrepo.git newrepo
    git remote rm origin
    git filter-branch --index-filter 'git rm --cached -r foldertoberemoved1 foldertoberemoved2' -- --all
    git remote add origin git@github.com:bitsmonkey/newrepo.git
    git push origin master
```

## Creating a new repository from the old without the history and selectively getting specific branches from old

- `git clone https://github.com/unrealnerd/myoldrepo.git`
- make another copy of the folder 
- `git remote add neworigin https://github.com/unrealnerd/mynewsrepo.git`
- `git checkout branch1`
- `git checkout --orphan orphbranch1`
- `git add -A`
- `git commit -am "commitmessage"`
- `git branch -D branch1`
- `git branch -m branch1`
- `git push -f neworigin branch1`
- `git checkout branch2`
- `git checkout --orphan orphbranch2`
- `git add -A`
- `git commit -am "commitmessage"`
- `git branch -D branch2`
- `git branch -m branch2`
- `git push -f neworigin branch2`

## Cherry picking commits from different repository

```shell
    git checkout -b newbranch
    git remote add oldrepo <git url>
    git log olrepo/oldbranch
    git cherry-pick <commitid>
    git push origin newbranch
```

## Hello World git commands

- Download git for windows from [here](https://git-scm.com/download/win)
- `git clone https://github.com/unrealnerd/kukke.git`
- make the changes on your local
- `git add .`
- `git commit -m 'commit message'`
- `git push origin master`


## when some files that were unintendedly pushed to the server, which were supposed to be part of gitignore. here is how to undo that

- `git rm -r --cached .`
- `git add .`
- `git commit -m '* removing unintended push'`

## moving from detached branch to an existing branch

- `git reflog` get the detached branch id hash
- `git checkout <hash>`
- `git branch temp` shifts the detached branch commits to a temp branch
- `git checkout existingbranchname` checkout to where this commit should flow


## renaming author and email

```
git filter-branch --env-filter '
WRONG_EMAIL="Arjun_Shetty@Dell.com"
NEW_NAME="unrealnerd"
NEW_EMAIL="arjunshetty2020@gmail.com"

if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```


## Delete old stashes

- `while git stash drop 'stash@{5}'; do true; done` keeps top 5 stashes and clears the rest
- `git stash clear` to clear all the stashes

## Credential manager

- when having multiple account set the credential config using this command
`git config credential.username unrealnerd`