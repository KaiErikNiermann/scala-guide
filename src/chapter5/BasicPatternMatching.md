# Basic pattern matching

## basic pattern matching

```scala
def matchTest(x: Int): String = x match {
  case 1 => "one"
  case 2 => "two"
  case _ => "other"
}
matchTest(3)  // returns other
matchTest(1)  // returns one
```

```scala
// basic format 
thingToMatch match {
	case A => 
	case B => 
	case _ => 
} 
```

### matching on case classes

```scala
sealed trait Notification
case class Email(sender: String, title: String, body: String) extends Notification
case class SMS(caller: String, message: String) extends Notification
case class VoiceRecording(contactName: String, link: String) extends Notification

def showNotification(notification: Notification): String = {
	notification match {
		case Email(sender, title, _) =>
			s"You got an email from $sender with title: $title"
		case SMS(number, message) =>
			s"You got an SMS from $number! Message: $message"
		case VoiceRecording(name, link) =>
			s"You received a Voice Recording from $name! Click the link to hear it: $link"
	}
}

val someSms = SMS("12345", "Are you there?")
val someVoiceRecording = VoiceRecording("Tom", "voicerecording.org/id/123")

println(showNotification(someSms)) 
println(showNotification(someVoiceRecording))
```

****************Concept**************** 

> Here we defined 3 case classes, and are matching by these classes and using the parameters in the return values. 
Note ~ we can use the wildcard `_` to ignore a particular field
> 

****************`sealed` keyword**

> Used to control the extension of classes and traits. Declaring a class or trait as sealed restricts where we can define its subclasses - ********************************they have to be defined in the same source file********************************.
> 

### pattern guard

```scala
...	
	case SMS(number, message) if importantPeopleInfo.contains(number) =>
		"SMS from someone important!"
...

val importantPeopleInfo = Seq("123-1234, "some message")
```

> Used to make cases more specific. Meaning it won’t match if the Boolean expression after the `if` evaluates to false.
> 

### matching on type only

```scala
... 
	case SMS => ...
	case Email => ... 
...
```

> We can match only based off the type of the case class
> 

⚠️ ~ ****************Warning**************** : If you explicitly define some user defined type by its supertype, then overloaded functions will match with explicitly defined types not the actual type.

```scala
class A
class A1 extends A

def g(x: A): Unit = println("A")
def g(x: A1): Unit = println("A1")

var foo : A = new A1
g(foo)
```

### example #1 ~ basic pattern matching

```scala
def test : String = {
	Some(Some(None)) match {
		case Some(Some(_)) => "test" // matches with this case
		case Some(_) => "fizz"
		case Some(Some(None)) => "buzz"
		case _ => "nope"
	}
}
println(test) // "test"
```

**********Idea**********

> `Some(Some(_))` is a valid case and since pattern matching just matches with the first valid case the function returns “test”
> 

### example #2

```scala
object testing_stuff extends App {
	abstract class Expression 
	case class Const(value: Int) extends Expression
	case class Var(name: String) extends Expression
	case class Op(lhs: Expression, op: String, rhs: Expression) extends Expression

	object Q {
		def simplify(exp: Expression): Expression = 
			exp match {
				case Op(Const(0), "+", r) => simplify(r)
				case Op(l, "*", Const(0)) => Const(0)
				case Op(l, op, r) => Op(simplify(l), op, simplify(r))
				case x => x
			}
			
		val v1: Expression = simplify(
			Op(
				Const(0),
				"+",
				Op(Var("x"), "+", Op(Const(5), "*", Const(0)))
				)
			)
	}
}
```

We can break this down by the different cases that are recursively matched

```scala
Op(Const(0), "+", r)
	simplify r = exp = Op(Var("x"), "+", Op(Const(5), "*", Const(0)))
	Op(l, op, r)
		l = exp = Var("x") => Var("x")
		r = exp = Op(Const(5), "*", Const(0))
			OP(l, "*", Const(0)) => Const(0)

simplify r =return> Op(Var("x"), "+", Const(0))
```

**************************************another expression**************************************

```scala
...
val v1: Expression = simplify(
	Op(
		Op(Var("y"), "*", Const(0)),
		"+",
		Var("x")
	)
)
```

> Basically similar idea here, you can see Since lhs is of type `Op` its gonna match with the last one. Lhs simplifies down to `Const(0)` and as `Var("x")` just matches to `x` as its not an operation it yields itself so we end up with `Op(Const(0), "+", Var("x"))`
> 
