# Ending slash

## Ending slash for source directory

Behaviour changes when an ending slash is added to the source path.

Suppose we have a source directory with structure as follow.

```
src_directory
|- file_a
|- file_b
```

### Ending slash is provided

```
rsync -a src_directory/ target_directory
```

Result

```
target_directory
|- file_a
|- file_b
```

### Ending slash is not provided

```
rsync -a src_directory target_directory
```

Result

```
target_directory
|- src_directory
  |- file_a
  |- file_b
```

## Ending slash for target directory

Behaviour is same whether there is an ending slash added to target directory or not.
