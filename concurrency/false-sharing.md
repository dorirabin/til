# False sharing

When a thread modifies a variable, the CPU cache is invalidated. CPU needs to get the latest value from the main memory instead of cache.

Caches are actually grouped as `cache line` in practice. It is possible that multiple variables are cached in a single cache line.

When a thread modifies a variable, caches of other variables on the same cache line are invalidated. CPU needs to get the values of all variables on the same cache line from main memory. It is like the situation for single variable. This is called `false sharing` which degrades the performance.

Pseduo code example: [1]

```
int sum[THREAD_COUNT];

function calculate(threadId) {
	sum[threadId] = 0

	for (i=0; i<N; ++i) {
		sum[threadId] += random
	}
}
```

From the above example, all elements in `sum` may be put in the same cache line.Although each thread doesn't share element with other threads, CPU needs to get all elements from main memory every time a thread wants to access its element.

References:

[1] [Avoiding and Identifying False Sharing Among Threads](https://software.intel.com/en-us/articles/avoiding-and-identifying-false-sharing-among-threads)
