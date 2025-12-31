## Reset upstream and origin

Given someone's repo, if we want to make its old `origin` as `upstream`, and add our own repo as the new `origin`, do:

`<my repo>` and `<their repo>`
```bash
# first, make a new repo called <my repo>, then
git clone <their repo>
cd <their repo>
git remote set-url origin <my repo>
git remote add upstream <their repo>
git push origin
git remote -v # check remote situations
```

## Delete branches
```bash
git push -d <remote_name> <branchname>   # Delete remote
git branch -d <branchname>               # Delete local
```