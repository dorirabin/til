# How does ThreadLocal store data

ThreadLocal is a per-thread storage. You may treat that as a Map<Thread, Object>. However, if a thread dies, will the entry still stay in memory?

From the Java Api documentation ([Link](https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html)), it says it doesn't.

> Each thread holds an implicit reference to its copy of a thread-local variable as long as the thread is alive and the ThreadLocal instance is accessible; after a thread goes away, all of its copies of thread-local instances are subject to garbage collection (unless other references to these copies exist).

Let's go through the source.

ThreadLocal ([Full source](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/java/lang/ThreadLocal.java))

```java
public class ThreadLocal<T> {
  public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
      map.set(this, value);
    else
      createMap(t, value);
  }

  ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
  }

  static class ThreadLocalMap {
    static class Entry extends WeakReference<ThreadLocal<?>> {
      Object value;
    }
  }
}
```

Thread ([Full source](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/java/lang/Thread.java))

```java
public class Thread {
  ThreadLocal.ThreadLocalMap threadLocals;

  private void exit() {
    threadLocals = null;
  }
}
```

From the source code, you can see the map is Map<ThreadLocal, Object> instead of Map<Thread, Object>. The map belongs to Thread, not ThreadLocal. When the thread dies, the map is set to null. No reference from ThreadLoal to the map stays.
