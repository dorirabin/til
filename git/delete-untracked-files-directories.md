# Delete untracked files and directories

Delete untracked files only

```
git clean -f
```

Delete untracked files and directories
```
git clean -fd
```

`-f` is used when `clean.requireForce` in configuration is set to `true` (default).

`-n` can be used for dry run.