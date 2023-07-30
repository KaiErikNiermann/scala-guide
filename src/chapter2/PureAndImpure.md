# Pure and Impure Functions

## **Pure Function**

Pure functions have three main properties:

1. Output depends **only** on input variables.
2. Doesn’t mutate any hidden state.
3. Doesn’t read or write data from the outside world.

### Example 1 - Pure Function

```scala
// Function that doubles an integer
def double(i: Int): Int = i * 2
```

1. Output depends only on input `i`.
2. Doesn’t mutate any hidden state.
3. Doesn’t read or write data from the outside world.

### Example 2 - Pure Function

```scala
// Function that recursively calculates the sum of elements in a list
def sum(list: List[Int]): Int = list match {
  case Nil => 0
  case head :: tail => head + sum(tail)
}
```

---

## **Impure Functions**

Impure functions have three main properties:

1. Read and write hidden inputs/outputs.
2. Mutate given parameters.
3. Output depends on something other than input variables.

### Example 1 - Impure Functions

```scala
println(f"hello world") // Writes to output

currentTimeMillis() // Impure as output depends on something other than input variables
```

### Example 2 - Impure Functions

```scala
// Function takes two parameters
def contains(l: List[Int], n: Int): Boolean = {
  // Output only depends on two parameters
  for (x <- l) if (x == n) return true // Does not mutate parameters
  return false
}
// Therefore, the function is pure
```

### Example 3 - Impure Functions

```scala
// Takes a mutable stack
def give3rd(s: mutable.Stack[Int]): Int {
  // Mutates the stack
  s.pop
  s.pop
  s.top
}
// Due to it mutating the given parameter, the function is impure
```
