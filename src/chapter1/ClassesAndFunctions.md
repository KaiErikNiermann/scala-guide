# Classes and Functions


**Primitives** - are passed by **value** and not by reference, any changes to the original object will not affect changes to the passed object / function etc. 
<script src="https://scastie.scala-lang.org/ClyBy11xS1eoDOrwVziiqw.js?theme=dark"></script>
<!-- ```scala
class A(var a : Int) 
def test() : Unit = {
    var a : Int = 10; 
    var foo : A = new A(a)
    println(foo.a)
    a = 20
    println(foo.a)
}
``` -->
In the above example we can see that even though we reassign `a` to 20 the fact that its passed by value. That is, a new instance is created for `A` the new assignment doesnt modify the separate instance `foo.a`. You could also think of this as `a` and `foo.a` having being in separate memory locations.

<br>
</br>

**Objects** - are passed by **reference** and not by value, any changes to the original object (aside from initializing it to a new object) will lead to changes into the passed class.
<script src="https://scastie.scala-lang.org/g3P1nYdZSPeCCDcq6JGrbg.js?theme=dark"></script>
<!-- ```scala
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
``` -->
Objects on the other hand due to being passed by reference are linked. Meaning that if we reassign `foo.a` then `bar.a.a` will have been altered both `foo.a` and `bar.a.a` point to the same memory location, so intuitively modifying the value at this location means both references change appropriately.

<br></br>
For the following examples I recommend you read through the comments and maybe print out a few of the values. Fundamentally it all just reduces to the two principles of you either creating a new instance of something or passing something by reference.
### example 1
<script src="https://scastie.scala-lang.org/0dVaZ3egTJiTEKgvVY4EGA.js?theme=dark"></script>

### example 2
<script src="https://scastie.scala-lang.org/FxTJsMWOQx6qOBHxkKaVHQ.js?theme=dark"></script>