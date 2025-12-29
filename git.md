## Reset upstream and origin

Given someone's repo, if we want to make its old `origin` as `upstream`, and add our own repo as the new `origin`, do:

`<myrepo>` and `<theirrepo>`
```bash
git clone <theirrepo>
cd <theirrepo>
git remote set-url origin <myrepo>
git remote add upstream <theirrepo>
git push origin
```