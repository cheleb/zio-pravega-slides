# ZIO 101

ZIO is a library for asynchronous and concurrent programming in Scala. 

<div v-click>
Meh, another one?
</div>

<v-clicks depth="2">

 * ZIO is a data type
 * ZIO is a functional effect system
 * ZIO is a library
 * ZIO is a framework
</v-clicks>

<div v-click>
Meh ...
</div>

---

# ZIO 101: ZIO is a data type

ZIO is a data type that represents a computation:
````md magic-move

```scala
trait ZIO[A] // "A": Success type, that may be produced by the effect
```
```java
Future<A> // "A": Success type, that may be produced by the Future
```
```scala
trait ZIO[A]
```
```scala
trait ZIO[-R, +E, +A]
```
```scala {*|1,4|1,3|1,2}
trait ZIO[
  -R, // Environment type, required by the effect
  +E, // Error type, that may fail the effect
  +A  // Success type, that may be produced by the effect
]
```
```scala
trait ZIO[-R, +E, +A]
```
````
<div v-click>
ZIO is a functional effect system:
</div>
<v-clicks depth="2">

 * describe the effect
 * compose effects
 * run effects with an interpreter.

</v-clicks>

<!--

Should remind of Future, but with more power
  * more control
  * more flexibility
  * more safety


-->

---

# ZIO 101

A simple mental model is to think of ZIO as a function:
````md magic-move
```scala
trait ZIO[-R, +E, +A]
```
```scala
trait ZIO[-R, +E, +A] {
  def run(r: R): Either[E, A]
}
```
```scala
trait ZIO[-R, +E, +A]
```
````
<div v-click>
Wait, what if:

* you don't care about the environment?
* you can't fail?
</div>

<div v-click>
Many aliases are provided for common use cases:

```scala {*|1|2|3|4|5}{at:4}
type UIO[+A]      = ZIO[Any, Nothing, A]   // Succeed with an `A`, cannot fail              , no requirements.
type URIO[-R, +A] = ZIO[R, Nothing, A]     // Succeed with an `A`, cannot fail              , requires an `R`.
type Task[+A]     = ZIO[Any, Throwable, A] // Succeed with an `A`, may fail with `Throwable`, no requirements.
type IO[+E, +A]   = ZIO[Any, E, A]         // Succeed with an `A`, may fail with `E`        , no requirements.
type RIO[-R, +A]  = ZIO[R, Throwable, A]   // Succeed with an `A`, may fail with `Throwable`, requires an `R`.
```
</div>

<!--

Sound abstract, but in practice, it's quite simple to use. Wait for the examples.

-->

---

# ZIO 101

````md magic-move {lines: true, at:1}
```scala
val simple: ZIO[Any, Nothing, Int] = ZIO.succeed(42)
```
```scala
val simple: UIO[Int] = ZIO.succeed(42) // Succeed with an `A`, cannot fail, no requirements.
```
```scala {3}
val simple:                  UIO[Int]    = ZIO.succeed(42)

def whatIsTheAnswer(i: Int): UIO[String] = ZIO.succeed(s"The answer is $i")
```
```scala {5}
val simple:                     UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]

val program: UIO[Int] = simple // UIO[Int]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]

val program: UIO[String] = simple.flatMap(i => whatIsTheAnswer(i)) // UIO[String]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]

val program: Task[Unit] = simple.flatMap(i => whatIsTheAnswer(i))  // UIO[String]
                                .flatMap(str => sayIntLoud(str))   // Task[Unit]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

def whatIsTheAnswer(i: Int):    UIO[String]  = ZIO.succeed(s"The answer is $i")       // Int => UIO[String]

def sayItLoud(message: String): Task[Unit]   = Console.printLine(message)             // String => Task[Unit]

val program: Task[Unit] = simple.flatMap(whatIsTheAnswer)  // UIO[String]
                                .flatMap(sayIntLoud)   // Task[Unit]
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

val program: Task[Unit] = simple.flatMap(whatIsTheAnswer)
                                .flatMap(sayIntLoud)
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

val program: Task[Unit] = simple flatMap whatIsTheAnswer flatMap sayIntLoud
```
```scala
val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

val program: Task[Unit] = simple flatMap whatIsTheAnswer
                              💥 flatMap sayIntLoud // Not compiling ...
                                                    // But it can be fixed with a simple extension method 
                                                   // Interested ?
```
```scala

val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

extension [R, E, A](zio: ZIO[R, E, A])
    def |>[R1, E1 <: E, B](f: A => ZIO[R1, E1, B]): ZIO[R1, E1, B] = zio.flatMap(f)

val program: Task[Unit] = simple flatMap whatIsTheAnswer
                              💥 flatMap sayIntLoud 
```
```scala

val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

extension [R, E, A](zio: ZIO[R, E, A])
    def |>[R1, E1 <: E, B](f: A => ZIO[R1, E1, B]): ZIO[R1, E1, B] = zio.flatMap(f)

val program: Task[Unit] = simple |> whatIsTheAnswer
                                 |> sayIntLoud
```
```scala {*|1}
import zorglub.flatmapthatshit.*

val simple:                     UIO[Int]     = ZIO.succeed(42)                        // UIO[Int]

val whatIsTheAnswer: Int    =>  UIO[String]  = i => ZIO.succeed(s"The answer is $i")  // Int => UIO[String]

val sayItLoud:       String =>  Task[Unit]   = message => Console.printLine(message)  // String => Task[Unit]

val program: Task[Unit] = simple |> whatIsTheAnswer
                                |> flatMap sayIntLoud
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message)

```

```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(s"$message and isEven: ${i % 2 == 0}")
                                                       

```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit] = simple.flatMap(i => whatIsTheAnswer(i))
                                .flatMap(str => sayIntLoud(str, 💥i)) // Won't compile
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit]  = simple.flatMap { i => 
                                              whatIsTheAnswer(i)
                                                 .flatMap {
                                                    str => sayIntLoud(str, i)
                                                  }
                                  }

```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit] = for { // For comprehension
    i   <- simple
    str <- whatIsTheAnswer(i)
    _   <- sayIntLoud(str)
  } yield ()
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message).repeatN(i%2)

val program: Task[Unit] = for
    i   <- simple
    str <- whatIsTheAnswer(i)
    _   <- sayIntLoud(str)
  yield ()
```
````


<!--

Simple illustration of the power of ZIO
  * Describe effects
  * Compose effects

-->

---

# ZIO 101 / ZLayer

````md magic-move {lines: true, at:1}

```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def sayItLoud(message: String, i: Int): Task[Unit]   = Console.printLine(message)
```

```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): Task[Int]    = Database.insert(message)
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): ???    = 
  ZIO.service[Database]  // UIO[Database, Database]
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): ???    = 
  ZIO.service[Database]  // UIO[Database, Database]
    .flatMap(db => db.insert(message))
```
```scala
val simple:                             UIO[Int]     = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]  = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): ZIO[Database, Throwable, Int]   = 
  ZIO.service[Database] // UIO[Database, Database]
    .flatMap(db => db.insert(message))
```
```scala
val simple:                             UIO[Int]            = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]         = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]  = 
  ZIO.service[Database]
    .flatMap(db => db.insert(message))
```
```scala
val simple:                             UIO[Int]            = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]         = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]  = Database.insert(message)
```
```scala
val simple:                             UIO[Int]            = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]         = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]  = Database.insert(message)

val program = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    _      <- writeToDB(str)
  } yield ()

```
```scala
val simple:                             UIO[Int]            = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]         = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]  = Database.insert(message)

val program: RIO[Database, Unit] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    _      <- writeToDB(str)
  } yield ()

```
```scala
val simple:                             UIO[Int]            = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]         = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]  = Database.insert(message)

val program: RIO[Database, Int] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    nRow   <- writeToDB(str)
  } yield nRow

```
```scala
val simple:                             UIO[Int]               = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]            = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]     = Database.insert(message)

def writeToRabbit(message: String, i: Int): RIO[Rabbit, Unit]  = Rabbit.offer(message)

val program: RIO[Database, Int] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    nRow   <- writeToDB(str)
  } yield nRow

```
```scala
val simple:                             UIO[Int]               = ZIO.succeed(42)

def whatIsTheAnswer(i: Int):            UIO[String]            = ZIO.succeed(s"The answer is $i")

def writeToDB(message: String, i: Int): RIO[Database, Int]     = Database.insert(message)

def writeToRabbit(message: String, i: Int): RIO[Rabbit, Unit]  = Rabbit.offer(message)

val program: RIO[Database & Rabbit, Int] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    _      <- writeToRabbit(str)
    nRow   <- writeToDB(str)
  } yield nRow

```
```scala
object MyApp extends ZIOAppDefault {

  val program: RIO[Database & Rabbit, Int] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    _      <- writeToRabbit(str)
    nRow   <- writeToDB(str)
  } yield nRow

  override def run = program 💥 // Dependencies not satisfied

}
```
```scala
object MyApp extends ZIOAppDefault {

  val program: RIO[Database & Rabbit, Int] = for { // For comprehension
    i      <- simple
    str    <- whatIsTheAnswer(i)
    _      <- writeToRabbit(str)
    nRow   <- writeToDB(str)
  } yield nRow

  override def run = program.provide(
    Database.live,
    Rabbit.live
  )

}
```
````

<!--

What is the issue with Database.insert(message) ?

The big difference with Console, is that Database is a service
  - that needs to be provided
  - can fail
  - must be configured
  - must be closed ...

-->

