# find -print0 and xargs -0

References: [Linux - find print0 & xargs
](https://davidlu1001.github.io/2016/02/20/Linux-find-print0-xargs/)

Suppose we want to remove all files whose file names match a pattern. But some files whose names contain whitespace.

## Setup

```shell
touch "test1.txt"
touch "test 2.txt"
```

## find

Try find without -print0

```shell
find . -name "test*.txt"
```

Results are separated by newline

```shell
./test1.txt
./test 2.txt
```

Try find with -print0

```shell
find . -name "test*.txt" -print0
```

Results are separated by null character

```shell
./test1.txt./test 2.txt
```

## xargs

Try find without -print0

```shell
find . -name "test*.txt" | xargs rm
```

test1.txt is removed, but test 2.txt is not because xargs also treat whitespace as delimiter.

```shell
rm: cannot remove './test': No such file or directory
rm: cannot remove '2.txt': No such file or directory
```

Try find with -print0 and xargs with -0

```shell
find . -name "test*.txt" -print0 | xargs -0 rm
```

Both test1.txt and test 2.txt are removed. The parameter -0 asks xargs to treat the pipe input with only null character as delimiter.
