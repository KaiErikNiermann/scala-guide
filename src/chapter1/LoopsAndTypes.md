# Loops and Types

### `yield` keyword

> The `yield` keyword specifies that the resulting value should be returned each iteration
> 

<script src="https://scastie.scala-lang.org/PUkDScUJQDq2iqu5CyYMnQ.js?theme=dark"></script>
<!-- ```scala
val numbers : List[Int] = List(1, 2, 3, 4)
val squares : List[Int] = for (n <- numbers) yield n * n 
// squares = List(1, 4, 9, 16)
// alternative `functional looking` syntax
val squares2 : List[Int] = for {
	n <- numbers
} yield n * n  
``` -->

### example #1

<script src="https://scastie.scala-lang.org/qevAgn8SSpy1sPciucs9Tw.js?theme=dark"></script>
<!-- ```scala
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
``` -->

### example #2

<script src="https://scastie.scala-lang.org/66MM2QGbRa6lqAIAK39BNA.js?theme=dark"></script>
<!-- ```scala
class A(val x : Int, val y : Int) 
    def test() : Unit = {
        val x = new A(2, 5) // type : A
        val y = new A(2, 5) // type : 
        val z = x           // type : A
				// type : Tupel3[Boolean, Boolean, Boolean]
				// also : (Boolean, Boolean, Boolean)
        val t = (x.x == y.x && x.y == y.y, x == y, x == z) 
        println(t)
    }
    test()
``` -->

