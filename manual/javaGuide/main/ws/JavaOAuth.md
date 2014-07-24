# OAuth

[OAuth](http://oauth.net/) korunan bir veri yayınlamak ve bununla etkileşimde bulunmak için en basit yoldur. Ayrıca insanların size izin vermesi için en güvenli yoldur. Örnek olarak, [Twitter](https://dev.twitter.com/docs/auth/using-oauth)'daki kullanıcılarınız verilerine erişmek için kullanılabilir.

OAuth'un birbirinden çok farklı 2 versiyonu vardır: [OAuth 1.0](http://tools.ietf.org/html/rfc5849) ve [OAuth 2.0](http://oauth.net/2/). Versiyon 2, kütüphane veya yardımcı desteği olmadan entegre edebilirsiniz, bu yüzden Play sadece OAuth 1.0 desteği sağlamaktadır.

## Kullanım

OAuth'u kullanmak için öncelikle `build.sbt` dosyanıza `javaWs`'yı eklemelisiniz:

```scala
libraryDependencies ++= Seq(
  javaWs
)
```

## Gerekli Bilgi

OAuth uygulamanızı servis sağlayıcıya kayıt ettirmeniz gerekmektedir. Sağladığınız geri çağırım bağlantınızı kontrol ettiğinizden emin olunuz, çünkü eşleşme olmaması durumunda servis sağlayıcı çağrılarınızı reddebilir. Yerel olarak çalışırken, `/etc/hosts` dosyasını, yerel makinanızda sahte alan adı açmak için kullanabilirsiniz.

Servis sağlayıcı size bunları verecektir:

* Application ID (Uygulama Kimliği)
* Secret key (Gizli Anahtar)
* Request Token URL (İstek Jetonu Bağlantısı)
* Access Token URL (Erişim Jetonu Bağlantısı)
* Authorize URL (Yetkilendirme Bağlantısı)

## Kimlik Doğrulama Akışı

Akışın çoğu Play kütüphanesi tarafından yapılacaktır.

1. Sunucudan istek jetonu alınız (sunucu-sunucu çağrılarında)
2. Kullanıcıyı servis sağlayıcıya yönlendiriniz, burada kullanıcı kendi bilgilerini kullanmanız için izin verecektir.
3. Servis sağlayıcı kullanıcıyı geri yönlendirip, size doğrulayıcı verecektir.
4. Bu doğrulayıcı ile istek jetonunu erişim jetonu ile takas ediniz. (sunucu-sunucu çağrısı)

Şimdi erişim jetonu herhangi bir çağrıda, korunan veriye erişim için kullanılabilir.

## Örnek

`conf/routes`:

@[ws-oauth-routes](code/javaguide.ws.routes)

controller:

@[ws-oauth-controller](code/javaguide/ws/controllers/Twitter.java)

> **Sonraki:** [[Akka ile Entegrasyon| JavaAkka]]
