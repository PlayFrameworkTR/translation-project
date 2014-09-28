<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Mesajlar ve uluslararasılaşma

## Uygulamanız tarafından desteklenen dilleri belirtmek

Geçerli bir dil kodu, geçerli bir **ISO 639-2 dil kodu** tarafından belirtilir, onu tercihen geçerli bir **ISO 3166-1 alpha-2 ülke kodu** takip eder. Örneğin, `fr` veya `en-US`.

Başlangıç olarak, uygulamanızın desteklediği dilleri `conf/application.conf` dosyasında belirtmelisiniz:
```
application.langs="en,en-US,fr"
```

## Mesajları haricileştirme

Mesajları, `conf/messages.xxx` dosyalarında haricileştirebilirsiniz.

Varsayılan `conf/messages`dosyası tüm dillerle eşleşir. Dilerseniz, örneğin `conf/messages.fr` veya `conf/messages.en-US` şeklinde ilave dil mesaj dosyaları belirtebilirsiniz.

`play.i18n.Messages` nesnesini kullanarak mevcut dil için mesajları çağırabilirsiniz:

```scala
val title = Messages("home.title")
```

Bütün uluslararasılaştırma API çağrıları mevcut kapsamdan alınan örtük bir `play.api.i18.Lang` argümanı alır. Aynı zamanda dili kendiniz de belirtebilirsiniz:

```scala
val title = Messages("home.title")(Lang("fr"))
```

> **Not:** Eğer kapsamda bir örtük `Request`iniz varsa o, `Accept-Language` başlığından çıkarılan ve uygulamanın desteklenen dillerinden biriyle eşleşen tercih edilen dil için varsayılan bir `Lang`değeri sağlayacaktır. Aynı zamanda, `Lang` örtük parametresini şablonunuza şu şekilde kapalı bir parametre olarak da eklemelisiniz: `@()(implicit lang: Lang)`.

## Mesajların biçimi

Mesajlar `java.text.MessageFormat` kütüphanesi kullanılarak biçimlendirilir. Örneğin şu şekilde bir mesaj tanımladığınızı varsayalım:

```
files.summary=The disk {1} contains {0} file(s).
```

Parametreleri şu şekilde belirtebilirsiniz:

```scala
Messages("files.summary", d.files.length, d.name)
```

## Kesme işareti üzerine notlar

Mesajlar; `java.text.MessageFormat` kullandığından, kesme işaretleri (single quote), parametre değişimlerini belirtmek için meta karakter olarak kullanıldığını lütfen unutmayın.

Örneğin, aşağıdaki mesajları tanımladıysanız:

@[apostrophe-messages](code/scalaguide/i18n/messages)
@[parameter-escaping](code/scalaguide/i18n/messages)

aşağıdaki sonucu elde etmeyi beklemelisiniz:

@[apostrophe-messages](code/ScalaI18N.scala)
@[parameter-escaping](code/ScalaI18N.scala)

## Bir HTTP isteğinden desteklenen dilleri almak

Belirli bir HTTP isteğinin desteklenen dillerini şu şekilde alabilirsiniz:

```scala
def index = Action { request =>
  Ok("Languages: " + request.acceptLanguages.map(_.code).mkString(", "))
}
```

> **Sonraki:** [[Uygulama Global nesnesi | ScalaGlobal]]