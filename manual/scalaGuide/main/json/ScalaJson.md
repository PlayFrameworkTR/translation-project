<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# JSON temelleri

Modern web uygulamaları sıklıkla JSON (JavaScript Object Notation) formatı içinde veriyi ayrıştırmaya ve oluşturmaya ihtiyaç duyar. Play JSON [JSON kütüphanesi](api/scala/index.html#play.api.libs.json.package) ile bunu destekler.

JSON hafif bir veri takas formatıdır ve aşağıdaki gibi görünür:

```json
{
  "name" : "Watership Down",
  "location" : {
    "lat" : 51.235685,
    "long" : -1.309197
  },
  "residents" : [ {
    "name" : "Fiver",
    "age" : 4,
    "role" : null
  }, {
    "name" : "Bigwig",
    "age" : 6,
    "role" : "Owsla"
  } ]
}
```

> JSON hakkında daha fazla bilgi almak [json.org](http://json.org/) adresine bakabilirsiniz.

## Play JSON Kütüphanesi
[`play.api.libs.json`](api/scala/index.html#play.api.libs.json.package) JSON verisi sunmak için gereken veri yapılarını ve bu veri yapıları ile 
diğer veri sunuş biçimleri arasında çevrim yapmak için gerekli olaran araçları içerir. İlgili tipler:

### [`JsValue`](api/scala/index.html#play.api.libs.json.JsValue)

Herhangi bir JSON değerini sunmak için var olan bir `trait`'dir. JSON kütüphanesi geçerli JSON tiplerini gösterebilmek için `JsValue`'den türetilmiş bir `case class`'a sahiptir:

- [`JsString`](api/scala/index.html#play.api.libs.json.JsString)
- [`JsNumber`](api/scala/index.html#play.api.libs.json.JsNumber)
- [`JsBoolean`](api/scala/index.html#play.api.libs.json.JsBoolean)
- [`JsObject`](api/scala/index.html#play.api.libs.json.JsObject)
- [`JsArray`](api/scala/index.html#play.api.libs.json.JsArray)
- [`JsNull`](api/scala/index.html#play.api.libs.json.JsNull)

gibi çeşitli `JsValue` tipleri kullanılır, sizde herhangi bir JSON yapısı oluşturabilirsiniz.

### [`Json`](api/scala/index.html#play.api.libs.json.Json$)
Json objesi gerekli yardımcı bileşenleri sağlar, öncelikli olarak JsValue yapıları arasındaki çevirme işlemleri içindir.

### [`JsPath`](api/scala/index.html#play.api.libs.json.JsPath)
JsValue yapısın içine bir yol sunar, XML için olan XPath'e benzerdir. JsValue yapıları içersinde gezinmek için ve örtük dönüştürücüler için desenler içinde kullanılır.

## Bir JsValue'ye çevirmek

### String ayrıştırma kullanarak

@[convert-from-string](code/ScalaJsonSpec.scala)

### Sınıf yapısı kullanarak

@[convert-from-classes](code/ScalaJsonSpec.scala)

`Json.obj` and `Json.arr` yapıyı bir parça basitleştirebilir. Şunu unutmayın ki çoğu değer JsValue sınıf ile belirtik olarak sarmalanmaya ihtiyacı yoktur, fabrika metodları örtük dönüşüm kullanır (daha fazlası aşağıda).

@[convert-from-factory](code/ScalaJsonSpec.scala)

### Writes çeviricilerini kullanmak
Scala'dan JsValue'ye çevirme işlemi `Json.toJson[T](T)(implicit writes: Writes[T])` yardımcı metodu ile gerçekleşir. Bu fonksiyonel bağlılık [`Writes[T]`](api/scala/index.html#play.api.libs.json.Writes) tipi üzerindedir, `T` değeri bir `JsValue` değerine dönüştürülür. 

Play JSON API temel tipler için örtülü olarak `Writes` sağlar. `Int`, `Double`, `String` ve `Boolean` bunların içindedir. Aynı zamanda `Writes`, `Writes[T]` herhangi bir `T` tipi ile var olan herhangi bir tip kolleksiyonunuda destekler.

@[convert-from-simple](code/ScalaJsonSpec.scala)

Kendi modellerinizi JsValues'e dönüştürebilirisiniz, örtülü olarak `Writes` çeviricileri tanımlamalı ve kapsamın içinde olmasını sağlamalısınız.

@[sample-model](code/ScalaJsonSpec.scala)

@[convert-from-model](code/ScalaJsonSpec.scala)

Alternatif olarak birleştiren desenle kendiniz `Writes` tanımlayabilirsiniz.

> Not: Birleştiren desen konusu [[JSON Reads/Writes/Formats Combinators|ScalaJsonCombinators]] detaylı bir şekilde anlatılmıştır.

@[convert-from-model-prefwrites](code/ScalaJsonSpec.scala)

## Bir JsValue yapısında gezinme

Bir `JsValue` yapısında gezinebilir ve belirli değerleri alabilirsiniz. Sintaks ve fonksiyonellik olarak Scala XML işlemeye benzerdir.

> Not: Aşağıdaki örnekler bir önceki örnekte oluşturulan JsValue yapısına uygulanmıştır.

### Basit yol `\` 
Bir `JsValue`'ye `\` operatörünü uygulamak bunun bir JsObject olduğunu farz edersek alan argümanına eş olan özelliği döndürecektir.

@[traverse-simple-path](code/ScalaJsonSpec.scala)

### Rekürsif yol `\\`
`\\` operatörünü uygulamak mevcut objede ve aynı soydan olanlarda alan için bir arama yapacaktır.

@[traverse-recursive-path](code/ScalaJsonSpec.scala)

### İndeks bakınma (JsArray'lar için)
apply operatörü ile indeks numarası kullanarak Bir `JsArray` içindeki değere ulaşabilirisiniz.

@[traverse-array-index](code/ScalaJsonSpec.scala)

## Bir JsValue'den çevirmek

### String yardımcıları kullanarak
Küçültülmüş:

@[convert-to-string](code/ScalaJsonSpec.scala)

```
{"name":"Watership Down","location":{"lat":51.235685,"long":-1.309197},"residents":[{"name":"Fiver","age":4,"role":null},{"name":"Bigwig","age":6,"role":"Owsla"}]}
```
Okunabilir:

@[convert-to-string-pretty](code/ScalaJsonSpec.scala)

```
{
  "name" : "Watership Down",
  "location" : {
    "lat" : 51.235685,
    "long" : -1.309197
  },
  "residents" : [ {
    "name" : "Fiver",
    "age" : 4,
    "role" : null
  }, {
    "name" : "Bigwig",
    "age" : 6,
    "role" : "Owsla"
  } ]
}
```

### JsValue.as/asOpt kullanmak

Bir `JsValue`'yi başka tipe çevirmenin en kolay yolu `JsValue.as[T](implicit fjs: Reads[T]): T` kullanmaktır. Bir `JsValue`'yi `T` tipine çevirmek için (`Writes[T]`'nin tersi) [`Reads[T]`](api/scala/index.html#play.api.libs.json.Reads) tipinde bir örtülü çevirici olması zorunludur. `Writes` ile JSON API temel tipler için `Reads` sağlar.

@[convert-to-type-as](code/ScalaJsonSpec.scala)

Eğer yol bulunamazsa yada çevirme imkansızsa, `as` metodu bir `JsResultException` fırlatacaktır. `JsValue.asOpt[T](implicit fjs: Reads[T]): Option[T]` güvenli bir metoddur.

@[convert-to-type-as-opt](code/ScalaJsonSpec.scala)

Buna rağman `asOpt` metodu daha güvenlidir, herhangi bir hata bilgisi kaybolmaz.

### Doğrulama Kullanmak
Bir `JsValue`'den başka bir tipe dönüştürmek için tercih edilen yol `validate` metodunun kullanılmasıdır (`Reads` tipinde bir argüman alır). Bu hem doğrulama hem de çevirme işlemini sağlar, [`JsResult`](api/scala/index.html#play.api.libs.json.JsResult) tipinde bir sonuç döndürür. `JsResult` iki sınıf tarafından uygulanmıştır:

- [`JsSuccess`](api/scala/index.html#play.api.libs.json.JsSuccess) - Başarılı doğrulama/çevirme sunar ve sonucu sarmalar.
- [`JsError`](api/scala/index.html#play.api.libs.json.JsError) - Başarısız doğrulama/çevirme sunar ve doğrulama hatalarının bir listesini içerir.

Doğrulama sonucunu işlemek için çeşitli desenler uygulayabilirsiniz:

@[convert-to-type-validate](code/ScalaJsonSpec.scala)

### JsValue'den bir modele

JsValue'den bir modele çevirmek için, örtülü bir `Reads[T]` tanımlamalısınız. `T` sizin modelinizin tipini gösterir.

> Not: Desen `Reads`'i uygulaman için kullanılır, özelliştirilmiş doğrulama [[JSON Reads/Writes/Formats Combinators|ScalaJsonCombinators]] konusunda anlatılmıştır.

@[sample-model](code/ScalaJsonSpec.scala)

@[convert-to-model](code/ScalaJsonSpec.scala)

> **Sonraki:** [[HTTP ile JSON|ScalaJsonHttp]]