<!--- Copyright (C) 2009-2014 Typesafe Inc. <http://www.typesafe.com> -->
# Asenkron yanıtları işlemek

## Controller'ları asenkron yapmak

Play Framework aşağıdan yukarıya asenkron olarak geliştirilmiştir. Play her isteği asenkron ve tıkanmasız olarak ele alır.

Varsayılan yapılandırma asenkron controller'lar için ayarlanmıştır. Başka bir deyişle uygulama kodu controller içinde bir işlemin sonucunu beklemek gibi tıkanmalı işlerden sakınmalıdır. Bu gibi tıkanmalı işlemlere örnek olarak JDBC çağrıları, streaming API, HTTP istekleri ve uzun süren hesaplamalar verilebilir.

Varsayılan execution context içindeki thread sayısını artırarak daha fazla eş zamanlı isteğin tıkanmalı controller'lar tarafından işlenmesini sağlamak mümkün olsa da controller'ları tavsiye edildiği gibi asenkron olarak bırakmak ölçeklemeyi kolaylaştırır ve ağır yük altında sistemin hızlı tepki verebilmesini sağlar.

## Tıkanmasız action'lar yaratmak

Play'in çalışma şekli dolayısıyla action kodu mümkün olduğunca hızlı, örnek olarak tıkanmasız olmalıdır. O halde henüz oluşturamıyorsak yanıt olarak ne döneceğiz? Bir *future* yanıt!

Bir `Future[Result]` gelecekte bir zamanda `Result` türünden bir değere sahip olacaktır. Normal bir `Result` yerine bir `Future[Result]` dönerek yanıtı tıkanmasız olarak hızlıca oluşturabiliyoruz. Söz verilen sonuç oluşur oluşmaz Play yanıtı sunacaktır.

Web istemci yanıtı beklerken bloklanacaktır fakat sunucuda hiçbir şey bloklanmayacağından sunucu kaynakları diğer istemcilere hizmet vermek için kullanılabilir.

## `Future[Result]` oluşturmak

Bir `Future[Result]` oluşturmak için önce yanıtın hesaplanacağı asıl değeri verecek bir başka future'a ihtiyacımız olacak:

@[future-result](code/ScalaAsync.scala)

Play'in tüm asenkron API çağrıları geriye bir `Future` döner. Harici bir web servisi `play.api.libs.WS` kullanarak çağırdığınızda, Akka ile asenkron işler zamanladığınızda ya da `play.api.libs.Akka` kullanarak actor'lerle haberleştiğinizde de durum böyledir.

Aşağıda bir kod bloğunu asenkron olarak çalıştırarak bir `Future` elde etmek için basit bir yol alıyor:

@[intensive-computation](code/ScalaAsync.scala)

> **Not:** Future'ların hangi thread kodu üzerinde çalıştığını anlamak önemlidir. İki kod bloğu yukarıda Play'in varsayılan execution context'i import ediliyor. Bu execution context, Future API'de callback kabul eden tüm metotlara örtük parametre olarak gönderilir. Execution context zorunlu olmasa da genel olarak bir thread havuzuna karşılık gelir.
>
> Senkron IO'yu yalnızca bir `Future` ile sarmalayarak sihirli bir şekilde asenkron hale getiremezsiniz. Eğer tıkanmalı işlemlerden sakınmak için uygulamanızın mimarisini değiştiremiyorsanız bu işlemler bir noktada çalışacak ve üzerinde çalıştıkları thread tıkanacaktır. O halde işlemi bir `Future`  ile sarmalamanın yanı sıra beklenen eşzamanlılık ile başa çıkabilecek sayıda thread ile yapılandırılmış ayrı bir execution context içinde çalışacak şekilde ayarlamalısınız. Daha fazla bilgi için [[Play thread havuzlarını anlamak|ThreadPools]] sayfasına bakabilirsiniz.
>
> Tıkanmalı işlemler için Actor'leri kullanmak faydalı olabilir. Actor'ler zaman aşımını ve hata durumlarını ele almak, tıkanmalı execution context'ler oluşturmak ve servisle ilişkili olabilecek herhangi bir durumu yönetmek için temiz bir model sunar. Ayrıca Actor'ler eş zamanlı önbellek ve veritabanı erişimi sağlamak için `ScatterGatherFirstCompletedRouter` gibi desenler sunmanın yanında bir sunucu kümesinde uzak çalıştırmaya da izin verir. Fakat bir Actor sizin durumunuz için aşırıya kaçabilir.

## Future dönmek

Şimdiye dek action oluşturmak için `Action.apply` yapıcı metodunu kullanmamıza benzer şekilde asenkron bir yanıt dönmek için `Action.async` yapıcı metodunu kullanmamız gerekiyor:

@[async-result](code/ScalaAsync.scala)

## Action'lar varsayılan olarak asenkrondur

Play [[action|ScalaActions]]'ları varsayılan olarak asenkrondur. Örneğin aşağıdaki controller kodu içinde `{ Ok(...) }` bölümü controller'ın gövde bölümü değil `Action` nesnesinin `apply` metoduna geçirilen anonim bir fonksiyondur. Bu anonim fonksiyon, türü `Action` olan bir nesne yaratır. İçeride ise, yazmış olduğunuz anonim fonksiyon çağrılarak sonucu bir `Future` içinde saklanacaktır.

@[echo-action](/manual/scalaGuide/main/http/code/ScalaActions.scala)

> **Not:** Hem `Action.apply` hem de `Action.async` içeride aynı şekilde ele alınan `Action` nesneleri oluşturur. Yalnızca tek bir çeşit `Action` vardır ve asenkrondur. Başka bir deyişle biri senkron, diğeri asenkron olan iki farklı `Action` yoktur. `.async` yapıcısı `Future` döndüren API'ler üzerine kurulu action'ların kolayca oluşturulmasını sağlayarak tıkanmasız kod yazmayı kolaylaştırır.

## Zaman aşımıyla başa çıkmak

Çoğunlukla zaman aşımlarını düzgünce ele alarak bir şeylerin yanlış gitmesi durumunda web tarayıcının tıkanarak beklemesinin önüne geçmek iyi olur. Bir promise ile promise zaman aşımını birleştirerek bu gibi durumlarla kolayca başa çıkabilirsiniz.

@[timeout](code/ScalaAsync.scala)

> **Sonraki:** [[HTTP yanıtlarını stream etmek | ScalaStream]]
