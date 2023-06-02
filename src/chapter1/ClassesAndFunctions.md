# Classes and Functions

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

**Primitives** - are passed by **value** and not by reference, any changes to the original object will not affect changes to the passed object / function etc.

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
**Objects** - are passed by **reference** and not by value, any changes to the original object (aside from initializing it to a new object) will lead to changes into the passed class.

### example #1

<script src="https://scastie.scala-lang.org/0dVaZ3egTJiTEKgvVY4EGA.js?theme=dark"></script>
### example #2
<script src="https://scastie.scala-lang.org/FxTJsMWOQx6qOBHxkKaVHQ.js?theme=dark"></script>