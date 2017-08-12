# Compress file separately

```shell
find . -iname <filename_pattern> -type f -print0 | xargs -0I {} tar -cvf {}.tar.gz {}
```
