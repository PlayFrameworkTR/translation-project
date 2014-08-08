<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Action birleştirme

Bu bölüm genel action işlevselliği tanımlamanın birçok yolunu göstermektedir.

## Özel action yapıcılar

[[Daha önce|ScalaActions]] action tanımlamanın istek parametresi ile, istek parametresi olmadan, gövde ayrıştırıcı ile vb. olmak üzere pek çok yolu olduğunu görmüştük. Aslında [[asenkron programlama|ScalaAsync]] bölümünde göreceğimiz üzere bundan çok daha fazlası var.

Action oluşturmak için kullanılan tüm bu metotlar [`ActionBuilder`](api/scala/index.html#play.api.mvc.ActionBuilder) isimli bir trait içinde tanımlanmışlardır. Action'larımızı tanımlamak için kullandığımız [`Action`](api/scala/index.html#play.api.mvc.Action$) nesnesi yalnızca bu trait'in bir örneğidir.

Önce bir loglama örneği ile başlayalım. Bu action'a yapılan her çağrıyı loglamak istiyoruz.

Bunun ilk yolu bu özelliği `ActionBuilder` tarafından üretilen her action için çağrılan `invokeBlock` içinde gerçeklemektir:

@[basic-logging](code/ScalaActionsComposition.scala)

Şimdi bunu `Action`'ı kullandığımız şekilde kullanabiliriz:

@[basic-logging-index](code/ScalaActionsComposition.scala)

`ActionBuilder` action oluşturmanın tüm yollarını sağladığından bu yöntem örnek olarak özel gövde ayrıştırıcı ile de çalışır:

@[basic-logging-parse](code/ScalaActionsComposition.scala)


### Action'ları birleştirmek

Çoğu uygulamada birden fazla action yapıcıya ihtiyaç duyarız. Bunlardan kimi değişik türde yetkilendirme yaparken, diğerleri başka işlevler sağlayabilir. Hangi durum olursa olsun her bir action yapıcı için loglama kodumuzu tekrar tekrar yazmak istemeyiz. Aksine bu özelliği tekrar kullanılabilir şekilde tanımlamak isteriz.

Tekar kullanılabilir action kodu action'ları sarmalayarak gerçeklenebilir:

@[actions-class-wrapping](code/ScalaActionsComposition.scala)

Ayrıca `Action` action yapıcısını da kendi action sınıfımızı tanımlamadan action oluşturmak için kullanabiliriz:

@[actions-def-wrapping](code/ScalaActionsComposition.scala)

Action'lar action yapıcılar içine `composeAction` metodunu kullanarak dahil edilebilirler:

@[actions-wrapping-builder](code/ScalaActionsComposition.scala)

Yapıcı artık önceki gibi kullanılabilir:

@[actions-wrapping-index](code/ScalaActionsComposition.scala)

Sarmalayan action'ları action yapıcı kullanmadan da dahil edebiliriz:

@[actions-wrapping-direct](code/ScalaActionsComposition.scala)

### Daha karmaşık action'lar

Şu ana dek gelen isteği pek de etkilemeyen action'ları gösterdik. Elbette gelen istek nesnesini okuyabilir ve değiştirebiliriz:

@[modify-request](code/ScalaActionsComposition.scala)

> **Not:** Play dahili olarak X-Forwarded-For başlık desteğine sahiptir.

İsteği engelleyebiliriz:

@[block-request](code/ScalaActionsComposition.scala)

Son olarak dönen yanıtı değiştirebiliriz:

@[modify-result](code/ScalaActionsComposition.scala)

## Farklı istek türleri

Action birleştirme HTTP istek ve yanıt seviyesinde ek işlem yapmanıza izin veriyor olsa da çoğu zaman isteğe bağlam ekleyen ya da istek üzerinde doğrulama yapan veri dönüştürme hatları oluşturmak isteyeceksiniz. `ActionFunction` istek üzerinde bir fonksiyon olarak düşünülebilir ve hem istek türü hem de bir sonraki katmana geçirilen yanıt türünde parametriktir. Her action fonksiyonu yetkilendirme, veritabanı araması, izin kontrolleri ve action'lar arasında tekrar kullanmak isteyebileceğiniz diğer işlemler gibi modüler işlemleri betimleyebilir.

Farklı türde işlemler için kullanışlı olan ve `ActionFunction`'ı implement eden birkaç öntanımlı trait bulunmaktadır:

* [`ActionTransformer`](api/scala/index.html#play.api.mvc.ActionTransformer) isteği, örneğin ek bilgi ekleyerek değiştirebilir.
* [`ActionFilter`](api/scala/index.html#play.api.mvc.ActionFilter) seçimli olarak isteklerde araya girebilir, isteği değiştirmeden hata oluşturmak için kullanılabilir.
* [`ActionRefiner`](api/scala/index.html#play.api.mvc.ActionRefiner) yukarıdaki ikisinin genel durumudur.
* [`ActionBuilder`](api/scala/index.html#play.api.mvc.ActionBuilder) girdi olarak `Request` alan fonksiyonların özel durumudur, böylece action oluşturabilir.

`invokeBlock` metodunu implement ederek kendi `ActionFunction` tanımınızı yapabilirsiniz. Genellikle girdi ve çıktı türlerini `Request` örneği (`WrappedRequest` kullanarak) olarak tanımlamak uygundur ancak zorunlu değildir.

### Kimlik doğrulama

Action fonksiyonları için en yaygın kullanım durumlarından bir kimlik doğrulamadır. Kullanıcıyı orijinal istekten alıp yeni bir `UserRequest` içine ekleyen bir action dönüştürücüyü kolayca yazabiliriz. Bunun da girdi olarak `Request` aldığı için aslında bir `ActionBuilder` olduğuna dikkat edin.

@[authenticated-action-builder](code/ScalaActionsComposition.scala)

Play aynı zamanda dahili bir kimlik doğrulama action yapıcı sağlar. Nasıl kullanılacağı hakkında daha fazla bilgi [burada](api/scala/index.html#play.api.mvc.Security$$AuthenticatedBuilder$) bulunabilir.

> **Not:** Dahili kimlik doğrulama action yapıcı yalnızca basit kullanım durumları için gereken kod miktarını en aza indirmek için sağlanan bir yardımcıdır ve gerçeklemesi yukarıdaki örneğe çok benzer şekildedir.
>
> Eğer dahili kimlik doğrulama action tarafından sağlanabileceğinden daha karmaşık gereksinimleriniz varsa kendizinkini gerçeklemeniz hem kolaydır hem de tavsiye edilir.

### İsteklere bilgi eklemek

Şimdi `Item` türünden nesneler üzerinde işlem yapan bir REST API düşünün. `/item/:itemId` yolu altında pek çok yol olabilir ve bunların her biri öğeyi veritabanından sorgulama ihtiyacı duyabilir. Bu durumda sorgulama işini bir action fonksiyonu içine koymak kullanışlı olabilir.

Önecelikle `UserRequest` içine bir `Item` ekleyen bir istek nesnesi oluşturacağız:

@[request-with-item](code/ScalaActionsComposition.scala)

Daha sonra öğeyi sorgulayan ve bir hata (`Left`) ya da yeni bir `ItemRequest` (`Right`) içeren bir `Either` döndüren bir action arıtıcı tanımlayacağız:

@[item-action-builder](code/ScalaActionsComposition.scala)

### İstekleri doğrulamak

Son olarak bir action fonksiyonunun isteğin devam edip etmemesine karar vermesini isteyebiliriz. Örneğin `UserAction`'dan gelen kullanıcının `ItemAction`'dan gelen öğeye erişim yetkisi olup olmadığını kontrol etmek ve yetkisi yoksa hata dönmek istiyor olabiliriz:

@[permission-check-action](code/ScalaActionsComposition.scala)

### Hepsini bir araya getirmek

Şimdi tüm bu action fonksiyonlarını (bir `ActionBuilder` başlayarak) bir action oluşturmak için `andThen` kullanarak bir araya getirebiliriz:

@[item-action-use](code/ScalaActionsComposition.scala)


Play ayrıca global kestirme ihtiyaçları için bir [[global filtre API | ScalaHttpFilters]] sağlar.

> **Sonraki:** [[İçerik müzakeresi| ScalaContentNegotiation]]
