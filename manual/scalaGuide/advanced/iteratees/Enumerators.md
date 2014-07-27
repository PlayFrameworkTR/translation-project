<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Veri akımlarını reaktif olarak işlemek

## Enumerator'lar

Eğer iteratee girdinin tüketicisini ya da alıcısını ifade ediyorsa, `Enumerator` girdiyi verilen iteratee'ye gönderen kaynaktır. Adından da anlaşılacağı üzere iteratee'ye girdiyi sırayla verir ve nihayetinde iteratee'nin yeni durumunu döndürür. `Enumerator`'ın imzasından da bu görülebilir:

```scala
trait Enumerator[E] {

  /**
   * Bu Enumerator'ı bir Iteratee'ye uygula
   */
  def apply[A](i: Iteratee[E, A]): Future[Iteratee[E, A]]

}
```

`Enumerator[E]`, `Input[E]` türünden bir girdi tüketen ve nihayetinde iteratee'nin yeni durumunu verecek olan bir `Future[Iteratee[E,A]]` döndüren bir `Iteratee[E,A]` alır.

Iteratee'nin fold metodunu art arda çağırarak `Enumerator` örnekleri gerçekleyebiliriz ya da dahili `Enumerator` oluşturma metotlarını kullanabiliriz. Örneğin aşağıdaki gibi bir iteratee'ye bir string listesini gönderen `Enumerator[String]` yaratabiliriz:

```scala
val enumerateUsers: Enumerator[String] = {
  Enumerator("Guillaume", "Sadek", "Peter", "Erwan")
}
```

Artık onu daha önce yarattığımız consume iteratee'ye uygulayabiliriz:

```scala
val consume = Iteratee.consume[String]()
val newIteratee: Future[Iteratee[String,String]] = enumerateUsers(consume)
```

Iteratee'yi sonlandırmak ve hesaplanan değeri ayıklamak için `Input.EOF` göndermemiz gerekir. `Iteratee` yalnızca bu işi yapan bir `run` metodu barındırır. Bir `Input.EOF` gönderir ve kalan girdiyi göz ardı ederek bir `Future[A]` döndürür.

```scala
// newIteratee bir promise olduğu
// ve run da bir promise döndürdüğü için flatMap kullanıyoruz
val eventuallyResult: Future[String] = newIteratee.flatMap(i => i.run)

// Nihayetinde sonucu yazdır
eventuallyResult.onSuccess { case x => println(x) }

// "GuillaumeSadekPeterErwan" yazar
```

Burada bir `Iteratee`'nin gelecekte bir sonuç üreteceği dikkatinizi çekmiş olmalı (fold çağrıldığında ve gerekli callback verildiğinde bir promise döndürerek). `Future` da gelecekte bir değer üretir. O halde bir `Future[Iteratee[E,A]]` bir `Iteratee[E,A]` olarak görülebilir. `Iteratee.flatten`'ın tam olarak yaptığı iş budur. Bir önceki örneğe bunu uygulayalım:

```scala
// Enumarator ve flatten uygula ve oluşan iteratee'yi çalıştır
val newIteratee = Iteratee.flatten(enumerateUsers(consume))

val eventuallyResult: Future[String] = newIteratee.run

// Nihayetinde sonucu yazdır
eventuallyResult.onSuccess { case x => println(x) }

// "GuillaumeSadekPeterErwan" yazar
```

`Enumerator` operatör olarak kullanılabilecek bazı sembolik metotlara sahiptir. Bu metotlar bazı durumlarda birkaç parantezden kurtulmayı sağlayabilir. Örneğin `|>>` metodu aynen apply gibi çalışır:

```scala
val eventuallyResult: Future[String] = {
  Iteratee.flatten(enumerateUsers |>> consume).run
}
```

Bir  `Enumerator` bir iteratee'ye girdi verip iteratee'nin yeni durumunu döndürdüğünden, dönen iteratee'ye başka bir `Enumerator` aracılığıyla daha fazla girdi göndermeye devam edebiliriz. Bunu `Future`'lar üzerinde `flatMap` fonksiyonunu çağırarak ya da daha basit şekilde aşağıdaki gibi `andThen` metodunu kullanarak `Enumerator` örneklerini bağlayarak yapabiliriz:

```scala
val colors = Enumerator("Red","Blue","Green")

val moreColors = Enumerator("Grey","Orange","Yellow")

val combinedEnumerator = colors.andThen(moreColors)

val eventuallyIteratee = combinedEnumerator(consume)
```

Apply için olduğu gibi `andThen` için de `>>>` şeklinde bir sembolik sürüm mevcuttur:

```scala
val eventuallyIteratee = {
  Enumerator("Red","Blue","Green") >>>
  Enumerator("Grey","Orange","Yellow") |>>
  consume
}
```

Bir dosyanın içeriğini sırayla vermek için `Enumerator` yaratabiliriz:

```scala
val fileEnumerator: Enumerator[Array[Byte]] = {
  Enumerator.fromFile(new File("path/to/some/file"))
}
```

Ya da daha genel olarak `Enumerator.fromStream` kullanarak bir `java.io.InputStream` sıralayabiliriz. Bu `Enumerator`'ın uygulandığı iteratee yeni bir girdi almaya hazır olana dek yeni girdinin okunmayacağını önemle vurgulamak gerekir.

Aslında iki metot da daha genel `Enumerator.generateM` üzerine kuruludur:

```scala
def generateM[E](e: => Future[Option[E]]) = {
  ...
}
```

`Enumerator` nesnesinde tanımlanmış olan bu metot imperatif mantık kullanarak bir `Enumerator` oluşturmanın en önemli metotlarından biridir. İmzasına daha yakından bakıldığında bu metodun, bu `Enumerator` girdi almaya hazır bir iteratee'ye her uygulandığında çağrılacak `e: => Future[Option[E]]` callback fonksiyonu aldığı görülür.

Bir promise döndürebilme yeteneğimizi kullanarak aşağıdaki gibi kolayca her 100 milisaniyede bir zaman değeri akımını ifade eden bir `Enumerator` yaratabiliriz.

```scala
Enumerator.generateM {
  Promise.timeout(Some(new Date), 100 milliseconds)
}
```

Benzer şekilde bir `Future` döndüren `WS` api kullanarak belli aralıklarla bir URL'den okuyacak bir `Enumerator` oluşturabiliriz.

Bu Enumerator'ı imperatif bir `Iteratee.foreach` ile bağlayarak bir zaman değeri akımını periyodik olarak yazdırabiliriz:

```scala
val timeStream = Enumerator.generateM {
  Promise.timeout(Some(new Date), 100 milliseconds)
}

val printlnSink = Iteratee.foreach[Date](date => println(date))

timeStream |>> printlnSink
```

Enumerator oluşturmanın daha imperatif bir yolu `Concurrent.unicast` kullanmaktır. Hazır olduğunda size üzerinde `push` ve `end` metotları tanımlanmış olan bir `Channel` döndürür.

```scala
val enumerator = Concurrent.unicast[String](onStart = channel => {
  channel.push("Hello")
  channel.push("World")
})

enumerator |>> Iteratee.foreach(println)
```

`Enumerator` bir `Iteratee`'ye her uygulandığında `onStart` fonksiyonu çağrılır. Bazı uygulamalarda, örneğin chat odası, `enumerator`'ı bir listener listesi tutacak senkronize global bir değere atamak mantıklı olabilir (örneğin STM kullanarak). `Concurrent.unicast`, `onComplete` ve `onError` olmak üzere iki fonksiyon daha kabul eder.

Bir diğer ilgi çekici metot ise `interleave` ya da `>-` metodudur. `Enumerator`'ın girdisi herhangi bir `Enumerator`'dan dönüşümlü olarak geliyormuş gibi görünür.

## Enumerator à la carte

`Enumerator` oluşturmak için artık çok sayıda yolumuz olduğuna göre bunları `andThen` / `>>>` ve `interleave` / `>-` birleştirme metotları ile birlikte kullanarak ihtiyaca göre `Enumerator`'lar oluşturabiliriz.

Akımsal bir uygulamayı organize etmenin bir yolu basit `Enumerator`'lar tanımlayıp bunların bir kısmını daha sonra birleştirmektir. Bir izleme sistemi geliştirdiğimizi düşünelim:

```scala
object AvailableStreams {

  val cpu: Enumerator[JsValue] = Enumerator.generateM(/* code here */)

  val memory: Enumerator[JsValue] = Enumerator.generateM(/* code here */)

  val threads: Enumerator[JsValue] = Enumerator.generateM(/* code here */)

  val heap: Enumerator[JsValue] = Enumerator.generateM(/* code here */)

}

val physicalMachine = AvailableStreams.cpu >- AvailableStreams.memory
val jvm = AvailableStreams.threads >- AvailableStreams.heap

def usersWidgetsComposition(prefs: Preferences) = {
  // birleştirmeyi dinamik olarak yap
}
```

Şimdi sıra `Enumerator` ve `Iteratee`'leri adapte etmek ve dönüştürmek için `Enumeratee` kullanmaya geldi!

> **Sonraki:** [[Enumeratee'ler | Enumeratees]]
