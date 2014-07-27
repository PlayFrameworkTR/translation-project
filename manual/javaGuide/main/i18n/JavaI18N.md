<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Mesajları haricileştirme ve uluslarlarasılaştırma

## Uygulamanızın desteklediği dilleri belirtmek

Uygulamanızın dillerini belirtmek için, geçerli bir **ISO Dil Kodu** tarafından belirtilen ve tercihen geçerli bir **ISO Ülke Kodu** ile devam eden geçerli bir dil koduna ihtiyacınız vardır. Örneğin, `fr` veya `en-US`.

Başlangıç olarak, uygulamanızın desteklediği dilleri `conf/application.conf` dosyasında belirtmelisiniz:

```
application.langs="en,en-US,fr"
```

## Mesajları haricileştirme

Mesajları, `conf/messages.xxx` dosyalarında haricileştirebilirsiniz.

Varsayılan `conf/messages`dosyası tüm dillerle eşleşir. Dilerseniz, örneğin `conf/messages.fr` veya `conf/messages.en-US` şeklinde ilave dil mesaj dosyaları belirtebilirsiniz.

`play.i18n.Messages` nesnesini kullanarak mevcut dil için mesajları çağırabilirsiniz:

```
String title = Messages.get("home.title")
```

Aynı zamanda, dili belirtebilirsiniz:

```
String title = Messages.get(new Lang(Lang.forCode("fr")), "home.title")
```

> **Not:** Eğer kapsamda bir `Request`iniz varsa o, `Accept-Language` başlığından çıkarılan ve uygulamanın desteklenen dillerinden biriyle eşleşen tercih edilen dil için varsayılan bir `Lang`değeri sağlayacaktır. Aynı zamanda, `Lang`ı şablonunuza şu şekilde kapalı bir parametre olarak da eklemelisiniz: `@()(implicit lang: Lang)`.

## Şablonlarda kullanım
```
@import play.i18n._
@Messages.get("key")
```
## Mesajları biçimlendirme

Mesajlar, `java.text.MessageFormat` kütüphanesi kullanılarak biçimlendirilirler. Örneğin, bu şekilde bir mesaj tanımladıysanız:

```
files.summary=The disk {1} contains {0} file(s).
```

Parametreleri şöyle belirtebilirsiniz:

```
Messages.get("files.summary", d.files.length, d.name)
```

## Kesme işaretleri üzerine notlar

Mesajlar `java.text.MessageFormat` kullandığından, lütfen kesme işaretlerinin (tek tırnakların) parametre yer değişimlerini kurtama işlemi için birer meta karakter olarak kullanıldığının farkında olun.

Örneğin, aşağıdaki mesajları tanımladıysanız:

@[single-apostrophe](code/javaguide/i18n/messages)
@[parameter-escaping](code/javaguide/i18n/messages)

aşağıdaki sonucu elde etmeyi beklemelisiniz:

@[single-apostrophe](code/javaguide/i18n/JavaI18N.java)
@[parameter-escaping](code/javaguide/i18n/JavaI18N.java)

## Bir HTTP isteğinden desteklenen dilleri almak

Belirli bir HTTP isteğinin desteklenen dillerini şu şekilde alabilirsiniz:

```
public static Result index() {
  return ok(request().acceptLanguages());
}
```

> **Sonraki:** [[Uygulama Global nesnesi | JavaGlobal]]