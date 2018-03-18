# Tail recursion

Kotlin supports tail recursion optimization by `tailrec`

```kotlin
class BinarySearch<T: Comparable<T>> {
    fun search(array: Array<T>, target: T): Int {
        return when {
            array.isEmpty() -> -1
            else -> search(array, target,0, array.size - 1)
        }
    }

    private tailrec fun search(array: Array<T>, target: T, lo: Int, hi: Int): Int {
        val mid = (lo + hi) / 2
        return when {
            lo > hi -> -1
            array[mid] == target -> mid
            array[mid] > target -> search(array, target, lo, mid - 1)
            else -> search(array, target, mid + 1, hi)
        }
    }
}
```
