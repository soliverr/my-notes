---
id: 8beheryvmn4fuuwovmzyb42
title: Scala Lens
desc: Implementation of lenses in Scala
updated: 1631865710556
created: 1631865040162
---

A **Lens** is an abstraction from functional programming which helps to deal with a problem of updating complex immutable nested objects like this:

```scala
case class User(id: UserId, generalInfo: GeneralInfo, billInfo: BillInfo)
case class UserId(value: Long)
case class GeneralInfo(email: Email,
                       password: String,
                       siteInfo: SiteInfo,
                       isEmailConfirmed: Boolean = false,
                       phone: String,
                       isPhoneConfirmed: Boolean = false)
case class SiteInfo(alias: String, avatarUrl: String, userRating: Double = 0.0d)
case class Email(value: String)
case class BillInfo(addresses: Seq[Address], name: Name)
case class Name(firstName: String, secondName: String)
case class Address(country: Country, city: City, street: String, house: String, isConfirmed: Boolean = false)
case class City(name: String)
case class Country(name: String)
```

* http://koff.io/posts/292173-lens-in-scala/

## Shapeless.Lenses

* <https://github.com/milessabin/shapeless>
    * <https://github.com/milessabin/shapeless/blob/main/examples/src/main/scala/shapeless/examples/lenses.scala>
* <https://www.scala-exercises.org/shapeless/lenses>
