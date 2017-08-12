# Search for text in multiple archive files

```shell
find . -iname "*.tar.gz" -print0 | xargs -0 zgrep -a <search_word>
```