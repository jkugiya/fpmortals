{frontmatter}
<!--
> "Love is wise; hatred is foolish. In this world, which is getting more
> and more closely interconnected, we have to learn to tolerate each
> other, we have to learn to put up with the fact that some people say
> things that we don't like. We can only live together in that way. But
> if we are to live together, and not die together, we must learn a kind
> of charity and a kind of tolerance, which is absolutely vital to the
> continuation of human life on this planet."
> 
> ― Bertrand Russell
-->

> 「愛することは賢明であり、憎しみは愚かである」という言葉があります。これからの世界はますます密接に関わり合うことになるので、
> お互いに寛容になることや、自分にとって好ましくない事実を受け入れるをこと学ばなければなりません。私達が共生するにはこの方法しかないのです。
> 私達が共生し、お互いに滅亡しないようにするには、地球上の人類の継続にとって必要不可欠な慈愛や寛容といったものを学ぶ必要があります。
> ― バーナード・ラッセル
<!--
# About This Book

This book is for the typical Scala developer, probably with a Java background,
who is both sceptical and curious about the **Functional Programming** (FP)
paradigm. This book justifies every concept with practical examples, including
writing a web application.
-->
# 本書について
本書は、Javaの開発経験があり、**関数型プログラミング**（FP）に懐疑心と好奇心の両方を持っているような、
典型的なScala開発者に向けて書きました。本書では、Webアプリケーションの作成といった典型的な例を用いて、関数型プログラミングのあらゆる概念が有用であることを示します。

<!--
This book uses [Scalaz 7.2](https://github.com/scalaz/scalaz), the most popular, stable, principled and
comprehensive Functional Programming framework for Scala.
-->
本書では、Scalaにおいて最も著名かつ安定していて、包括的な関数型プログラミングの原則に基づいたフレームワークである[Scalaz 7.2](https://github.com/scalaz/scalaz)を使用します。

<!--
This book is designed to be read from cover to cover, in the order presented,
with a rest between chapters. Earlier chapters encourage coding styles that we
will later discredit: similar to how we learn Newton's theory of gravity as
children, and progress to Riemann / Einstein / Maxwell if we become students of
physics.
-->

本書の構成は先頭から最後まで順番に読んでいくようになっています。例えば、初めの方の章では後の章で疑問を投げかけるような
コーディングスタイルを奨めています。子供のときにはニュートンの重力理論を学び、物理学生になってからリーマンやアインシュタイン、
マクスウェルの理論を学ぶのと同じです。

<!--
A computer is not necessary to follow along, but studying the Scalaz source code
is encouraged. Some of the more complex code snippets are available with [the
book's source code](https://github.com/fommil/fpmortals/) and those who want practical exercises are encouraged to
(re-)implement Scalaz (and the example application) using the partial
descriptions presented in this book.
-->

本を読んでいくのにコンピュータは必要ではありませんが、Scalazのソースコードを勉強することをお勧めします。
いくつか複雑なコードスニペットが[本書のコード例](https://github.com/fommil/fpmortals/)として出てきますが、
もっと実践的な演習を望む方はScalaz(および、サンプルのアプリケーション)を本書に記載されている部分的な説明を
用いて実装することをお勧めします。

<!--
We also recommend [The Red Book](https://www.manning.com/books/functional-programming-in-scala) as further reading. It teaches how to write an FP
library in Scala from first principles.
-->

[Scala関数型デザイン&プログラミング](https://www.amazon.co.jp/dp/4844337769/)を読むこともお勧めします。
本書では、Scalaの第一原理による関数型プログラミングの作り方を学ぶことができます。

<!--
# Copyleft Notice
-->

# コピーレフトについて

<!--
This book is **Libre** and follows the philosophy of [Free Software](https://www.gnu.org/philosophy/free-sw.en.html): you can use
this book as you like, the [source is available](https://github.com/fommil/fpmortals/) you can redistribute this book
and you can distribute your own version. That means you can print it, photocopy
it, e-mail it, upload it to websites, change it, translate it, charge for it,
remix it, delete bits, and draw all over it.
-->

本書は**自由**であり[フリーソフトウェア](https://www.gnu.org/philosophy/free-sw.en.html)の哲学に則ります。
誰でも好きなように本書を使うことができますし、[本書のソースコード](https://github.com/fommil/fpmortals/)を使って再配布や
自らのバージョンの出版を行っても構いません。つまり、印刷やコピー、Eメールの送信やWEBサイトへのアップロード、翻訳、それらに対する変更、
改変、図を削除したり付け加えたりすることもできます。

<!--
This book is **Copyleft**: if you change the book and distribute your own version,
you must also pass these freedoms to its recipients.
-->

本書は**コピーレフト**です。本書に対する変更を行って自らのバージョンの配布を行う場合は、配布するバージョンにもこれらの自由を与えなければいけません。

<!--
This book uses the [Creative Commons Attribution ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/legalcode) (CC
BY-SA 4.0) license.
-->

本書は[CC-BY-SA 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/legalcode)ライセンスを用います。

<!--
All original code snippets in this book are separately [CC0](https://wiki.creativecommons.org/wiki/CC0) licensed, you may use
them without restriction. Excerpts from Scalaz and related libraries maintain
their license, reproduced in full in the appendix.
-->

本書に収められているソースコードについては、制限なくそれらを利用できるように別途[CC0](https://wiki.creativecommons.org/wiki/CC0)ライセンスを用います。 Scalazや関連ライブラリからの引用については付録に記載しています。

<!--
The example application `drone-dynamic-agents` is distributed under the terms of
the [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html): only the snippets in this book are available without restriction.
-->

`dron-dynamic-agents`というサンプルアプリケーションは[GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html)での配布なので、
制限なしに利用できるのは本書のスニペットのみです。

<!--
# Thanks
-->

# 謝辞

<!--
Diego Esteban Alonso Blas, Raúl Raja Martínez and Peter Neyens of 47
degrees, Rúnar Bjarnason, Tony Morris, John de Goes and Edward Kmett
for their help explaining the principles of FP. Kenji Yoshida and
Jason Zaugg for being the main authors of Scalaz, and Paul Chuisano /
Miles Sabin for fixing a critical bug in the Scala compiler ([SI-2712](https://issues.scala-lang.org/browse/SI-2712)).
-->
47 degreesのディエゴ・エステバン・アロンソ・ブラス、ラウール・ラジャ・マルティネス、ピーター・ネイエンズ、および、ルナール・ビャナソン、トニー・モリス、ジョン・ド・ゴーズ、エドワード・クメット各氏らは関数型プログラミングの原則の説明において手助けをしてくれました。
吉田憲治氏とジェイソン・ザーグ氏はScalazの主要な著者です。
ポール・チュイザーノとマイルス・セイビンはScalaコンパイルの致命的なバグを修正してくれました。（[SI-2712](https://issues.scala-lang.org/browse/SI-2712)）

<!--
Thanks to the readers who gave feedback on early drafts of this text.
-->

草稿段階においてフィードバックをしてくれた読者に感謝します。

<!--
Some material was particularly helpful for my own understanding of the concept?
that are in this book. Thanks to Juan Manuel Serrano for [All Roads Lead to
Lambda](https://skillsmatter.com/skillscasts/9904-london-scala-march-meetup#video), Pere Villega for [On Free Monads](http://perevillega.com/understanding-free-monads), Dick Wall and Josh Suereth for [For:
What is it Good For?](https://www.youtube.com/watch?v=WDaw2yXAa50), Erik Bakker for [Options in Futures, how to unsuck them](https://www.youtube.com/watch?v=hGMndafDcc8),
Noel Markham for [ADTs for the Win!](https://www.47deg.com/presentations/2017/06/01/ADT-for-the-win/), Sukant Hajra for [Classy Monad Transformers](https://www.youtube.com/watch?v=QtZJATIPB0k),
Luka Jacobowitz for [Optimizing Tagless Final](https://lukajcb.github.io/blog/functional/2018/01/03/optimizing-tagless-final.html), Vincent Marquez for [Index your
State](https://www.youtube.com/watch?v=JPVagd9W4Lo), Gabriel Gonzalez for [The Continuation Monad](http://www.haskellforall.com/2012/12/the-continuation-monad.html), and Yi Lin Wei / Zainab Ali
for their tutorials at Hack The Tower meetups.
-->

いくつかの資料は私が本書を書くための概念を理解するのに特に役立ちました。
フアン・マヌエル・セラーノ氏の[All Roads Lead to Lambda](https://skillsmatter.com/skillscasts/9904-london-scala-march-meetup#video)、ペレ・ヴィッレガ氏の[On Free Monads](http://perevillega.com/understanding-free-monads)、ディック・ウォール氏とジョシュ・スエース氏の[For: What is it Good For?](https://www.youtube.com/watch?v=WDaw2yXAa50)、エリック・バッカー氏の[Options in Futures, how to unsuck them](https://www.youtube.com/watch?v=hGMndafDcc8)、ノエル・マーカム氏の[ADTs for the Win!](https://www.47deg.com/presentations/2017/06/01/ADT-for-the-win/)、スーカント・ハーラ氏の[Classy Monad Transformers](https://www.youtube.com/watch?v=QtZJATIPB0k)、ルカ・ヤコボウィッツ氏の[Optimizing Tagless Final](https://lukajcb.github.io/blog/functional/2018/01/03/optimizing-tagless-final.html)、ヴィンセント・マルケス氏の[Index your State](https://www.youtube.com/watch?v=JPVagd9W4Lo)、ガブリエル・ゴンザレス氏の[The Continuation Monad](http://www.haskellforall.com/2012/12/the-continuation-monad.html)、およびイ・リン・ウェイ氏とザイナブ・アリ氏によるHack The Towerミートアップにおけるチュートリアルなどです。

<!--
The helpul souls who patiently explained things to me: Merlin Göttlinger, Edmund
Noble, Fabio Labella, Adelbert Chang, Michael Pilquist, Paul Snively, Daniel
Spiewak, Stephen Compall, Brian McKenna, Ryan Delucchi, Pedro Rodriguez, Emily
Pillmore, Aaron Vargo, Tomas Mikula, Jean-Baptiste Giraudeau, Itamar Ravid, Ross
A. Baker, Alexander Konovalov, Harrison Houghton, Alexandre Archambault,
Christopher Davenport, Jose Cardona, Isaac Elliott.
-->
次に挙げるのは、私に辛抱強く物事を教えてくれた人たちです。マーリン・ゲットリンガー、エドモンド・ノーブル、ファビオ・ラベラ、アデルベルト・チャン、マイケル・ピルキスト、ポール・スナイプス、ダニエル・スピワック、スティーブン・コンパル、ブライアン・マッケンナ、ライアン・デュルキッチ、ペドロ・ロドリゲス、エミリー・ピルモア、アーロン・ヴァルゴ、トーマス・ミクラ、ジャン＝バプティスト・ジウロドー、イタマール・ラヴィド、ロス・A・ベイカー、アレクサンダー・コナヴァロフ、ハリソン・ホートン、アレクサンドル・アーチェックンボルト、クリストファー・ダベンポート、ホセ・カルデナ、アイザック・エリオット。

<!--
# Practicalities
-->

# 本書のプログラムについて

<!-- To set up a project that uses the libraries presented in this book, use a recent -->
<!-- version of Scala with FP-specific features enabled (e.g. in `build.sbt`): -->

本書で紹介するライブラリをプロジェクトで利用するには、最新のScalaのバージョンを使用して、関数型プログラミングの機能を有効にしてください。

{lang="text"}
~~~~~~~~
  scalaVersion in ThisBuild := "2.12.6"
  scalacOptions in ThisBuild ++= Seq(
    "-language:_",
    "-Ypartial-unification",
    "-Xfatal-warnings"
  )
  
  libraryDependencies ++= Seq(
    "com.github.mpilquist" %% "simulacrum"     % "0.13.0",
    "org.scalaz"           %% "scalaz-core"    % "7.2.26"
  )
  
  addCompilerPlugin("org.spire-math" %% "kind-projector" % "0.9.7")
  addCompilerPlugin("org.scalamacros" % "paradise" % "2.1.1" cross CrossVersion.full)
~~~~~~~~

<!-- In order to keep our snippets short, we will omit the `import` -->
<!-- section. Unless told otherwise, assume that all snippets have the -->
<!-- following imports: -->

本文中のスニペットの記述を短くするため、`import`セクションは省略します。 特に断りのない限り、すべてのスニペットに次のインポートがあることを前提とします。

{lang="text"}
~~~~~~~~
  import scalaz._, Scalaz._
  import simulacrum._
~~~~~~~~


