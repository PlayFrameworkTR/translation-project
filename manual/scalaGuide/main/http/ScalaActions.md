<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Action'lar, Controller'lar ve Result'lar

## Action nedir?

Bir Play uygulaması tarafından alınan isteklerin çoğu bir `Action` tarafından karşılanır.

`play.api.mvc.Action` basitçe bir isteği karşılayarak istemciye gönderilmek üzere bir yanıt oluşturan `(play.api.mvc.Request => play.api.mvc.Result)` şeklinde bir fonksiyondur.

@[echo-action](code/ScalaActions.scala)

Action, web istemciye gönderilecek HTTP yanıtını ifade eden `play.api.mvc.Result` türünden bir değer döndürür. Bu örnekte `Ok` yanıt gövdesi **text/plain** olan bir **200 OK** yanıtı oluşturur.

## Action oluşturma

`play.api.mvc.Action` eş nesnesi bir Action değeri oluşturmak için çeşitli yardımcı metotlar sunar.

Bunlardan en basit olanı yalnızca `Result` döndüren bir ifade bloğunu argüman olarak alır:

@[zero-arg-action](code/ScalaActions.scala)

Bir Action oluşturmanın en kolay yolu bu olsa da gelen isteğe bir referans alamayız. Çoğu zaman bu Action'ı çağıran HTTP isteğine erişmeye ihtiyaç olur.

Bu sebeple `Request => Result` fonksiyonunu argüman olarak alan başka bir Action yapıcı bulunmaktadır.

@[request-action](code/ScalaActions.scala)

İhtiyaç duyan diğer API'lerin de örtük biçimde erişebilmesi için `request` parametresini `implicit` olarak işaretlemek kullanışlı olur.

@[implicit-request-action](code/ScalaActions.scala)

Bir Action değeri oluşturmanın son yolu ek bir `BodyParser`(Gövde ayrıştırıcı) argümanı belirtmektir:

@[json-parser-action](code/ScalaActions.scala)

Gövde ayrıştırıcılar bu kılavuzda daha sonra ayrıntılı şekilde ele alınacak. Şimdilik bilmeniz gereken Action değeri oluşturmanın diğer yollarının varsayılan olarak **Herhangi içerik gövde ayrıştırıcı** kullanıyor olmalarıdır.

## Controller'lar action üreticidir

`Controller`, `Action` değerler üreten bir singleton nesneden başka bir şey değildir.

Bir action üretici tanımlamanın en basit yolu `Action` değeri döndüren parametresiz bir metottur.

@[full-controller](code/ScalaActions.scala)

Elbette üretici metot parametreler alabilir ve bu parametreler `Action` closure tarafından hapsedilebilir.

@[parameter-action](code/ScalaActions.scala)

## Basit yanıtlar

Şimdilik yalnızca basit yanıtlarla ilgileniyoruz: web istemciye gönderilecek durum kodu, HTTP başlıkları ve gövdeden ibaret olan HTTP yanıtı.

Bu yanıtlar `play.api.mvc.Result` tarafından tanımlanır:

@[simple-result-action](code/ScalaActions.scala)

Elbette yukarıdaki örnekteki `Ok` yanıtı gibi sık kullanılan yanıtları oluşturmak için çeşitli yardımcılar bulunmaktadır.

@[ok-result-action](code/ScalaActions.scala)

Bu, önceki ile birebir aynı sonucu üretir.

Aşağıda çeşitli yanıtlar üretmek için örnekler verilmiştir.

@[other-results](code/ScalaActions.scala)

Tüm bu yardımcılar `play.api.mvc.Results` trait ve eş nesnesinde bulunmaktadırlar.

## Yönlendirmeler de birer basit yanıttır

Tarayıcıyı yeni bir URL'e yönlendirmek de başka bir tür basit yanıttır. Fakat bu yanıt türleri yanıt gövdesi içermezler.

Yönlendirme yanıtları yaratmak için çok sayıda yardımcı bulunmaktadır.

@[redirect-action](code/ScalaActions.scala)

Varsayılan `303 SEE_OTHER` yanıt türüdür; fakat ihtiyaç duyarsanız farklı bir durum kodu belirtebilirsiniz:

@[moved-permanently-action](code/ScalaActions.scala)

## "TODO" taslak sayfası

`TODO` olarak tanımlanan boş bir `Action` kullanabilirsiniz. Bu durumda sonuç standart bir ‘Henüz gerçeklenmedi’ sayfası olur.

@[todo-action](code/ScalaActions.scala)

> **Sonraki:** [[HTTP Routing | ScalaRouting]]
