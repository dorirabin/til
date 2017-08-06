# Weak reference and string pooling

Reference: [WeakReference string didn't garbage collected? How?](https://stackoverflow.com/questions/14494875/weakreference-string-didnt-garbage-collected-how)

Because of string pooling, string will not be garbage collected if it is literal, so the weak reference will still hold the value after gc.

## Example

```java
WeakReference<String> str1 = new WeakReference("Hello world 1");
WeakReference<String> str2 = new WeakReference(new String("Hello world 2"));
System.out.println(str1.get());
System.out.println(str2.get());
System.out.println("gc");
System.gc();
System.out.println(str1.get());
System.out.println(str2.get());
```

## Result

```
Hello world 1
Hello world 2
gc
Hello world 1
null
```