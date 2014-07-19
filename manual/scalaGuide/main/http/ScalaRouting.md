<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# HTTP yönlendirme

## Yerleşik HTTP router

Router gelen her HTTP isteğini bir Action'a dönüştüren bileşendir.

Bir HTTP isteği MVC çatısı tarafından bir olay olarak görülür. Bu olay iki önemli bilgi parçası içerir:

- query string de dahil olmak üzere istek yolu (örneğin `/clients/1542`, `/photos/list`)
- HTTP metodu (GET, POST, vb.)

Yönlendirmeler `conf/routes` dosyasında tanımlanmıştır. Bu dosya da derlenen bir dosyadır. Bu nedenle yönlendirme hatalarını doğrudan tarayıcınızda görürsünüz:

[[images/routesError.png]]

## routes dosyası sözdizimi

`conf/routes` router tarafından kullanılan ayar dosyasıdır. Bu dosya uygulama tarafından ihtiyaç duyulan bütün yönlendirmeleri listeler. Her yönlendirme bir HTTP metodu ve URI deseninden oluşur ve bir `Action` üretici ile ilişkilidir.

Bir yönlendirme tanımının neye benzediğine bir bakalım:

@[clients-show](code/scalaguide.http.routing.routes)

Her yönlendirme bir HTTP metodu ile başlar ve URI deseni ile devam eder. Son eleman ise action çağrısı tanımıdır.

routes dosyasına `#` karakteri ile yorum satırları ekleyebilirsiniz.

@[clients-show-comment](code/scalaguide.http.routing.routes)

## HTTP metodu

HTTP metodu HTTP tarafından desteklenen metodlardan herhangi biri olabilir (`GET`, `POST`, `PUT`, `DELETE`, `HEAD`).

## URI deseni

URI deseni yönlendirmenin istek yolunu tanımlar. İstek yolunun belli kısımları değişken olabilir.

### Sabit yol

Örneğin gelen `GET /clients/all` isteğini yakalamak için aşağıdaki yönlendirmeyi tanımlayabilirsiniz:

@[static-path](code/scalaguide.http.routing.routes)

### Değişken kısımlar

Eğer bir kaynağı ID ile getiren bir yönlendirme tanımlamak isterseniz değişken bir kısım eklemeniz gerekir:

@[clients-show](code/scalaguide.http.routing.routes)

> URI deseninin birden fazla değişken kısmı olabileceğini unutmayın.

Bir değişken kısım için varsayılan kısım eşleme stratejisi şu düzenli ifade ile tanımlanır: `[^/]+`. Bu, `:id` şeklinde tanımlanan bir değişken kısmın yalnız bir URI kısmına eşleneceği anlamına gelir.

### Birden fazla / içeren değişken kısımlar

Eğer bir değişken kısmın `/` ile ayrılan birden fazla URI yol kısmını yakalamasını isterseniz, `.+` düzenli ifadesini kullanan `*id` sözdizimini kullanarak bir değişken kısım tanımlayabilirsiniz:

@[spanning-path](code/scalaguide.http.routing.routes)

Burada `GET /files/images/logo.png` gibi bir istek için değişken kısım `images/logo.png` değerini yakalayacaktır.

### Özel düzenli ifadeler ile değişken kısımlar

Değişken kısım için `$id<regex>` sözdizimini kullanarak kendi düzenli ifadenizi de tanımlayabilirsiniz:

@[regex-path](code/scalaguide.http.routing.routes)

## Action üretici metodu çağırma

Yönlendirme tanımının son kısmı çağrı kısmıdır. Bu kısım bir `play.api.mvc.Action` değeri döndüren bir metoda geçerli bir çağrı tanımlamalıdır. Bu tanım doğal olarak bir controller action metodu olacaktır.

Eğer metot hiçbir parametre tanımlamıyorsa yalnızca tam metot adını vermeniz yeterlidir:

@[home-page](code/scalaguide.http.routing.routes)

Eğer action metodu bazı parametreler tanımlıyorsa tüm bu parametreler istek URI içerisinde aranacak ve doğrudan URI yolundan ya da query string'inden elde edilecektir.

@[page](code/scalaguide.http.routing.routes)

Ya da:

@[page](code/scalaguide.http.routing.query.routes)

Aşağıda buna karşılık gelen `controllers.Application` controller'ındaki `show` metot tanımı bulunuyor.

@[show-page-action](code/ScalaRouting.scala)

### Parametre türleri

`String` türündeki parametreler için parametre türünü yazmaya gerek yoktur. Eğer Play'in gelen parametreyi belirli bir Scala türüne dönüştürmesini istiyorsanız bir tür belirtebilirsiniz:

@[clients-show](code/scalaguide.http.routing.routes)

Ve aynısını karşılık gelen `controllers.Clients` controller'ındaki `show` metodu için de yapmalısınız:

@[show-client-action](code/ScalaRouting.scala)

### Sabit değerli parametreler

Bazen bir parametre için sabit bir değer belirtmek isteyebilirsiniz:

@[page](code/scalaguide.http.routing.fixed.routes)

### Varsayılan değerli parametreler

Ayrıca gelen istekte bir değer bulunamadığında kullanılacak varsayılan bir değer belirtebilirsiniz:

@[clients](code/scalaguide.http.routing.defaultvalue.routes)

### Seçimli parametreler

Tüm isteklerde bulunması zorunlu olmayan seçimli bir parametre de tanımlayabilirsiniz:

@[optional](code/scalaguide.http.routing.routes)

## Yönlendirme önceliği

Farklı yönledirmeler aynı istekle eşleşebilir. Eğer bir çakışma varsa ilk yönlendirme (tanım sırasına göre) kullanılır.

## Tersine yönlendirme

Router ayrıca bir Scala çağrısından bir URL oluşturmak için de kullanılabilir. Bu yöntem tüm URI desenlerinizin ortak bir ayar dosyasında bulunmasını sağlar. Böylece uygulamanızı daha güvenle yeniden düzenleyebilirsiniz.

routes dosyasında belirtilen her bir controller için router `routes` paketinde bir ‘reverse controller’ oluşturur. Bu controller da aynı imza ile aynı action metotlarına sahiptir. Fakat bu action metotlar `play.api.mvc.Action` yerine `play.api.mvc.Call` döndürürler.

`play.api.mvc.Call` bir HTTP çağrısı tanımlayarak bir HTTP metodu ile bir URI belirtir.

Örneğin aşağıdaki gibi bir controller oluşturursanız:

@[reverse-controller](code/ScalaRouting.scala)

Ve `conf/routes` dosyasında şöyle eşlerseniz:

@[route](code/scalaguide.http.routing.reverse.routes)

`hello` action metoduna giden URL'i `controllers.routes.Application` reverse controller'ı kullanarak elde edebilirsiniz:

@[reverse-router](code/ScalaRouting.scala)

> **Sonraki:** [[Yanıtları işlemek | ScalaResults]]
