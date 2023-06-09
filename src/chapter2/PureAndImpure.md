# Pure and Impure functions

## **Pure Function**
Pure functions have 3 main properties
1. Output depends **only** on input variables
2. Doesn’t mutate any hidden state 
3. Doesn’t read or write data from the outside world

### example 1

```scala
// function that doubles an integer
def double(i : Int) : Int = i * 2
```

1. Output depends only on input `i` 
2. Doesn’t mutate any hidden state 
3. Doesn’t r/w data from the outside world

### example 2
```scala
// function that recursivley calculates sum of elements in a list
def sum(list: List[Int]) : Int = list match {
	case Nil => 0
	case head :: tail => head + sum(tail) 
}
```

---

## **Impure functions**
Impure functions have 3 main properties
1. Read and write hidden inputs/outputs
2. Mutate given parameters  
3. Output depends on something other than input variables


```scala
println(f"hello world") // writes to output 

currentTimeMillis() // impure as output depends on something other than input variables
```

### example 1

```scala
// function takes two parameters
def contains(l: List[Int], n: Int): Boolean = {
    // output only depends on two parameters
		for(x <- l) if(x == n) return true // does not mutate parameters 
    return false
}
// therefore function is pure
```

### example 2

```scala
// takes a mutable stack
def give3rd(s: mutable.Stack[Int]): Int {
	// mutates the stack 
	s.pop
	s.pop
	s.top
} 
// due to it mutating the given parameter the function is impure
```