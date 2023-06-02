# Pure and Impure functions

### **Pure Function**

> - Output depends **only** on input variables
> - Doesn’t mutate any hidden state 
> - Doesn’t read or write data from the outside world

**Examples**

```scala
// function that doubles an integer
def double(i : Int) : Int = i * 2
```

→ Output depends only on input `i` 
→ Doesn’t mutate any hidden state 
→ Doesn’t r/w data from the outside world

```scala
// function that recursivley calculates sum of elements in a list
def sum(list: List[Int]) : Int = list match {
	case Nil => 0
	case head :: tail => head + sum(tail) 
}
```

### **impure functions**

> → Read and write hidden inputs/outputs
→ Mutate given parameters  
→ Output depends on something other than input variables
> 

```scala
println(f"hello world") // writes to output 

currentTimeMillis() // impure as output depends on something other than input variables
```

### **example #1**

```scala
// function takes two parameters
def contains(l: List[Int], n: Int): Boolean = {
    // output only depends on two parameters
		for(x <- l) if(x == n) return true // does not mutate parameters 
    return false
}
// therefore function is pure
```

### example #2

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