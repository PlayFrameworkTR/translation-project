<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Cross Site Request Forgery'ye Karşı Korunmak

Cross Site Request Forgery (CSRF), saldırganın kurbanın bilgisayarını, kurbanın oturumu ile bir istek yapacak şekilde kandırdığı bir güvenlik açığıdır. Oturum jetonu her istekle birlikte gönderildiğinden, bir saldırgan kurbanın tarayıcısını bir istek yapmaya zorlarsa, bu istek kullanıcının adına yapılmış gibi olur.

CSRF ile, saldırı taşıyıcılarının neler olduğu ve neler olmadığı ile ilgili bilgi edinmeniz tavsiye edilir. Biz buradan başlamayı tavsiye ediyoruz: [this information from OWASP](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29).

Kısaca, bir saldırgan bir kurbanın tarayıcısını aşağıdaki türlerde istekler yapmaya zorlayabilir:

* Tüm `GET` istekleri
* `application/x-www-form-urlencoded`, `multipart/form-data` veya `text/plain` gövde türlerinde `POST` istekleri

Bir saldırgan, tarayıcıyı:

* `PUT` veya `DELETE` gibi diğer istek metotlarını kullanmaya
* `application/json` ve benzeri farklı içerik türlerini göndermeye
* sunucunun tanımladığından farklı olarak yeni çerezler tanımlamaya
* tarayıcının isteklere eklediği normal başlıklara ilave olarak keyfi başlıklar eklemeye

zorlayamaz.

`GET` istekleri değiştirici bir anlam taşımadığından, bu en iyi yöntemi kullanan bır uygulama için bir tehlike yoktur. Yani, CSRF korumasına ihtiyaç duyan istekler, yukarıda belirtilen içerik türlerinden birine sahip `POST` istekleridir.

### Play'in CSRF koruması

Play, bir isteğin CSRF isteği olmadığını doğrulamak için birçok metot içerir. Birincil mekanizma bir CSRF jetonudur. Bu jeton hem gönderilen her formun sorgu dizesine konumlandırılır, hem de kullanıcının oturumuna eklenir. Daha sonra Play bu iki jetonun var olduğunu ve eşleştiğini doğrular.

Tarayıcıdan gelmeyen istekler (örneğin AJAX aracılığıyla yapılan istekler) için basit koruma sağlamak için, Play aynı zamanda şunu destekler:

* Eğer bir `X-Requested-With` başlığı mevcut ise, Play bu isteği güvenli olarak kabul eder. `X-Requested-With`, jQuery gibi birçok popüler Javascript kütüphanesi tarafından isteklere eklenir.
* Bir `nocheck` değerine veya geçerli bir CSRF jetonuna sahip `Csrf-Token` başlığı mevcut ise  Play bu isteği güvenli kabul eder.

## Global bir CSRF filtresi uygulamak

Play, bütün isteklere uygulanabilecek bir global CSRF filtresi sunar. Bu bir uygulamaya CSRF koruması uygulamanın en kolay yoludur. Bu global filtreyi etkinleştirmek için, Play filtreleri yardımcısı bağımlılığını projenizin `build.sbt` dosyasına ekleyin:

```scala
libraryDependencies += filters
```

Şimdi filtreyi `Global` nesnenize ekleyin:

@[global](code/javaguide/forms/csrf/Global.java)

### Mevcut jetonu almak

Formlara CSRF jetonları eklemeye yardımcı olmak için Play bazı şablon başlıklar sağlar. İlk olanı action URL'sinin sorgu dizesine jetonu ekler:

@[csrf-call](code/javaguide/forms/csrf.scala.html)

Bu, aşağıdaki gibi görünen bir form sunabilir:

```html
<form method="POST" action="/items?csrfToken=1234567890abcdef">
   ...
</form>
```

Eğer sorgu dizesinde jetonu bulundurmak sizin için istenmeyen bir şey ise, Play aynı zamanda CSRF jetonunu formda gizli bir alan olarak eklemek için bir yardımcı da sağlar:

@[csrf-input](code/javaguide/forms/csrf.scala.html)

Bu, aşağıdaki gibi görünen bir form sunabilir:

```html
<form method="POST" action="/items">
   <input type="hidden" name="csrfToken" value="1234567890abcdef"/>
   ...
</form>
```

### Oturuma bir CSRF jetonu eklemek

Bir CSRF jetonunun formlarda sunulduğundan ve istemciye geri gönderildiğinden emin olmak için, eğer bir jeton gelen istekte mebcut değilse global filtre HTML kabul eden her GET isteği için yeni bir jeton üretecektir.

## Eylem bazında CSRF filtrelemesi uygulamak

Bazen, örneğin bir uygulamanın kökenler arası (cross origin) form gönderimlerine izin vermek istemesi durumunda, global CSRF filtrelemesi uygun olmayabilir. Bazı oturum tabanlı olmayan standartlar, örneğin OpenID 2.0, siteler arası form gönderimini veya sunucudan sunucuya RPC iletişimlerinde form gönderimini şart koşar.

Böyle durumlarda, Play uygulamanızın eylemleriyle birleştirilebilecek iki eylem sunar.

İlk eylem `play.filters.csrf.RequireCSRFCheck` eylemidir ve kontrolü uygular. Bu eylem, tüm kimlik doğrulama gerektiren POST form gönderimi kabul eden eylemlere eklenmelidir:

@[csrf-check](code/javaguide/forms/JavaCsrf.java)

İkinci eylem, `play.filters.csrf.AddCSRFToken` eylemidir ve gelen istekte eğer halihazırda bir CSRF jetonu yoksa, yenisini üretir. Bu eylem form sunan tüm eylemlere eklenmelidir:

@[csrf-add-token](code/javaguide/forms/JavaCsrf.java)

## CSRF yapılandırma seçenekleri

AŞağıdaki seçenekler `application.conf` dosyasında yapılandırılabilir:

* `csrf.token.name` - Hem oturumda hem de istek gövdesi/sorgu dizesinde kullanıalcak jetonun adıdır. Varsayılanı `csrfToken`dir.
* `csrf.cookie.name` - Eğer yapılandırıldıysa, Play CSRF jetonlarını oturumda saklamak yerine verilen isimde bir çerezde saklayacaktır.
* `csrf.cookie.secure` - Eğer `csrf.cookie.name` ayarlandıysa, CSRF çerezi güvenli işaretine sahip olmalı/olmamalı işaretidir. Varsayılanı `session.secure`ünkiyle aynıdır.
* `csrf.body.bufferSize` - Jetonları gövdeden okuyabilmek için, Play ilk önce gövdeyi tamponlamalı ve potansiyel olarak ayrıştırmalıdır. Bu seçenek gövdeyi tamponlamak için en yüksek tampon boyutunu ayarlar. Varsayılanı 100k'dir.
* `csrf.sign.tokens` - Play imzalı CSRF jetonları kullanmalı/kullanmamalı ayarı. İmzalı CSRF jetonları bir jetonun her istek için rastgeleleştirilmiş olduğundan emin olur ve böylece BREACH tarzı saldırıları yok eder.
* `csrf.error.handler` - Hata işleyici. `play.filters.csrf.CSRFErrorHandler` veya `play.filters.csrf.CSRF.ErrorHandler`ı uygulamalıdır.

> **Sonraki:** [[JSON ile Çalışmak| JavaJsonActions]]

