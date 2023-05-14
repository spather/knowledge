# Git Tips

Amend a commit without having to deal with the editing the commit message:

```bash
git commit --amend --no-edit
```

Alias to list local branches in order they were most recently touched:

```
bcr = branch --list --sort=committerdate --format='%(align:40)%(refname:short)%(end) %(committerdate:relative)'
```

Alias to show local branches that no longer have a corresponding remote branch (probably means you merged the PR and can delete the local branch):

```
brg = !git bcr $(git branch --format='%(if:equals=[gone])%(upstream:track)%(then)%(refname:short)%(end)')
```

To automatically remove remote branches once they've been merged and deleted on github, put this in .gitconfig:

```
[fetch]
        prune = true
```
