# Loops and Types

## `yield` keyword

The `yield` keyword specifies that the resulting value should be returned each iteration.

There is the normal syntax.

```scala
val numbers : List[Int] = List(1, 2, 3, 4)
val squares : List[Int] = for (n <- numbers) yield n * n 
```

There is also a more functional looking syntax, but its the same thing.

```scala
val squares : List[Int] = for {
    n <- numbers
} yield n * n  
```

### Example 1

The following is a basic demostration of a type mismatch

```scala
object testing_stuff extends App {
    def test() : Unit = {
        var t = "hello!" // type : String
        var x = 0        // type : Int

        for(i <- 1 to 3) x += i // x = 6
        
        t = 4   // error: type mismatch ~ String != Int
        x += t  // error: type mismatch 
        
        println(x)
    }
}
```

### Example 2

In the following example something to take note of is that you can denote the variable `t` by two equivalent types

- `(Boolean, Boolean, Boolean)`
- `Tuple3[Boolean, Boolean, Boolean]`

Both types express a tuple of 3 boolean values

```scala
class A(val x : Int, val y : Int) 
    def test() : Unit = {
        val x = new A(2, 5) // type : A
        val y = new A(2, 5) // type : A
        val z = x           // type : A

        val t = (x.x == y.x && x.y == y.y, x == y, x == z) 
        println(t)
    }
    test()
```

