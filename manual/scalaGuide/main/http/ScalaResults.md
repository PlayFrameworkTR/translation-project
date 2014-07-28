<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Yanıtları işlemek

## Varsayılan Content-Type'ı değiştirmek

Yanıt içerik türü yanıt gövdesi olarak belirttiğiniz Scala değerinden otomatik olarak çıkarılır.

Örneğin:

@[content-type_text](code/ScalaResults.scala)


`Content-Type` başlığını otomatik olarak `text/plain` setlerken:

@[content-type_xml](code/ScalaResults.scala)

Content-Type başlığını `application/xml` olarak setleyecektir.

> **İpucu:** bu işlem `play.api.http.ContentTypeOf` tür sınıfı tarafından gerçekleştirilir.

Bu yöntem genelde kullanışlıdır fakat bazen bu davranışı değiştirmek istersiniz. Benzer bir yanıtı farklı bir `Content-Type` başlığı ile oluşturmak için result üzerinde `as(newContentType)` metodunu çağırmanız yeterlidir.

@[content-type_html](code/ScalaResults.scala)

Daha da iyisi aşağıdaki gibidir:

@[content-type_defined_html](code/ScalaResults.scala)

> **Not:** `"text/html"` yerine `HTML` kullandığınızda charset sizin için otomatik olarak ele alınacak ve Content-Type başlığı `text/html; charset=utf-8` olarak setlenecektir. Bunu birazdan göreceğiz.

## HTTP başlıklarını işlemek

Yanıta istediğiniz HTTP başlığını ekleyebilir ya da mevcut başlığı güncelleyebilirsiniz:

@[set-headers](code/ScalaResults.scala)

Eğer setlenmek istenen HTTP başlığı orijinal yanıtta mevcutsa başlığın önceki değerinin üzerine yazılır.

## Çerezleri setlemek ya da silmek

Çerezler yalnızca HTTP başlıklarının özel bir biçimidir fakat onları yönetmek için bazı yardımcılar sunuyoruz.

HTTP yanıtına aşağıdaki gibi kolayca bir çerez ekleyebilirsiniz:

@[set-cookies](code/ScalaResults.scala)

Ya da tarayıcıda daha önce saklanmış olan çerezi silebilirsiniz:

@[discarding-cookies](code/ScalaResults.scala)

Aynı yanıtta çerezleri hem ekleyebilir hem de silebilirsiniz:

@[setting-discarding-cookies](code/ScalaResults.scala)

## Metin tabanlı HTTP yanıtlarının charset'ini değiştirmek

Metin tabanlı HTTP yanıtları için charset'in düzgün ele alınması çok önemlidir. Play bunu sizin için yönetir ve varsayılan olarak `utf-8` kullanır.

Charset hem metin yanıtının ağ soketi üzerinden üzerinden gönderilecek bayt dizisine çevrilmesinde hem de `Content-Type` başlığının düzgün `;charset=xxx` eklentisi ile güncellenmesinde kullanılır.

Charset `play.api.mvc.Codec` tür sınıfı tarafından otomatik olarak yönetilir. Örtük bir `play.api.mvc.Codec` örneğini şimdiki kapsama import ederek tüm işlemler tarafından kullanılacak charset'i değiştirebilirsiniz:

@[full-application-set-myCustomCharset](code/ScalaResults.scala)

Yukarıdaki örnekte kapsamda örtük bir charset değeri olduğundan hem `Ok(...)` metodu tarafından XML mesajın `ISO-8859-1` kodlanmış baytlara dönüştürülmesinde hem de `text/html; charset=iso-8859-1` Content-Type başlığının oluşturulmasında kullanılacaktır.

Şimdi `HTML` metodunun nasıl çalıştığını merak ediyorsanız tanımı aşağıda yer alıyor:

@[Source-Code-HTML](code/ScalaResults.scala)

Eğer charset'i genel bir yolla ele almaya ihtiyaç duyarsanız benzer bir yöntemi kendi API'niz içinde kullanabilirsiniz.

> **Sonraki:** [[Session ve Flash kapsamları | ScalaSessionFlash]]
