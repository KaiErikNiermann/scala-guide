# Final keyword

## `final` keyword

> Used in many different contexts generally to **prevent overriding or reassigning**, a bit like `const` in other languages.
> 

### classes

```scala
final class A 

class B extends A // ! not allowed
// not allowed in any context
```

### methods / functions

```scala
class Base {
	final def foo() : Unit = { }
}

class Dervied extends Base {
	// error: method foo cannot be overridden
	override def foo() : Unit = { } 
}
```

### variables

```scala
final val x = 10
// x = 20  // Error: cannot reassign a value to a final variable
```

### code blocks

```scala
val x = 10

final {
  val y = 20
	// Can access x because it is defined outside the final block
  println(x)  
  // x = 30   // Error: cannot reassign a value to x because outside final block
}
```