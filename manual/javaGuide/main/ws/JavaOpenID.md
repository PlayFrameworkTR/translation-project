<!--- Copyright (C) 2009-2014 Typesafe Inc. <http://www.typesafe.com> -->

# Play'de OpenID Desteği

OpenId kullanıcıların tek bir hesap ile birkaç servise erişmesini sağlayan bir protokolüdür. Web geliştiricisi olarak, OpenID'yi kullanıcılarınız önceden varolan hesaplarını kullanarak, örneğin [Google account](https://developers.google.com/accounts/docs/OpenID), giriş yapmalarını sağlayabilirsiniz. Kurumsal kurumlarda, OpenID'yi şirketinizin SSO sunucusuna bağlanmak için kullanabilirsiniz.

## Özetle OpenID Akışı

1. Kullanıcı size OpenID'sini verir. (Bağlantı).
2. Sunucunuz bağlantının arkasındaki içeriği inceler ve size, kullanıcıyı yönlendirmek için gerekli olan bağlantıyı üretir.
3. Kullanıcı OpenID sağlayıcısındaki izinleri onaylar ve sunucunuza geri yönlendirilir.
4. Sunucunuz yönlendirmeden gelen bilgiyi alır ve OpenID sağlayıcısından bilginin doğruluğunu kontrol eder.

Eğer tüm kullanıcılar aynı OpenID sağlayıcısını kullanıyorsa 1. adım atlanabilir (örnek olarak tamamen Google hesaplarına güvenmeye karar verirseniz).

## Kullanım

OpenID'u kullanmak için öncelikle `build.sbt` dosyanıza `javaWs`'yı eklemelisiniz:

```scala
libraryDependencies ++= Seq(
  javaWs
)
```

## Play'de OpenID

OpenID API'si iki önemli fonksiyona sahiptir:

* `OpenID.redirectURL` kullanıcıyı yönlendirmeniz gereken bağlantıyı belirler. Kullanıcının OpenID sayfasını asenkron olarak çekmeyi içerir, bu sebeple `Promise<String>` döndürür. Eğer OpenID geçersizse, dönen `Promise` `Thrown` olacaktır.
* `OpenID.verifiedId` kullanıcının onaylanmış OpenID'si de olmak üzere, bilgilerini almak için güncel isteği inceler. Bilginin doğruluğunu kontrol etmek için asenkron olarak OpenID sunusunu çağırır, bu sebeple `Promise<UserInfo>` döndürür. Eğer bilgi doğru değilse veya sunucu kontrolü hatalıysa (örnek olarak yönlendirme bağlantısı sahte ise), dönen `Promise` `Thrown` olacaktır.

Eğer `Promise` başarısız olursa, kullanıcıyı giriş sayfasına geri yönlendiren veya `BadRequest` döndüren bir geri çağırım tanımlayabilirsiniz.

### Örnek

`conf/routes`:

@[ws-openid-routes](code/javaguide.ws.routes)

controller:

Java
: @[ws-openid-controller](code/javaguide/ws/controllers/OpenIDController.java)

Java 8
: @[ws-openid-controller](java8code/java8guide/ws/controllers/OpenIDController.java)


## Genişletilmiş Özellikler

Kullanıcının OpenID'si size onun kimliğini verir. Bu protokol ayrıca [genişletilmiş özellikleri](http://openid.net/specs/openid-attribute-exchange-1_0.html) destekler. Örneğin e-mail adresi, adı veya soyadı gibi.

*İsteğe bağlı* ve/veya *gerekli* özellikleri, OpenID sunucusundan isteyebilirsiniz. Gerekli özellikleri istemek demek, kullanıcı bu bilgileri sağlamadan sizin servisinize giriş yapamaz anlamına gelir.

Genişletilmiş özellikler yönlendirme bağlantısında talep edilir.

@[ws-openid-extended-attributes](code/javaguide/ws/controllers/OpenIDController.java)

Özellikler OpenID sunucusu `UserInfo` sağladıktan sonra uygun olacaktır.

> **Sonraki:** [[OAuth tarafından korunan kaynaklara erişim|JavaOAuth]]
