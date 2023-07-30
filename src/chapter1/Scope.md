# Scope

```scala
val x = 10 
{
    val x = f(3)
    x * x
}
```

The 3 main things you should remember.

- Definitions inside block only visible from within block
- Definitions inside a block shadow definitions outside a block
- Definitions of outer block are visible unless they are shadowed by inner block.

## Example

```scala
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
```

### explanation

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

And here is the step by step description of the process shown above.

1. Call the function `m1` with the arguments `y = 2` and `x = 6`.

2. Inside the function `m1`, assign the value of `x` to `yi`, so `yi = 6`.

3. Compute the shadowed value of `y` by adding 1 to `yi`, so `shadowed y = 6 + 1 = 7`.

4. Increment the shadowed value of `y` by 2, so `shadowed y += 2 = 9`.

5. Return the result of adding 1 to the shadowed value of `y`, so `return shadowed y + 1 = 10`.

6. Assign the value 10 to the variable `shadow x`.

7. Call the function `m1` again, but this time with the arguments `shadow x = 10` and `y = 2`.

8. Inside the function `m1`, assign the value of `shadow x` (which is 10) to `x`, and `y` to `yi`, so `x = 10` and `yi = 2`.

9. Compute the shadowed value of `y` by adding 1 to `yi`, so `shadow y = 2 + 1 = 3`.

10. Increment the shadowed value of `y` by 10, so `shadow y += 10 = 13`.

11. Return the result of adding 1 to the shadowed value of `y`, so `return shadow y + 1 = 14`.

12. Assign the value 14 to the variable `shadow y`.

13. Call the function `show` with the arguments `shadow y = 14` and `shadow x = 10`.

14. Inside the function `show`, assign the value of `shadow y` (which is 14) to `x`, and the value of `shadow x` (which is 10) to `y`, so `x = 14` and `y = 10`.

15. The output of the whole process is "14,10".