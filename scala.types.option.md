---
id: m8v1xyrz21w6mylxp8rdmt8
title: Option Type in Scala
desc: Using Option Type in Scala
updated: 1634797409290
created: 1631623627135
---

# The Option Type in Scala

* <https://www.baeldung.com/scala/option-type>
* <https://github.com/Baeldung/scala-tutorials/tree/master/scala-core-3>
* <https://blog.tmorris.net/posts/scalaoption-cheat-sheet/index.html>

## Create 

```scala
val o1: Option[Int] = None
val o2 = Some(10)

val o1: Option[Int] = Option(null)
assert(false == o1.isDefined)
```

## Test and get

* _isDefined_ – true if the object is Some
* _nonEmpty_ – true if the object is Some
* _isEmpty_ – true if the object is None

```scala
val o1: Option[Int] = ...
val v1 = if (o1.isDefined) {
  o1.get
} else {
  0
}

val o1: Option[Int] = ...
val v1 = o1 match {
  case Some(n) =>
    n
  case None =>
    0
}
```

This is useful when we don’t care about using the value. It is often used in “side-effecting” situations, like if we want to store the value in a database or print it to a file:

```scala
val o1: Option[Int] = Some(10)
o1.foreach(println)
```

## Default values

```scala
val v1 = o1.getOrElse(0)

val usdVsZarFxRate: Option[BigDecimal] = ... // populated via a web service call, for example
val marketRate = usdVsZarFxRate.getOrElse(throw new RuntimeException("No exchange rate defined for USD/ZAR"))
```

## Mapping

```scala
final def map[B](f: (A) => B): Option[B]
```

```scala
val o1: Option[Int] = Some(10)
assert(o1.map(_.toString).contains("10"))
assert(o1.map(_ * 2.0).contains(20))

val o2: Option[Int] = None
assert(o2.map(_.toString).isEmpty)
```

```scala
trait Player {
  def name: String
  def getFavoriteTeam: Option[String]
}

trait Tournament {
  def getTopScore(team: String): Option[Int] // Specified team's top score or None if they haven't played yet
}

val player: Player = ...                     // Let's not worry how we instantiate these just yet
val tournament: Tournament = ...
```

How do we get a player’s favorite team’s top score? We could call `player.getFavoriteTeam().map(tournament.getTopScore)` but this returns an `Option[Option[Int]]`. **Working with multiple layers of Option is awkward, to be sure**.

```scala
def getTopScore(player: Player, tournament: Tournament): Option[(Player, Int)] = {
  player.getFavoriteTeam.flatMap(tournament.getTopScore).map(score => (player, score))
}
```

## Flow control

```scala
val o1: Option[Int] = Some(10)
val o2: Option[Int] = None

def times2(n: Int): Int = n * 2

assert(o1.map(times2).contains(20))
assert(o2.map(times2).isEmpty)
```

## Flow control with Filtering


* filter – Returns the Option if the Option‘s value is Some and the supplied function returns true
* filterNot – Returns the Option if the Option‘s value is Some and the supplied function returns false
* exists – Returns true if the Option is set and the supplied function returns true
* forall – Same behavior as exists since Options have a maximum of one value

```scala
def whoHasTopScoringTeam(playerA: Player, playerB: Player, tournament: Tournament): Option[(Player, Int)] = {
    getTopScore(playerA, tournament).foldRight(getTopScore(playerB, tournament)) {
      case (playerAInfo, playerBInfo) => playerBInfo.filter {
        case (_, scoreB) => scoreB > playerAInfo._2
      }.orElse(Some(playerAInfo))
    }
  }
```
