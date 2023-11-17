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