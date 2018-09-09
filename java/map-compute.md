# Map compute()

Consider we want to create a frequency map. When we build the map, we need to check if the key exists or not.

```
String str = "abbc";
Map<Character, Integer> frequencyMap = new HashMap<>();
str.chars().forEach(c -> {
    char ch = (char) c;
    if (frequencyMap.containsKey(ch)) {
        frequencyMap.put(ch, frequencyMap.get(ch) + 1);
    } else {
        frequencyMap.put(ch, 1);
    }
});
```

However, with `Map`'s `compute` method, we can check the existence of mapping. If the mapping does not exist, then `v` is null.

Therefore we can rewrite the above code to the following.

```
String str = "abbc";
Map<Character, Integer> frequencyMap = new HashMap<>();
str.chars()
  .mapToObj(c -> (char) c)
  .forEach(c -> frequencyMap.compute(c, (k, v) -> v == null ? 1 : v + 1));
```

We can also remove the mapping by returning `null` from the lambda function.
