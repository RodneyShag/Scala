<p align="center">
    <img src="images/scala_logo.png">
</p>

- [Introduction](#introduction)
- [Basics](#basics)
- [Unified Types](#unified-types)
- [Classes](#classes)
- [Tuples](#tuples)
- [Class composition with Mixins](#class-composition-with-mixins)
- [Higher Order Functions](#higher-order-functions)
- [Nested Methods](#nested-methods)
- [Multiple Parameter Lists (Currying)](#multiple-parameter-lists-currying)
- [Pattern matching](#pattern-matching)
- [Singleton objects](#singleton-objects)
- [Regular Expression Patterns](#regular-expression-patterns)
- [For Comprehensions](#for-comprehensions)
- [Generic Classes](#generic-classes)
- [Variances](#variances)
- [Upper Type Bounds](#upper-type-bounds)
- [Lower Type Bounds](#lower-type-bounds)
- [Inner Classes](#inner-classes)
- [Self-type](#self-type)
- [Implicit Parameters](#implicit-parameters)
- [Polymorphic Methods](#polymorphic-methods)
- [Operators](#operators)
- [By-name Parameters](#by-name-parameters)
- [Default Parameter Values](#default-parameter-values)
- [Packages and Imports](#packages-and-imports)
- [More Notes](#more-notes)
- [References](#references)


This repo is a concise summary and _replacement_ of the [Tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html) tutorial. Using the hyperlinks below is optional.


## [Introduction](https://docs.scala-lang.org/tour/tour-of-scala.html)

Scala is both an object-oriented and functional programming language. It is a statically-typed language, but uses _type inference_ so the user is not required to annotate code with redundant type information.

Online compiler: [Scala Fiddle](https://scalafiddle.io)


## [Basics](https://docs.scala-lang.org/tour/basics.html)

#### Values vs. Variables

- `val` is a constant
- `var` is a variable

```scala
val x = 2;
x = 3; // does not compile
```

```scala
var x = 2;
x = 3; // compiles
```

#### Declaring type of variable

Types of values can be inferred, but you can also explicitly state the type:

```scala
val x: Int = 2
```

#### Functions

```scala
(x: Int) => x + 1                   // anonymous (no-name) function
val getTheAnswer = () => 42         // named function (0 parameters)
val addOne = (x: Int) => x + 1      // named function (1 parameter)
val add = (x: Int, y: Int) => x + y // named function (2 parameters)
```

#### Blocks & Returning values

A block evaluates to the last line in the block.
```scala
println({
  val x = 1 + 1
  x + 1
}) // 3
```
For methods, the `return` statement is not needed, since the value of the last line is returned.

#### Methods

0 parameters

```scala
def name: String = System.getProperty("user.name")

println("Hello, " + name + "!")
```

1 parameter list

```scala
def add(x: Int, y: Int): Int = x + y

println(add(1, 2)) // 3
```

2 parameter lists

```scala
def addThenMultiply(x: Int, y: Int)(multiplier: Int): Int = (x + y) * multiplier

println(addThenMultiply(1, 2)(3)) // 9
```

#### Classes

`Unit` is like `void` in Java

```scala
class Greeter(prefix: String, suffix: String) {
  def greet(name: String): Unit =
    println(prefix + name + suffix)
}
```


```scala
val greeter = new Greeter("Hello, ", "!")
greeter.greet("Scala developer") // Hello, Scala developer!
```

#### Case Classes

case classes are __immutable__ and __compared by value__

```scala
case class Point(x: Int, y: Int)
```

The `new` keyword is not necessary when creating a Case Class

```scala
val point = Point(1, 2)
val anotherPoint = Point(1, 2)
```

```scala
if (point == anotherPoint) {
  println("same point") // this will print
}
```

#### Objects

The object keyword is used to create a singleton. This feature replaces the `static` keyword in Java.

```scala
object IdFactory {
  private var counter = 0
  def create(): Int = {
    counter += 1
    counter
  }
}
```

```scala
val newId: Int = IdFactory.create()
println(newId) // 1
val newerId: Int = IdFactory.create()
println(newerId) // 2
```

#### Traits

Traits are like interfaces in Java

```scala
trait Greeter {
  def greet(name: String): Unit
}
```

Traits can have default implementations:

```scala
trait Greeter {
  def greet(name: String): Unit =
    println("Hello, " + name + "!")
}
```

- Extend traits using `extends` keyword
- Override an implementationusing `override` keyword.

```scala
class DefaultGreeter extends Greeter

class CustomizableGreeter(prefix: String, postfix: String) extends Greeter {
  override def greet(name: String): Unit = {
    println(prefix + name + postfix)
  }
}

val greeter = new DefaultGreeter()
greeter.greet("Scala developer") // Hello, Scala developer!

val customGreeter = new CustomizableGreeter("How are you, ", "?")
customGreeter.greet("Scala developer") // How are you, Scala developer?
```

#### Main method

Syntax for Scala's main method:
```scala
object Main {
  def main(args: Array[String]): Unit =
    println("Hello, Scala developer!")
}
```


## [Unified Types](https://docs.scala-lang.org/tour/unified-types.html)

![Type Hierarchy](./images/typeHierarchy.svg)


## [Classes](https://docs.scala-lang.org/tour/classes.html)

#### Private Members and Getter/Setter Syntax

Setters use special syntax: `_=`

```scala
class Point {
  private var _x = 0
  private val bound = 100

  // Getter
  def x = _x

  // Setter
  def x_= (newValue: Int): Unit = {
    if (newValue < bound) _x = newValue else printWarning
  }

  private def printWarning = println("WARNING: Out of bounds")
}

val point1 = new Point
point1.x = 99
point1.y = 101 // prints the warning
```

[Alternative explanation on getters/setters](https://www.dustinmartin.net/getters-and-setters-in-scala/)


## [Tuples](https://docs.scala-lang.org/tour/tuples.html)

#### Accessing the elements

```scala
val ingredient = ("Sugar" , 25)

println(ingredient._1) // Sugar
println(ingredient._2) // 25
```

#### Pattern matching on tuples

Grabbing values out of the tuple:

```scala
val (name, quantity) = ingredient
println(name) // Sugar
println(quantity) // 25
```

Pattern matching using `foreach` and `case`:

```scala
val planets = List(("Mercury", 57.9), ("Venus", 108.2), ("Earth", 149.6))

planets.foreach{
  case ("Earth", distance) =>
    println(s"Our planet is $distance million kilometers from the sun")
  case _ =>
}
```

Pattern matchin in `for` loops:

```Scala
val numPairs = List((2, 5), (3, -7))
for ((a, b) <- numPairs) {
  println(a * b)
}
```

#### Tuples and case classes

Variables in tuples don't have names. If you want them to have names, use a "case class" instead.

Example: `case class Planet(name: String, distance: Double)`


## [Class composition with Mixins](https://docs.scala-lang.org/tour/mixin-class-composition.html)

This is how you can extend a subclass, and also have a "trait":

```scala
abstract class A {
  val message: String
}
class B extends A {
  val message = "I'm an instance of class B"
}
trait C extends A {
  def loudMessage = message.toUpperCase()
}
class D extends B with C

val d = new D
println(d.message)  // I'm an instance of class B
println(d.loudMessage)  // I'M AN INSTANCE OF CLASS B
```


## [Higher Order Functions](https://docs.scala-lang.org/tour/higher-order-functions.html)

Higher order functions are functions that taking another function as a parameter.

```scala
val salaries = Seq(2, 7, 4)
val newSalaries = salaries.map(x => x * 2) // List(4, 14, 8)
```

You an alternatively write the 2nd line as:

```scala
val newSalaries = salaries.map(_ * 2)
```


## [Nested Methods](https://docs.scala-lang.org/tour/nested-functions.html)

You can nest methods in Scala.


## [Multiple Parameter Lists (Currying)](https://docs.scala-lang.org/tour/multiple-parameter-lists.html)

Main use cases:
1. Passing a value to the 1st parameter list helps the 2nd parameter list infer the type
1. Partial application

Here is an example of "partial application" that lets us define `B` as `List[Int]`:

```scala
def foldLeft[B](z: B)(op: (B, A) => B): B
```

```scala
val numbers = List(1, 2, 3, 4, 5)
val numberFunc = numbers.foldLeft(List[Int]()) _

val squares = numberFunc((xs, x) => xs :+ x*x)
print(squares) // List(1, 4, 9, 16, 25)

val cubes = numberFunc((xs, x) => xs :+ x*x*x)
print(cubes)  // List(1, 8, 27, 64, 125)
```


## [Pattern matching](https://docs.scala-lang.org/tour/pattern-matching.html)

"Switch statements" from Java are called "match expressions" in Scala.

#### Matching on case classes

```scala
abstract class Notification

case class Email(sender: String, title: String, body: String) extends Notification

case class SMS(caller: String, message: String) extends Notification
```

```scala
def showNotification(notification: Notification): String = {
  notification match {
    case Email(sender, title, _) =>
      s"You got an email from $sender with title: $title"
    case SMS(number, message) =>
      s"You got an SMS from $number! Message: $message"
  }
}

val someSms = SMS("12345", "Are you there?")
println(showNotification(someSms))  // prints You got an SMS from 12345! Message: Are you there?
```

#### Pattern guards

Pattern guards are boolean expressions that make cases more specific:

```scala
def showImportantNotification(notification: Notification, importantPeopleInfo: Seq[String]): String = {
  notification match {
    case Email(sender, _, _) if importantPeopleInfo.contains(sender) =>
      "You got an email from special someone!"
    case SMS(number, _) if importantPeopleInfo.contains(number) =>
      "You got an SMS from special someone!"
    case other =>
      showNotification(other) // nothing special, delegate to our original showNotification function
  }
}
```

#### Other functionality

- __matching on type__ - You can also match on "type" of the object
- __sealed__ - Traits and classes can be marked `sealed` which means all subtypes must be declared in the same file. This assures all subtypes are known. This is useful for pattern matching because we don't need a "catch all" case.


## [Singleton Objects](https://docs.scala-lang.org/tour/singleton-objects.html)

#### Companion objects

Scala uses __companion objects__ instead of Java's `static` keyword.

An object with the same name as a class is called a __companion object__. Conversely, the class is the object’s companion class. A companion class or object can access the private members of its companion.

Use a companion object for methods and values which are not specific to instances of the companion class.

```scala
import scala.math._

case class Circle(radius: Double) {
  import Circle._
  def area: Double = calculateArea(radius)
}

object Circle {
  private def calculateArea(radius: Double): Double = Pi * pow(radius, 2.0)
}

val circle1 = Circle(5.0)

circle1.area
```


## [Regular Expression Patterns](https://docs.scala-lang.org/tour/regular-expression-patterns.html)

Any string can be converted to a regular expression using the `.r` method.

```scala
import scala.util.matching.Regex

val numberPattern: Regex = "[0-9]".r

numberPattern.findFirstMatchIn("awesomepassword") match {
  case Some(_) => println("Password OK")
  case None => println("Password must contain a number")
}
```


## [For Comprehensions](https://docs.scala-lang.org/tour/for-comprehensions.html)

`for each` loop example:

```scala
case class User(name: String, age: Int)

val userBase = List(User("Travis", 28),
  User("Kelly", 33),
  User("Jennifer", 44),
  User("Dennis", 23))

val twentySomethings = for (user <- userBase if (user.age >=20 && user.age < 30))
  yield user.name  // i.e. add this to a list

twentySomethings.foreach(name => println(name))  // prints Travis Dennis
```

`for each` loop example with 2 iterators:

```scala
def foo(n: Int, v: Int) =
   for (i <- 0 until n;
        j <- 0 until n if i + j == v)
   println(s"($i, $j)")

foo(10, 10) // prints (1, 9) (2, 8) (3, 7) (4, 6) (5, 5) (6, 4) (7, 3) (8, 2) (9, 1)
```


## [Generic Classes](https://docs.scala-lang.org/tour/generic-classes.html)

A generic `Stack`:

```scala
class Stack[A] {
  private var elements: List[A] = Nil
  def push(x: A) { elements = x :: elements }
  def peek: A = elements.head
  def pop(): A = {
    val currentTop = peek
    elements = elements.tail
    currentTop
  }
}
```

```scala
val stack = new Stack[Int]
stack.push(1)
stack.push(2)
println(stack.pop)  // prints 2
println(stack.pop)  // prints 1
```


## [Variances](https://docs.scala-lang.org/tour/variances.html)

```scala
class Foo[+A] // Covariant class
class Bar[-A] // Contravariant class
class Baz[A]  // Invariant class
```

#### Covariance

For some class `List[+A]`, making `A` covariant implies that for two types `A` and `B` where `A` is a subtype of `B`, then `List[A]` is a subtype of `List[B]`

- Given
    - `A` is `Animal`
    - `B` is `Cat`
    - `Animal` is a subtype of `Cat`
- Then
    - `List[Animal]` is a subtype of `List[Cat]`

Scala's `List` class is `sealed abstract class List[+A]`, where the type parameter `A` is covariant

#### Contravariance

Opposite of Covariance.

Let's say we literally had a printer that prints `Animal`s. Then it should be able to print `Cat`s as well. Covariance can help us model this scenario.

- Given
    - `A` is `Animal`
    - `B` is `Cat`
    - `Animal` is a subtype of `Cat`
    - `abstract class Printer[-A]`
    - `AnimalPrinter` extends `Printer[Animal]`
    - `CatPrinter` extends `Printer[Cat]`
    - `def printMyCat(printer: Printer[Cat])`
- Then
    - `Printer[Animal]` can be passed into `printMyCat`

#### Invariance

Generic classes in Scala are invariant by default. This means that they are neither covariant nor contravariant.

Example: If we make a custom `Container` class, then `Container[Cat]` is not a `Container[Animal]`. The reverse is not true either.


## [Upper Type Bounds](https://docs.scala-lang.org/tour/upper-type-bounds.html)

An upper type bound `T <: A` declares that type variable `T` refers to a subtype of type `A`

- Given
    - `Cat` extends `Pet`
    - `Lion` extends `Animal`
    - `class PetContainer[P <: Pet](p: P) { def pet: P = p }`
- Then
    - `new PetContainer[Cat](new Cat)` compiles
    - `new PetContainer[Lion](new Lion)` fails to compile

Common Pitfall: "Variances" and "Upper Type Bounds" are 2 different concepts. Notice you cannot replace `P <: Pet` with `+P` since then `new PetContainer[Lion](new Lion)` would incorrectly succeed.


## [Lower Type Bounds](https://docs.scala-lang.org/tour/lower-type-bounds.html)

Opposite of Upper Type Bounds.

Common pitfall: "functions are contravariant in their parameter types and covariant in their result types". When making a type covariant by using `+B`, we will run into a problem when using `B` as a "parameter type" to a function. This is solved by using `U >: B` instead. See [Lower Type Bounds](https://docs.scala-lang.org/tour/lower-type-bounds.html) for a full example.


## [Inner Classes](https://docs.scala-lang.org/tour/inner-classes.html)

Scala allows classes to have other classes as members.


## [Compound Types](https://docs.scala-lang.org/tour/compound-types.html)

If you have 2 traits: `Cloneable` and `Resetable`, then the syntax for a function to take an object with those 2 traits is:

```scala
def cloneAndReset(obj: Cloneable with Resetable): Cloneable = {
  //...
}

```

If it was 3 or more traits, the syntax would be `Trait1 with Trait2 with Trait3`


## [Self-type](https://docs.scala-lang.org/tour/self-types.html)

Unlikely I'll use this often. Self-types are used when 1 trait depends on another trait, but doesn't "extend" it. See [Self-type](https://docs.scala-lang.org/tour/self-types.html) for an example.


## [Implicit Parameters](https://docs.scala-lang.org/tour/implicit-parameters.html)

This is when we mark a variable `implicit` in it's declaration, and `implicit` where it's used in a function, and the compiler will try to match the 2 together.

- Given:
    - value: `implicit val stringMonoid: Monoid[String]`
    - value: `implicit val intMonoid: Monoid[Int]`
    - method definition: `def sum[A](xs: List[A])(implicit m: Monoid[A]): A`
- Then
    - `m` will be matched with `intMonoid` if we call `sum(List(1, 2, 3))`

See [Implicit Parameters](https://docs.scala-lang.org/tour/implicit-parameters.html) for the full example.


## [Polymorphic Methods](https://docs.scala-lang.org/tour/polymorphic-methods.html)

#### Omitting the Type

The compiler knows `businessName` is a String:
```scala
val businessName = "Montreux Jazz Café"
```

The compiler knows an `Int` will be returned:
```scala
def squareOf(x: Int) = x * x
```

For recursive functions, the compiler can't know the return type. The following code will fail:
```scala
def fac(n: Int) = if (n == 0) 1 else n * fac(n - 1)
```

Compiler can also infer the types:
```scala
case class MyPair[A, B](x: A, y: B)
val p = MyPair(1, "scala") // type: MyPair[Int, String]

def id[T](x: T) = x
val q = id(1)              // type: Int
```


## [Operators](https://docs.scala-lang.org/tour/operators.html)

It's easy to "overload" an operator:

```scala
case class Vec(x: Double, y: Double) {
  def +(that: Vec) = Vec(this.x + that.x, this.y + that.y)
}

val vector1 = Vec(1.0, 1.0)
val vector2 = Vec(2.0, 2.0)

val vector3 = vector1 + vector2
vector3.x  // 3.0
vector3.y  // 3.0
```


## [By-name Parameters](https://docs.scala-lang.org/tour/by-name-parameters.html)

_By-name_ parameters are only evaluated when used. They are in contrast to _by-value_ parameters. To make a parameter called by-name, prepend `=>` to its type:

```scala
def calculate(input: => Int) = input * 37
```

By-name parameters have the advantage that they are not evaluated if they aren’t used in the function body. On the other hand, by-value parameters have the advantage that they are evaluated only once.

This ability to delay evaluation of a parameter until it is used can help performance if the parameter is computationally intensive to evaluate.


## [Default Parameter Values](https://docs.scala-lang.org/tour/default-parameter-values.html)

```scala
class Point(val x: Double = 0, val y: Double = 0)

val point0 = new Point(1);    // point (1, 0)
val point1 = new Point(y = 3) // point (0, 3)
```

For `point1`, we use `y=3` (a named argument) since "if the caller omits an argument, any following arguments must be named."


## [Packages and Imports](https://docs.scala-lang.org/tour/packages-and-imports.html)

#### Imports

```scala
import users._  // import everything from the users package
import users.User  // import the class User
import users.{User, UserPreferences}  // Only imports selected members
import users.{UserPreferences => UPrefs}  // import and rename for convenience
```

## More notes

#### Constructors

Class constructor: private, public, read-only, mutable variables:

```scala
class Ok[T](statusCode: Int, result: T) // private fields, but present on the constructor

class Ok[T](val statusCode: Int, val result: T) // public, read-only fields

class Ok[T](var statusCode: Int, var result: T) // public, mutable fields
```

On a case class, "when you use the case keyword, you do not need to use `val` to make a field public and read-only":

```scala
case class Ok[T](statusCode: Int, result: T)
```

#### [Null, null, Nil, Nothing, None, and Unit in Scala](https://sanaulla.info/2009/07/12/nothingness-2)

- `Null` – it's a Trait.
- `null` – it's an instance of `Null` - similar to Java null.
- `Nil` – represents an empty List of anything of zero length.
- `Nothing` - it's a Trait. Its a subtype of everything, but not superclass of anything. There are no instances of `Nothing`.
- `None` – used with `Option` which has exactly 2 subclasses: `Some` and `None`. `None` is used to represent a sensible return value to avoid null pointer exceptions.
- `Unit` – type used in method that doesn’t return a value.

#### The `return` keyword

[A `return` expression, when evaluated, abandons the current computation and returns to the caller of the _method_ in which return appears](https://tpolecat.github.io/2014/05/09/return.html):

Correct:

```scala
def sum(ns: Int*): Int = ns.foldLeft(0)((n, m) => n + m)
sum(33, 42, 99)

// Output
res2: Int = 174
```

Incorrect: was expecting 174, but got 33.

```scala
def sumR(ns: Int*): Int = ns.foldLeft(0)((n, m) => return n + m)
sumR(33, 42, 99)

// Output
res3: Int = 33
```


## References

- Notes are summarized from [Tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html). All sections in their tutorial (other than "Implicit Conversions") were well written.
- Article: [Nothingness](https://sanaulla.info/2009/07/12/nothingness-2) - summarized above.
- Article: [The Point of No Return](https://tpolecat.github.io/2014/05/09/return.html) - went through first 2 examples, which are summarized above.
