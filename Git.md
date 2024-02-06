## Stash
### Push stash with message
Adding the specific files is optional.
```bash
git stash push -m "message here" <file1> <file2>
```
### Show diff
Show the diff of the specified stash number
```bash
stash show -p stash@{0}
```

### Pop specific stash
```bash
git stash pop stash@{1}
```

## Add default git message
Add template to `~/.gitmessage`

## Cherry pick change from another package
```bash
git --git-dir=../<some_other_repo>/.git \
format-patch -k -1 --stdout <commit SHA> | \
git am -3 -k
```

## git log

### Check file history, even if deleted
```sh
git log --full-history -- <filepath>
```
### Search if a string has ever been in the repo
`git -C <package> log -S <string> --source --all`
### Local ignore
`vim .git/info/exclude`
