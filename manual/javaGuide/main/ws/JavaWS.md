# Play WS API

Bazen Play uygulaması içinde diğer HTTP servisleri çağırmak isteriz. Play asenkron HTTP istekler yapmayı sağlayan, [WS library](api/java/play/libs/ws/package-summary.html) ile bunu destekler.

WS API'yi kullanırken iki önemli parça vardır: istek yapmak ve cevabı işlemek. Öncelikle nasıl GET ve POST HTTP istekleri yapılır onu tartışacağız, ardından WS'den gelen cevabı işlemeyi göstereceğiz. Sonunda, bazı ortak kullanım durumlarını tartışacağız.

## İstek Yapmak

WS kullanmak için, öncelikle `build.sbt` dosyasına `javaWs` eklenmelidir:

```scala
libraryDependencies ++= Seq(
  javaWs
)
```

Ardından, devamı belirtilmelidir:

@[ws-imports](code/javaguide/ws/JavaWS.java)

HTTP isteği oluşturmak için, `WS.url()` ile bağlantıyı belirterek başlayın.

@[ws-holder](code/javaguide/ws/JavaWS.java)

Bu başlık ayarları gibi çeşitli HTTP özelliklerini belirmek için kullanabileceğiniz [`WSRequestHolder`](api/java/play/libs/ws/WSRequestHolder.html) döndürecektir. Karışık istekler oluşturmak için birlikte zincir çağrılar yapabilirsiniz.

@[ws-complex-holder](code/javaguide/ws/JavaWS.java)

Kullanmak istediğiniz HTTP methoduna karşılık gelen bir methodu çağırarak sona erdirebilirsiniz. Bu zinciri sonlandıracaktır ve `WSRequestHolder`'da oluşturulan tüm seçenekleri kullanacaktır.

@[ws-get](code/javaguide/ws/JavaWS.java)

[`WSResponse`](api/java/play/libs/ws/WSResponse.html)'un sunucudan dönen veriyi içerdiği [`Promise<WSResponse>`](api/java/play/libs/F.Promise.html) döndürür.

### Doğrulamalı istekler

Eğer HTTP doğrulamasına ihticacınız varsa, kullanıcı adı, şifre ve [`WSAuthScheme`](api/java/play/libs/ws/WSAuthScheme.html) kullanarak kurucunun içinde belirtebilirsiniz. `WSAuthScheme` için seçenekler `BASIC`, `DIGEST`, `KERBEROS`, `NONE`, `NTLM`, ve `SPNEGO`'dur.

@[ws-auth](code/javaguide/ws/JavaWS.java)

### Takip yönlendirmeli istekler

HTTP çağrısının sonucu 302 veya 301 yönlendirmesi ise, tekrar cağrı yapmadan bu yönlendirmeyi takip edebilirsiniz.

@[ws-follow-redirects](code/javaguide/ws/JavaWS.java)

### Sorgu parametreli istekler

İstek için sorgu parametreleri belirleyebilirsiniz.

@[ws-query-parameter](code/javaguide/ws/JavaWS.java)

### Ek başlıklı istekler

@[ws-header](code/javaguide/ws/JavaWS.java)

Örnek olarak, belirli formatta yalın metin gönderiyorsanız, içerik tipini açıkca belirmek isteyebilirsiniz.

@[ws-header-content-type](code/javaguide/ws/JavaWS.java)

### Zaman aşımlı istek

İstek için zaman aşımı belirmek isterseniz, `setTimeout` kullanarak milisaniye olarak zaman aşımını belirtebilirsiniz.

@[ws-timeout](code/javaguide/ws/JavaWS.java)

### Form verisi göndermek

url-form-encoded veri göndermek için uygun bir başlık ve biçimlendirilmiş veri ayarlayabilirsiniz.

@[ws-post-form-data](code/javaguide/ws/JavaWS.java)

### JSON veri göndermek

JSON verisi göndermek için en kolay yol [[JSON library|JavaJsonActions]] kullanmaktır.

@[json-imports](code/javaguide/ws/JavaWS.java)

@[ws-post-json](code/javaguide/ws/JavaWS.java)

## Cevabı işlemek

[`WSResponse`](api/java/play/libs/ws/WSResponse.html) ile çalışmak `Promise` içinde planlanarak halledilir.

### Cevabı JSON olarak işlemek

`response.asJson()` çağırarak, cevabı `JsonNode` olarak işleyebilirsiniz.

Java
: @[ws-response-json](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-response-json](java8code/java8guide/ws/JavaWS.java)

### Cevabı XML olarak işlemek

Benzer bir şekilde, `response.asXml()` çağırarak cevabı XML olarak işleyebilirsiniz.

Java
: @[ws-response-xml](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-response-xml](java8code/java8guide/ws/JavaWS.java)

### Büyük cevapları işlemek

Büyük dosya veya döküman indirirken, `WS` cevap gövdesini `InputStream` olarak almanıza izin verir, böylece veriyi bütün içeriği bir kerede belleğe yüklemeden işleyebilirsiniz.

Java
: @[ws-response-input-stream](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-response-input-stream](java8code/java8guide/ws/JavaWS.java)

Bu örnek cevap gövdesini okuyup, arabelleklenen arttırımlarla dosyaya yazacaktır.

## Ortak Motifler ve Kullanım Durumları

### WS Çağrıları Zincirlemek

`flatMap` kullanarak zincir WS çağrıları yapabilirsiniz.

Java
: @[ws-composition](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-composition](java8code/java8guide/ws/JavaWS.java)

### İstisnaları Kurtarmak
Eğer istek içinde istisna durumları kurtarmak istiyorsanız, temcilci cevap için `recover` veya `recoverWith` kullanınız.

Java
: @[ws-recover](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-recover](java8code/java8guide/ws/JavaWS.java)

### Controller Kullanımı

`Promise<WSResponse>`'u `Promise<Result>`'a dönüştürerek [[Handling Asynchronous Results|JavaAsync]]'da tanımlanmış asenkron action motifi kullanarak Play sunucusu tarafından direkt idare edilmesini sağlayabilirsiniz.

Java
: @[ws-action](code/javaguide/ws/JavaWS.java)

Java 8
: @[ws-action](java8code/java8guide/ws/JavaWS.java)

## WSClient kullanarak

WSClient [AsyncHttpClient](https://github.com/AsyncHttpClient/async-http-client) temilinin kapsayıcısıdır. Çeşitli profillere sahip çoklu istemci tanımlamak veya taklit istek kullanımı için kullanışlıdır

Varsayılan istemci WS sınıfından çağırılabilir:

@[ws-client](code/javaguide/ws/JavaWS.java)

WS istemcisini doğrudan koddan tanımlayabilir ve bunu istek yapmak için kullanabilirsiniz.

@[ws-custom-client](code/javaguide/ws/JavaWS.java)

> NOTE: NingWSClient nesnesini temsil ediyorsanız, WS eklenti sistemini kullanmaz, ve bu yüzden `Application.onStop`'da otomatik olarak kapatılmayacaktır. Bunun yerine, işlemler tamamlanınca `client.close()` kullanarak elle kapatılmalıdır. Bu AsyncHttpClient tarafından kullanılan ThreadPoolExecutor'ın temelini serbest bırakacaktır. İstemci kapatırken oluşan bozukluk yetersiz bellek hatasına sebep olabilir. (özellikle uygulamanıcı geliştirici modunda sıklıkla kapatıp açıyorsanız).

`AsyncHttpClient`'in temelinede erişebilirsiniz.

@[ws-underlying-client](code/javaguide/ws/JavaWS.java)

Birkaç durumda bu önemlidir. WS'nin istemciye erişmesi gereken birkaç kısıtlaması vardır.

* `WS` direkt olarak çoklu parçalı form yüklemeyi desteklemez.Temel istemciyi ile [RequestBuilder.addBodyPart](http://asynchttpclient.github.io/async-http-client/apidocs/com/ning/http/client/RequestBuilder.html) kullanabilirsiniz.
* `WS` yayın yapan gövdeyi yüklemeyi desteklemez. Bu durumda, AsyncHttpClient tarafından sağlanan `FeedableBodyGenerator` kullanmalısınız.

## WS Yapılandırma

WS istemcisini yapılandırmak için aşağıdaki özellikleri `application.conf` içinde kullanınız

* `ws.followRedirects`: İstemciyi 301 ve 302 yönlendirmelerini takip etmek için yapılandırır *(ön tanımlı **true**)*.
* `ws.useProxyProperties`: Sistemin http vekalet(proxy) ayarlarını kullanmak (http.proxyHost, http.proxyPort) *(ön tanımlı **true**)*.
* `ws.useragent`: User-Agent başlık alanını yapılandırmak için
* `ws.compressionEnable`: Gzip/deflater sıkıştırmayı kullanmak için `true` olarak ayarlanmalıdır *(ön tanımlı **false**)*.

### Zaman Aşımları

WS'de 3 farklı zaman aşımı vardır. Zaman aşımına uğramak WS isteğinin kesilmesine yol açar.

* `ws.timeout.connection`: Uzak sunucuya bağlanırken beklenen azami süre *(ön tanımlı **120 saniye**)
* `ws.timeout.idle`: Beklemede kalınan azami süre (bağlantı kuruldu fakat daha fazla veri için bekleniyor) *(ön tanımlı **120 saniye**)*.
* `ws.timeout.request`: İstek almayı kabul etmek için toplam süre (uzak sunucu veri göndermeye devam etse bile istek kesilecektir) *(ön tanımlı **none**, akış bağlantılarına izin vermek için)*.

İstek zaman aşımı belirli bağlantılarda `setTimeout()` kullanılarak geçersiz kılınabilir. ("İstek Yapmak" bölümüne bakınız)

## WS'yi SSL ile Yapılandırma

WS'yi HTTP isteklerini SSL/TLS (HTTPS) üzerinden yapılandırmak için, lütfen [[WS SSL Yapılandırma|WsSSL]]'e bakınız.

> **Sonraki:** [[OpenID servislerine bağlanma|JavaOpenID]]
