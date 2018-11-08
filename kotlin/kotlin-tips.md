# Kotlin tips for Java developer

## Use elvis operator `?:` to ensure null safety

Without elvis operator

```
var list = map.get(id)
if (list == null) {
  list = ArrayList<Cat>()
}
```

With elvis operator

```
val list = map.get(id) ?: ArrayList<Cat>()
```

It is not only for readability. Another benefit of using elvis operator is we can declare the variable as constant.