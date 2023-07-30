# Classes and Functions

## Primitives 

are passed by **value** and not by reference, any changes to the original object will *not* affect changes to the passed object / function etc.

```scala
class A(var a : Int) 
def test() : Unit = {
    var a : Int = 10; 
    var foo : A = new A(a)
    println(foo.a)
    
    a = 20
    println(foo.a)
}
```

In the above example we can see that even though we reassign `a` to 20 the fact that its passed by value. That is, a new instance is created for `A` the new assignment doesnt modify the separate instance `foo.a`. You could also think of this as `a` and `foo.a` having being in separate memory locations.



## Objects 

are passed by **reference** and not by value, any changes to the original object (aside from initializing it to a new object) *will* lead to changes into the passed class.

```scala
class A(var a : Int) 
class B(var a : A)

def test() : Unit = {
    var a : Int = 10; 
    var foo : A = new A(a)
    var bar : B = new B(foo)
    println(bar.a.a)
    foo.a = 20
    println(bar.a.a)
}
```

Objects on the other hand due to being passed by reference are linked. Meaning that if we reassign `foo.a` then `bar.a.a` will have been altered both `foo.a` and `bar.a.a` point to the same memory location, so intuitively modifying the value at this location means both references change appropriately.

For the following examples I recommend you read through the comments and maybe print out a few of the values. Fundamentally it all just reduces to the two principles of you either creating a new instance of something or passing something by reference.

### Example 1

```scala
object testing_stuff extends App {
    class A(var b: B) {
        def increment() : Unit =
            b = new B(b.g + 1) // reassignment does not modify previous assingments
    }

    class B(var g: Int) {
        def increment(x: Int) : Unit =
            g += x
    }

    object Test {
        def test() : Unit = {
            var b = new B(10)  // b.g = 10
            var a = new A(b)   // a.b.g = 10
            var x = 3
            b.increment(3)     // b.g = 13
            x -= a.b.g         // x = -7
            a.increment()      // new a.b.g = 11
            x += b.g           // x = 6
            b = a.b            // b.g = 11 | Note ~ assigning to new a.b.g
            a.increment()      // new a.b.g = 12
            x += b.g           // x = 17 | Note ~ b.g = 11 not 12 
            println(x)         // out -> x = 17
        }
    }
    Test.test()
}
```

Going through it line by line :

1. `b.g` starts out being equal to 10

2. `a.b.g` is also 10, I think still straightforward enough at this point

3. `x` is 3

4. `b.increment(3)` calls the increment function of b and increments `b.g` to 13

5. Subtracting `a.b.g` (which is 10) from `x` means `x = -7`

6. `a.increment()` is called, and the `increment()` method in class `A` is executed. It creates a new instance of class `B` with the value `b.g + 1`, which is `10 + 1 = 11`. The variable `a.b` is updated to point to this new instance of class `B`, so `a.b.g` becomes `11`.

7. After the increment operation, `x` is `-7`, and now it is incremented by `b.g`, which is `13`. So, `x` becomes `6`.

8. The line `b = a.b` reassigns the variable `b` to the same reference as `a.b`. Now both `b` and `a.b` point to the same instance of class `B` with `g = 11`.

9. The function `a.increment()` is called again, and it creates a new instance of class `B` with the value `b.g + 1`, which is `11 + 1 = 12`. The variable `a.b` is updated to point to this new instance of class `B`, so `a.b.g` becomes `12`.

10. Now, `x` is `6`, and it is incremented by `b.g`, which is `11`. So, `x` becomes `17`.

11. Finally, the value of `x` is printed, and it is `17`.

### Example 2

```scala
object testing_stuff extends App {
    class A(var g: Int) {
        def increment() : Unit = {
            g += 1
            if(g > 5) 
                g = 0
        }
        def set(x : Int) : Unit = g = x
    }

    class B(var a : A) {
        def increment() : Unit = a.increment()    
    }
    object Test {
        def test() : Unit = {
            var a = new A(0)    // a.g = 0
            var b = new B(a)    // b.a.g = 0
            a.increment()       // a.g = 1, b.a.g = 1
            b.increment()       // a.g = 2, b.a.g = 2
            a.set(5)            // a.g = 5, b.a.g = 5
            b.increment()       // a.g = 0, b.a.g = 0
            b.a.g -= 3          // a.g = -3, b.a.g = -3
            a.increment()       // a.g = -2, b.a.g = -2
            a = new A(4)        // a.g = 4, b.a.g = -2
            a.increment()       // a.g = 5, b.a.g = -2
            print(b.a.g)        // out -> x = -2

        }    
    }
    Test.test()
}
```

Going through it line by line : 

1. Create an instance of class `A` with `g = 0` and assign it to the variable `a`. At this point, `a.g` is `0`.

2. Create an instance of class `B` with `a` as the argument (which is the instance of class `A` we just created). Assign this new instance of class `B` to the variable `b`. Now `b.a.g` is also `0`.

3. Call the `increment()` method of class `A` on the instance `a`. This method increments the value of `g` by 1, so `a.g` becomes `1`, and `b.a.g` also becomes `1`.

4. Call the `increment()` method of class `B` on the instance `b`. This method calls the `increment()` method of class `A` on its member variable `a`. As a result, `a.g` and `b.a.g` both become `2`.

5. Call the `set()` method of class `A` on the instance `a` and pass `5` as the argument. This method sets the value of `g` to `5`, so `a.g` and `b.a.g` both become `5`.

6. Call the `increment()` method of class `B` on the instance `b`. This method calls the `increment()` method of class `A` on its member variable `a`. The `increment()` method in class `A` increments the value of `g` by 1, but also checks if `g` is greater than `5`. Since `g` is `5`, it is reset to `0`. As a result, `a.g` and `b.a.g` both become `0`.

7. Perform `b.a.g -= 3`. This means subtract `3` from the value of `b.a.g`. As a result, `a.g` and `b.a.g` both become `-3`.

8. Call the `increment()` method of class `A` on the instance `a`. This method increments the value of `g` by 1, so `a.g` becomes `-2`, and `b.a.g` also becomes `-2`.

9. Create a new instance of class `A` with `g = 4` and assign it to the variable `a`. Now `a.g` is `4`, but `b.a.g` still remains `-2`.

10. Call the `increment()` method of class `A` on the instance `a`. This method increments the value of `g` by 1, so `a.g` becomes `5`, and `b.a.g` remains `-2`.

11. Finally, `print(b.a.g)`. The value of `b.a.g` is `-2`, so the output is `-2`.

In summary, the code creates two classes, `A` and `B`, and performs various increment and assignment operations on their member variables. The output of the code is `-2`.
