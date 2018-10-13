<!--
# Introduction
-->

# はじめに

<!--
It is human instinct to be sceptical of a new paradigm. To put some
perspective on how far we have come, and the shifts we have already
accepted on the JVM, let's start with a quick recap of the last 20
years.
-->
人間は新しいパラダイムには懐疑的なものです。長い道のりを辿って既にJVMに取り入れられた変化を俯瞰するため、
この20年の間に起きたことについておさらいしておきましょう。

<!--
Java 1.2 introduced the Collections API, allowing us to write methods
that abstracted over mutable collections. It was useful for writing
general purpose algorithms and was the bedrock of our codebases.
-->

Java 1.2でコレクションAPIが導入され、可変コレクションによって抽象化されたメソッドを利用できるようになりました。
このAPIは一般的用途のアルゴリズムを記述するのに便利で、私たちのコードベースの基盤になりました。

<!--
But there was a problem, we had to perform runtime casting:
-->

しかし、実行時にキャストを行わなければならないという問題がありました。

{lang="text"}
~~~~~~~~
  public String first(Collection collection) {
    return (String)(collection.get(0));
  }
~~~~~~~~

<!--
In response, developers defined domain objects in their business logic
that were effectively `CollectionOfThings`, and the Collection API
became implementation detail.
-->

このため、開発者はビジネスロジックを`CollectionOfThings`といったドメインオブジェクトに定義し、
コレクションAPIは実装の詳細として扱うといった対応が行われました。

<!--
In 2005, Java 5 introduced *generics*, allowing us to define
`Collection<Thing>`, abstracting over the container **and** its
elements. Generics changed how we wrote Java.
-->

2005年にJava 5で*ジェネリクス*が導入され、`Collection<Thing>`といった記述ができるようになりました。
ジェネリクスによって入れ物に*加えて*要素を抽象化できるようになり、私たちが記述するJavaのコードに変化をもたらしました。

<!--
The author of the Java generics compiler, Martin Odersky, then created
Scala with a stronger type system, immutable data and multiple
inheritance. This brought about a fusion of object oriented (OOP) and
functional programming (FP).
-->

それから、Javaのジェネリクスコンパイラの著者であるマーチン・オダスキー氏は、強力な型システムと不変なデータ構造と多重継承を備えたScalaを作りました。
Scalaは、オブジェクト指向（OOP）と関数型プログラミング（FP）の融合をもたらしました。

<!--
For most developers, FP means using immutable data as much as
possible, but mutable state is still a necessary evil that must be
isolated and managed, e.g. with Akka actors or `synchronized` classes.
This style of FP results in simpler programs that are easier to
parallelise and distribute, an improvement over Java. But it is only
scratching the surface of the benefits of FP, as we will discover in
this book.
-->

ほとんどの開発者にとって、FPとはできる限り不変なデータを使用するということですが、その場合でも変更可能な状態は依然として分離して管理する必要があります。例えば、Akkaのアクターや`同期`クラスです。
これらを使用したFPは、Javaと比べて並列や分散を扱うプログラムを簡単にします。しかし、本書を読むことで、これらがFPの利点と思われるところを傷つけているということがわかるでしょう。

<!--
Scala also brings `Future`, making it easy to write asynchronous
applications. But when a `Future` makes it into a return type,
*everything* needs to be rewritten to accomodate it, including the
tests, which are now subject to arbitrary timeouts.
-->

Scalaでは`Future`を使って非同期アプリケーションの記述を簡単にすることもできます。
しかし、`Future`を戻り値型にすると、テストを含めて*全て*をそれに合わせて書き直さなければいけません。
この場合、テストではタイムアウトを使う必要があります。

<!--
We have a problem similar to Java 1.0: there is no way of abstracting
over execution, much as we had no way of abstracting over collections.
-->

Scalaにおいて実行を抽象化する方法がないのと同じように、Java 1.0でもコレクションを抽象化する方法がありませんでした。

<!--
## Abstracting over Execution
-->

## 実行の抽象化

<!--
Say we want to interact with the user over the command line interface. We can
`read` what the user types and we can `write` a message to them.
-->

コマンドラインでの対話を例に考えてみましょう。このとき、コマンドラインはユーザーの入力を*読み取り*、
インターフェースに対してメッセージを*書き込む*ことができます。

{lang="text"}
~~~~~~~~
  trait TerminalSync {
    def read(): String
    def write(t: String): Unit
  }
  
  trait TerminalAsync {
    def read(): Future[String]
    def write(t: String): Future[Unit]
  }
~~~~~~~~

<!--
How do we write generic code that does something as simple as echo the user's
input synchronously or asynchronously depending on our runtime implementation?
-->

それでは、単純に入力をエコーするプログラムを同期的または非同期的に実行するとき、実行の方法を一般化するにはどのようにすればよいでしょうか？

<!--
We could write a synchronous version and wrap it with `Future` but now
we have to worry about which thread pool we should be using for the
work, or we could `Await.result` on the `Future` and introduce thread
blocking. In either case, it is a lot of boilerplate and we are
fundamentally dealing with different APIs that are not unified.
-->

同期実行するコードを書いてから`Future`でラップすることもできますが、その場合もスレッドプールで動作するのかを気にかける必要があります。
`Await.result`を使うこともできますが、これはスレッドをブロックすることになります。
どちらの場合も沢山のボイラプレートを書く必要があり、根本的に統一されていない様々なAPIを使うことになります。

<!--
We can solve the problem, like Java 1.2, with a common parent using the *higher
kinded types* (HKT) Scala language feature.
-->

この問題は、かつてのJava 1.2と似ていて共通性もありますが、Scalaでは*高カインド型*という言語機能で解決できます。

<!--
A> **Higher Kinded Types** allow us to use a *type constructor* in our type
A> parameters, which looks like `C[_]`. This is a way of saying that
A> whatever `C` is, it must take a type parameter. For example:
-->
A> **高カインド型**は、`C[_]`のような型パラメータと一緒に*型コンストラクタ*を使用できる機能です。
A> ある型コンストラクタが、`C`がどんなものであれ、型パラメータ`C[_]`を取るという宣言をすることができます。
A> 以下はコード例です。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   trait Foo[C[_]] {
A>     def create(i: Int): C[Int]
A>   }
A> ~~~~~~~~
A> 
<!--
`List` is a type constructor because it takes a type (e.g. `Int`) and constructs
a type (`List[Int]`). We can implement `Foo` using `List`:
-->
A> `List`は型（例えば`Int`）を1つ受け取ることができるので型コンストラクタの一種です。（例えば、`List[Int]`）
A> Listを使った`Foo`の実装は以下のようになります。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   object FooList extends Foo[List] {
A>     def create(i: Int): List[Int] = List(i)
A>   }
A> ~~~~~~~~
A> 
<!--
A> We can implement `Foo` for anything with a type parameter hole, e.g.
A> `Either[String, _]`. Unfortunately it is a bit clunky and we have to
A> create a type alias to trick the compiler into accepting it:
-->
A> `Either[String, _]`のように、型パラメータにホール(`_`の部分のこと)を1つだけ持つ型に対しても`Foo`を実装することもできます。
A> 残念ながらこの場合、少し不格好になりますが、型エイリアスを使ってコンパイルを通します。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   type EitherString[T] = Either[String, T]
A> ~~~~~~~~
A> 
<!--
A> Type aliases don't define new types, they just use substitution and
A> don't provide extra type safety. The compiler substitutes
A> `EitherString[T]` with `Either[String, T]` everywhere. This technique
A> can be used to trick the compiler into accepting types with one hole
A> when it would otherwise think there are two, like when we implement
A> `Foo` with `EitherString`:
-->
A> 型エイリアスは単に型を置き換えるだけで、新しい型を定義するわけではないので、何らかの型安全性をもたらすものではありません。
A> コンパイラは`EitherString[T]`と`Either[String, T]`を置き換えるだけです。
A> しかし、型エイリアスを使うことで、コンパイラにホールが1つだけであるかのように見せることができるので、
A> `Foo`に対して`EitherString`を与えるように、型パラメータが2つある型でも3つある型でも高カインド型の実装のコンパイルを通すことができます。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   object FooEitherString extends Foo[EitherString] {
A>     def create(i: Int): Either[String, Int] = Right(i)
A>   }
A> ~~~~~~~~
A> 
<!--
A> Alternatively, the [kind projector](https://github.com/non/kind-projector/) plugin allows us to avoid the `type`
A> alias and use `?` syntax to tell the compiler where the type hole is:
-->
A> 型エイリアスを使う代替の方法として、[kind projector](https://github.com/non/kind-projector/)プラグインを使用すると、
A> ホールの部分に`?`を使って高カインド型の実装のコンパイルを通すことができます。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   object FooEitherString extends Foo[Either[String, ?]] {
A>     def create(i: Int): Either[String, Int] = Right(i)
A>   }
A> ~~~~~~~~
A> 
<!--
A> Finally, there is this one weird trick we can use when we want to ignore the
A> type constructor. Define a type alias to be equal to its parameter:
-->
A> 最後に、型コンストラクタを無視したいときに使用する少し変わったトリックを紹介します。
A> 以下のように型コンストラクタのエイリアスを、型コンストラクタの型パラメータの型自身で定義します。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   type Id[T] = T
A> ~~~~~~~~
A> 
<!--
A> Before proceeding, understand that `Id[Int]` is the same thing as `Int`, by
A> substituting `Int` into `T`. Because `Id` is a valid type constructor we can use
A> `Id` in an implementation of `Foo`
-->
ここで、`Id[Int]`という型が`Int`と同じであることを理解しておいてください。`Id`は有効な型コンストラクタなので、`Foo`の実装で`Id`を使うことができます。
A> 
A> {lang="text"}
A> ~~~~~~~~
A>   object FooId extends Foo[Id] {
A>     def create(i: Int): Int = i
A>   }
A> ~~~~~~~~

<!--
We want to define `Terminal` for a type constructor `C[_]`. By
defining `Now` to construct to its type parameter (like `Id`), we can
implement a common interface for synchronous and asynchronous
terminals:
-->
`Terminal`という高カインド型に対して`C[_]`という型コンストラクタを与える場合を考えてみましょう。
`Id`と同じように、与えられた型パラメータの型を返す`Now`という型コンストラクタを定義することで、同期端末と非同期端末の共通インターフェースを実装できます。
{lang="text"}
~~~~~~~~
  trait Terminal[C[_]] {
    def read: C[String]
    def write(t: String): C[Unit]
  }
  
  type Now[X] = X
  
  object TerminalSync extends Terminal[Now] {
    def read: String = ???
    def write(t: String): Unit = ???
  }
  
  object TerminalAsync extends Terminal[Future] {
    def read: Future[String] = ???
    def write(t: String): Future[Unit] = ???
  }
~~~~~~~~

<!--
We can think of `C` as a *Context* because we say "in the context of
executing `Now`" or "in the `Future`".
-->

<!--
But we know nothing about `C` and we cannot do anything with a
`C[String]`. What we need is a kind of execution environment that lets
us call a method returning `C[T]` and then be able to do something
with the `T`, including calling another method on `Terminal`. We also
need a way of wrapping a value as a `C[_]`. This signature works well:
-->

{lang="text"}
~~~~~~~~
  trait Execution[C[_]] {
    def chain[A, B](c: C[A])(f: A => C[B]): C[B]
    def create[B](b: B): C[B]
  }
~~~~~~~~

<!-- letting us write: -->

{lang="text"}
~~~~~~~~
  def echo[C[_]](t: Terminal[C], e: Execution[C]): C[String] =
    e.chain(t.read) { in: String =>
      e.chain(t.write(in)) { _: Unit =>
        e.create(in)
      }
    }
~~~~~~~~

<!--
We can now share the `echo` implementation between synchronous and
asynchronous codepaths. We can write a mock implementation of
`Terminal[Now]` and use it in our tests without any timeouts.
-->

<!--
Implementations of `Execution[Now]` and `Execution[Future]` are
reusable by generic methods like `echo`.
-->

<!--
But the code for `echo` is horrible!
-->

<!--
The `implicit class` Scala language feature gives `C` some methods.
We will call these methods `flatMap` and `map` for reasons that will
become clearer in a moment. Each method takes an `implicit
Execution[C]`, but this is nothing more than the `flatMap` and `map`
that we're used to on `Seq`, `Option` and `Future`
-->

{lang="text"}
~~~~~~~~
  object Execution {
    implicit class Ops[A, C[_]](c: C[A]) {
      def flatMap[B](f: A => C[B])(implicit e: Execution[C]): C[B] =
            e.chain(c)(f)
      def map[B](f: A => B)(implicit e: Execution[C]): C[B] =
            e.chain(c)(f andThen e.create)
    }
  }
  
  def echo[C[_]](implicit t: Terminal[C], e: Execution[C]): C[String] =
    t.read.flatMap { in: String =>
      t.write(in).map { _: Unit =>
        in
      }
    }
~~~~~~~~

<!--
We can now reveal why we used `flatMap` as the method name: it lets us
use a *for comprehension*, which is just syntax sugar over nested
`flatMap` and `map`.
-->

{lang="text"}
~~~~~~~~
  def echo[C[_]](implicit t: Terminal[C], e: Execution[C]): C[String] =
    for {
      in <- t.read
       _ <- t.write(in)
    } yield in
~~~~~~~~

<!--
Our `Execution` has the same signature as a trait in Scalaz called `Monad`,
except `chain` is `bind` and `create` is `pure`. We say that `C` is *monadic*
when there is an implicit `Monad[C]` available. In addition, Scalaz has the `Id`
type alias.
-->

<!--
The takeaway is: if we write methods that operate on monadic types,
then we can write sequential code that abstracts over its execution
context. Here, we have shown an abstraction over synchronous and
asynchronous execution but it can also be for the purpose of more
rigorous error handling (where `C[_]` is `Either[Error, _]`), managing
access to volatile state, performing I/O, or auditing of the session.
-->

<!--
## Pure Functional Programming
-->

<!--
Functional Programming is the act of writing programs with *pure functions*.
Pure functions have three properties:
-->

<!--
-   **Total**: return a value for every possible input
-   **Deterministic**: return the same value for the same input
-   **Inculpable**: no (direct) interaction with the world or program state.
-->

<!--
Together, these properties give us an unprecedented ability to reason about our
code. For example, input validation is easier to isolate with totality, caching
is possible when functions are deterministic, and interacting with the world is
easier to control, and test, when functions are inculpable.
-->

<!--
The kinds of things that break these properties are *side effects*: directly
accessing or changing mutable state (e.g. maintaining a `var` in a class or
using a legacy API that is impure), communicating with external resources (e.g.
files or network lookup), or throwing and catching exceptions.
-->

<!--
We write pure functions by avoiding exceptions, and interacting with the world
only through a safe `F[_]` execution context.
-->

<!--
In the previous section, we abstracted over execution and defined `echo[Id]` and
`echo[Future]`. We might reasonably expect that calling any `echo` will not
perform any side effects, because it is pure. However, if we use `Future` or
`Id` as the execution context, our application will start listening to stdin:
-->

{lang="text"}
~~~~~~~~
  val futureEcho: Future[String] = echo[Future]
~~~~~~~~

<!--
We have broken purity and are no longer writing FP code: `futureEcho` is the
result of running `echo` once. `Future` conflates the definition of a program
with *interpreting* it (running it). As a result, applications built with
`Future` are difficult to reason about.
-->

<!--
A> An expression is *referentially transparent* if it can be replaced with its
A> corresponding value without changing the program's behaviour.
-->
A> 
<!--
A> Pure functions are referentially transparent, allowing for a great deal of code
A> reuse, performance optimisation, understanding, and control of a program.
-->
A> 
<!--
A> Impure functions are not referentially transparent. We cannot replace
A> `echo[Future]` with a value, such as `val futureEcho`, since the pesky user can
A> type something different the second time.
-->

<!--
We can define a simple safe `F[_]` execution context
-->

{lang="text"}
~~~~~~~~
  final class IO[A](val interpret: () => A) {
    def map[B](f: A => B): IO[B] = IO(f(interpret()))
    def flatMap[B](f: A => IO[B]): IO[B] = IO(f(interpret()).interpret())
  }
  object IO {
    def apply[A](a: =>A): IO[A] = new IO(() => a)
  }
~~~~~~~~

<!--
which lazily evaluates a thunk. `IO` is just a data structure that references
(potentially) impure code, it isn't actually running anything. We can implement
`Terminal[IO]`
-->

{lang="text"}
~~~~~~~~
  object TerminalIO extends Terminal[IO] {
    def read: IO[String]           = IO { io.StdIn.readLine }
    def write(t: String): IO[Unit] = IO { println(t) }
  }
~~~~~~~~
<!--
and call `echo[IO]` to get back a value
-->

{lang="text"}
~~~~~~~~
  val delayed: IO[String] = echo[IO]
~~~~~~~~

<!--
This `val delayed` can be reused, it is just the definition of the work to be
done. We can map the `String` and compose additional programs, much as we would
map over a `Future`. `IO` keeps us honest that we are depending on some
interaction with the world, but does not prevent us from accessing the output of
that interaction.
-->

<!--
The impure code inside the `IO` is only evaluated when we `.interpret()` the
value, which is an impure action
-->

{lang="text"}
~~~~~~~~
  delayed.interpret()
~~~~~~~~

<!--
An application composed of `IO` programs is only interpreted once, in the `main`
method, which is also called *the end of the world*.
-->

<!--
In this book, we expand on the concepts introduced in this chapter and show how
to write maintainable, pure functions, that achieve our business's objectives.
-->


