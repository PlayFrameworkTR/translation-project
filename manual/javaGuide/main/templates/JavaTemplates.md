<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Şablon motoru

## Scala üzerine kurulmuş tür güvenli bir şablon motoru

Play; Scala tabanlı, tasarımı ASP.NET Razor'dan esinlenmiş, güçlü bir şablon motoru olan <a href="https://github.com/playframework/twirl">Twirl</a> ile birlikte gelmektedir. Twirl şu şekilde açıklanabilir:

- **kompakt, ifadeli ve akıcı:** Bir dosyada gereken karakter sayısı ve tuş basımlarını en aza indirir ve hızlı, akıcı bir kodlama iş akışı sağlar. Çoğu şablon sözdizimlerinin aksine, kodlamanızı HTML içindeki kod bloklarını açıkça belirterek bölmek zorunda kalmazsınız. Çözümleyici, bunu kodunuzdan çıkaracak kadar akıllıdır. Bu durum yazması temiz, hızlı, eğlenceli, gerçekten kompakt ve ifadeli bir söz dizimini mümkün kılar.
- **öğrenmesi kolay:** En az miktarda kavram ile hızlıca üretken olmanızı sağlar. Basit Scala ifadeleri ve mevcut HTML becerileriniz yeterlidir.
- **yeni bir dil değil:** Bilinçli bir şekilde yeni bir dil yaratmamayı seçtik. Bunun yerine, Scala geliştiricilerinin mevcut Scala becerilerini kullanabilmelerini ve müthiş bir HTML yapılandırma iş akışını sağlayacak bir şablon biçimlendirme söz dizimine ulaşmayı istedik.
- **her metin editöründe düzenlenebilir:** Hiçbir belirli aracı şart kılmaz, böylece eski metin editörlerinde bile üretken olmanızı mümkün kılar.

> **Not:** Şablon motoru ifade dili olarak Scala'yı kullansa da, bu Java geliştiricileri için bir sorun değildir. Şablon motorunun dili tıpkı Java'ymış gibi kullanabilirsiniz.
> Bir şablonun karmaşık mantık kodu yazmak için doğru bir yer olmadığını hatırlayın. Buraya karmaşık Scala kodu yazmak zorunda değilsiniz. Şablon motorunu çoğu zaman yalnızca model nesnelerinizdeki verilere erişmek için kullanacaksınız. Örneğin:
> 
> ```
> myUser.getProfile().getUsername()
> ```
> Parametre türleri son eklemeli bir söz dizimi ile belirtilir. Genel türler, Java söz dizimindeki tipik `<>` yerine `[]` sembolleri ile belirtilir. Örneğin, Java'daki `List<String>` için `List[String]` yazabilirsiniz.

Şablonlar derlenir, bu yüzden hataları web tarayıcınızda göreceksiniz:

[[images/templatesError.png]]

## Genel Bakış

Bir Play Scala şablonu, küçük Scala kod blokları taşıyan basit bir metin dosyasıdır. Şablonlar, HTML, XML ya da CSV gibi herhangi bir metin tabanlı format üretebilir.

Şablon motoru, HTML ile çalışmaya alışkın olanları rahat hissettirmek için tasarlanmıştır. Böylece ön yüz geliştiriciler şablonlarla kolayca çalışabilirler.

Şablonlar basit bir isimleme düzenini takip ederek standart Scala fonksiyonları olarak derlenirler. Örneğin bir `views/Application/index.scala.html` şablon dosyası oluşturursanız, `render()` metodu olan bir `views.html.Application.index` sınıfı oluşturulacaktır.

Örneğin, basit bir şablon:

```html
@(customer: Customer, orders: List[Order])
 
<h1>Welcome @customer.name!</h1>

<ul> 
@for(order <- orders) {
  <li>@order.getTitle()</li>
} 
</ul>
```

Şablondan üretilen sınıfı, tıpkı Java kodunda bir sınıftan bir metodu çağırır gibi çağırabilirsiniz:

```java
Content html = views.html.Application.index.render(customer, orders);
```

## Söz dizimi: sihirli ‘@’ karakteri

Scala şablonları `@` karakterine özel bir anlam yükler. Bu karakterle her karşılaşıldığında, dinamik bir ifadeye başlanıldığı anlaşılır. Kod bloğunu bilinçli bir şekilde kapatmak zorunda değilsiniz. Dinamik ifadenin bitişi kodunuzdan anlaşılacaktir:

```
Hello @customer.getName()!
       ^^^^^^^^^^^^^^^^^^
          Dinamik Kod
```

Şablon motoru kod bloğunun sonunu, kodunuzu analiz ederek otomatik olarak fark ettiği için, bu söz dizimi yalnızca basit ifadeleri destekler. Birden çok parçalı bir ifade eklemek isterseniz, dinamik ifadenizi parantezler ile sarın:

```
Hello @(customer.getFirstName() + customer.getLastName())!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
                          Dinamik Kod
```

Ayrıca, çok ifadeli bir kod bloğu yazmak için küme parantezleri de kullanabilirsiniz:

```
Hello @{val name = customer.getFirstName() + customer.getLastName(); name}!
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                                  Dinamik Kod
```

`@` özel bir karakter olduğu için, bu karakteri bazen kurtarmak isteyebilirsiniz. Bu `@@` şeklinde yapılabilir:

```
My email is bob@@example.com
```

## Şablon parametreleri

Bir şablon, fonksiyon gibidir, yani parametrelere ihtiyaç duyar. Bu parametreler şablon dosyasının en üstünde bildirilmelidir:

```scala
@(customer: models.Customer, orders: List[models.Order])
```

Ayrıca parametreler için varsayılan değerler kullanabilirsiniz:

```scala
@(title: String = "Home")
```

Hatta birçok parametre grubu bile:

```scala
@(title:String)(body: Html)
```

## Tekrarlama

Oldukça standart bir şekilde, `for` anahtar kelimesini kullanabilirsiniz:

```html
<ul>
@for(p <- products) {
  <li>@p.getName() ($@p.getPrice())</li>
} 
</ul>
```
> **Not:** İfadenin bir sonraki satırda devam ettiğini belirtmek için `{` işaretinin `for` kelimesi ile aynı satırda olduğundan emin olun.

## Koşul blokları

Koşul bloklarında da özel bir durum yoktur. Scala’nın standart `if` ifadesini kullanabilirsiniz:

```html
@if(items.isEmpty()) {
  <h1>Nothing to display</h1>
} else {
  <h1>@items.size() items!</h1>
}
```

## Tekrar kullanılabilir bloklar bildirmek

Tekrar kullanılabilir bloklar oluşturabilirsiniz:

```html
@display(product: models.Product) = {
  @product.getName() ($@product.getPrice())
}
 
<ul>
@for(product <- products) {
  @display(product)
} 
</ul>
```

Ayrıca tekrar kullanılabilir saf kod blokları da oluşturabilirsiniz:

```html
@title(text: String) = @{
  text.split(' ').map(_.capitalize).mkString(" ")
}
 
<h1>@title("hello world")</h1>
```

> **Not:** Buşekilde kod blokları tanımlamak bazen faydalı olabilir, ama şablonun karmaşık mantık yazmak için doğru yer olmadığını hatırlayın. Çoğu zaman bu tür kodları (isterseniz `views/` paketinde saklayabileceğiniz) bir Java sınıfına çıkarmak daha iyidir.

Geleneksel olarak **implicit** kelimesiyle başlayan bir isimle tanımlanan tekrarlanabilir bir kod bloğu, `implicit` olarak işaretlenecektir.

```
@implicitFieldConstructor = @{ MyFieldConstructor() }
```

## Tekrar kullanılabilir değerler bildirmek

Kapsama bağlı değerleri `defining` yardımcısı ile tanımlayabilirsiniz:

```html
@defining(user.getFirstName() + " " + user.getLastName()) { fullName =>
  <div>Hello @fullName</div>
}
```

## Import ifadeleri

Şablonunuza dışarıdan eklemek istediğiniz her şeyi, şablonunuzun ya da alt şablonunuzun başında belirtebilirsiniz:

```scala
@(customer: models.Customer, orders: List[models.Order])
 
@import utils._
 
...
```
Mutlak bir çözümleme belirtmek için, import ifadesinde **_root** ön ekini kullanın.

```scala
@import _root_.company.product.core._
```

Eğer tüm şablonlarda ihtiyaç duyulan ortak import ifadeleriniz varsa, bunları `build.sbt` dosyasında bildirebilirsiniz.

```scala
TwirlKeys.templateImports += "org.abc.backend._"
```

## Yorumlar

Şablonlarda sunucu tarafında blok yorumlar yazmak için `@* *@` kullanabilirsiniz:

```
@*********************
 * Bu bir yorumdur *
 *********************@   
```

Scala API dökümantasyonuna şablonunuzu tanıtmak için ilk satıra yorum koyabilirsiniz:

```
@*************************************
 * Home page.                        *
 *                                   *
 * @param msg The message to display *
 *************************************@
@(msg: String)

<h1>@msg</h1>
```

## Karakter kurtarma

Varsayılan olarak, dinamik kısımlar şablonun türüne göre (örneğin HTML, XML) kurtarılırlar. Ham bir içerik parçasını çıktıya vermek isterseniz şablon türüyle onu sarın.

Örneğin saf HTML çıktısı:

```html
<p>
  @Html(article.content)    
</p>
```

> **Next:** [[Common use cases | JavaTemplateUseCases]]
