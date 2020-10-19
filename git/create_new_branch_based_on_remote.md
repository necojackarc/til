# Create New Branch Based on Remote

I almost always make a new branch based on the current `HEAD` in everyday work using `git checkout -b <new_branch>` but ocasionally, I'd like to make a new branch based on a remote branch. In that case, I tend to lazily checkout the target remote branch with `git checkout <base_remote>/<branch>`, then do `git checkout -b <new_branch>`, which defenitly works but looks to involved one unnecessary step - checking out the base remote branch.

You can easily find how you can make a new branch based on a remote branch with `--help` but here's my notes:


```bash
git checkout --no-track -b <new_branch> <remote>/<branch>
```

`--no-track` is necessary not to make the new branch linked to the base remote branch.

If you don't want to swtich to the new branch on creation, you can use `git branch` with the same options:

```bash
git branch --no-track <new_branch> <remote>/<branch>
```
