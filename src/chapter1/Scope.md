# Scope

```scala
val x = 10 
{
	val x = f(3)
	x * x 
}
```

- Definitions inside block only visible from within block
- Definitions inside a block shadow definitions outside a block
- Definitions of outer block are visible unless they are shadowed by inner block.

## example #1

<script src="https://scastie.scala-lang.org/LKD5J56BToO3pNIsURSzqg.js?theme=dark"></script>
<!-- ```scala
object testing_stuff extends App {
    var x = 6 // global x
    var y = 2 // global y

    def show(x: Int, y: Int) : Unit = print(f"$x,$y ")

    def m1(x: Int, yi: Int) : Int = {
        var y = yi + 1 // shadow y 
        y += x         // shadow y 
        return y + 1   // shadow y 
    }

    def test() : Unit = {
        x = m1(y, x) // shadow x
        y = m1(x, y) // shadow y 
        show(y, x)
    }
    test()
}
``` -->

We can make what shadows what a bit clearer by directly showing the computed values as such.
```scala
m1(y = 2, x = 6) 
	x = 2, yi = 6
	shadowed y = 6 + 1 = 7
	shadowed y += 2 = 9
	return shadowed y + 1 = 10

shadow x = 10 

m1(shadow x = 10, y = 2)
	x = 10, yi = 2
	shadow y = 2 + 1 = 3
	shadow y += 10 = 13
	return shadow y + 1 = 14

shadow y = 14

show(shadow y = 14, shadow x = 10)
	x = 14, y = 10

output -> "14,10"
```