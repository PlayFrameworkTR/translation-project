<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# İçerik müzakeresi

İçerik müzakeresi aynı kaynağın (URI) farklı görünümlerini sunabilmeyi mümkün kılan bir mekanizmadır. Değişik biçimlerde (XML, JSON, vb.) çıktı veren Web Servisleri yazmak için kullanılabilir. Sunucu-sürülen müzakere temel olarak `Accept*` istek başlıkları kullanılarak gerçekleştirilir. [HTTP specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec12.html) içerisinde içerik müzakeresi hakkında daha fazla bilgiye ulaşabilirsiniz.

# Dil

Bir istek için kabul edilen dillerin listesini, bu dilleri `Accept-Language` başlığından çıkaran ve onları kalite değerlerine göre sıralayan `play.api.mvc.RequestHeader#acceptLanguages` metodunu kullanarak elde edebilirsiniz. Play bunu `play.api.mvc.Controller#lang` metodunda kullanır. Bu metod action'larınızın en iyi dili kullanmaları için örtük bir `play.api.i18n.Lang` sağlar (eğer uygulamanız tarafından destekleniyorsa, aksi halde uygulamanın varsayılan dili kullanılır).

# İçerik

Benzer şekilde `play.api.mvc.RequestHeader#acceptedTypes` metodu bir istek için kabul edilen yanıt MIME türlerini verir. Bu türleri `Accept` istek başlığından alır ve onları kalite faktörlerine göre sıralar.

Aslında `Accept` başlığı gerçekten MIME türleri barındırmak yerine medya aralıkları içerir (örneğin tüm metin yanıtlarını kabul eden bir istek `text/*` aralığını setleyebilir; `*/*` aralığı tüm yanıt türleri kabul edilir anlamına gelir). Controller'lar medya aralıklarını yönetmeniz için yüksek seviyeli bir `render` metodu sunar. Örnek olarak aşağıdaki action tanımını inceleyin:

@[negotiate_accept_type](code/ScalaContentNegotiation.scala)

`Accepts.Html()` ve `Accepts.Json()` verilen medya aralığının sırasıyla `text/html` ve `application/json` ile eşleştiğini test eden çıkarıcılardır. `render` metodu `play.api.http.MediaRange`'ten `play.api.mvc.Result`'a bir kısmi fonksiyon alır ve onu isteğin `Accept` başlığında bulunan her bir medya aralığı için tercih sırasına göre uygulamaya çalışır. Eğer kabul edilen medya aralıklarından hiçbiri fonksiyonunuz tarafından desteklenmiyorsa `NotAcceptable` yanıtı döndürülür.

Örneğin bir istemci `Accept` başlığında `*/*;q=0.5,application/json` değeri ile bir istek yaptığında ki bu tüm yanıt türlerini kabul ettiği fakat JSON tercih ettiği anlamına gelir, yukarıdaki kod JSON görünümünü döndürecektir. Eğer başka bir istemci `Accept` başlığı için yalnızca XML kabul ettiğini belirten `application/xml` değerini kullanırsa yukarıdaki kod `NotAcceptable` döndürür.

# İstek çıkarıcılar

Play tarafından `render` metodunda dahili olarak desteklenen MIME türlerinin bir listesine erişmek için `play.api.mvc.AcceptExtractors.Accepts` nesnesinin API dokümantasyonuna bakınız. İstediğiniz MIME türü için `play.api.mvc.Accepting` case class'ını kullanarak kolayca kendi çıkarıcınızı yazabilirsiniz. Örneğin aşağıdaki kod `audio/mp3` MIME türü ile eşleşen medya aralıklarını test eden bir çıkarıcı yaratır:

@[extract_custom_accept_type](code/ScalaContentNegotiation.scala)


> **Sonraki:** [[Asenkron HTTP programlama | ScalaAsync]]
