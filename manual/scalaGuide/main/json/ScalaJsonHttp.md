<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# HTTP ile JSON

Play, HTTP API'nin JSON kütüphanesi ile birlikte kullanılması ile içerik türü JSON olan HTTP istek ve yanıtlarını destekler.

> Controller'lar, Action'lar ve yönlendirme hakkında ayrıntılı bilgi için [[HTTP Programlama | ScalaActions]] sayfasına bakınız.

Gerekli kavramları, varlıkları GET ile almak ve POST ile yaratmak için bir RESTful web servis geliştirerek göstereceğiz. Bu servis tüm veri için içerik türü olarak JSON kullanacak.

Servisimiz için kullanacağımız model aşağıda yer alıyor:

@[model](code/ScalaJsonHttpSpec.scala)

## Bir varlık listesini JSON biçiminde sunmak

Controller'ımıza gerekli import'ları ekleyerek başlayalım.

@[controller](code/ScalaJsonHttpSpec.scala)

`Action`'ı yazmadan evvel modelimizden `JsValue` görünümüne dönüşüm yapacak bir bağlantıya ihtiyacımız var. Bu bağlantıyı örtük bir `Writes[Place]` tanımlayarak sağlıyoruz.

@[serve-json-implicits](code/ScalaJsonHttpSpec.scala)

Sonra `Action`'ı yazıyoruz:

@[serve-json](code/ScalaJsonHttpSpec.scala)

`Action` önce bir `Place` listesi elde ediyor, daha sonra bunları örtük `Writes[Place]` aracılığıyla `Json.toJson` kullanarak bir `JsValue`'ya dönüştürüyor ve bu değeri yanıt gövdesi olarak döndürüyor. Play, yanıtı JSON olarak tanıyacak ve yanıt için gerekli `Content-Type` başlığı ile gövde değerini setleyecektir.

Geriye yalnızca bu `Action` için `conf/routes` dosyasına bir yönlendirme eklemek kalıyor:

```
GET   /places               controllers.Application.listPlaces
```

Bu action bir tarayıcı ya da HTTP aracı kullanarak test edilebilir. Bizim örneğimiz UNIX komut satırı aracı [cURL](http://curl.haxx.se/) kullanıyor.

```
curl --include http://localhost:9000/places
```

Yanıt:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 141

[{"name":"Sandleford","location":{"lat":51.377797,"long":-1.318965}},{"name":"Watership Down","location":{"lat":51.235685,"long":-1.309197}}]
```

## JSON ile yeni bir varlık oluşturmak

Bu `Action` için bir `JsValue`'dan bizim modelimize dönüşüm yapacak örtük bir `Reads[Place]` tanımlamamız gerekiyor.

@[handle-json-implicits](code/ScalaJsonHttpSpec.scala)

Daha sonra `Action`'ı tanımlıyoruz.

@[handle-json-bodyparser](code/ScalaJsonHttpSpec.scala)

Bu `Action` bir öncekinden daha karmaşık. Dikkate alınması gereken bazı noktalar şöyle:

- Bu `Action`, `Content-Type` başlığı `text/json` ya da `application/json` olan ve gövdesi oluşturulacak varlığın JSON görünümünü içeren bir istek bekliyor.
- İsteği ayrıştırmak ve `request.body`'yi bir `JsValue` olarak vermek için JSON'a özel bir `BodyParser` kullanıyor.
- Dönüşüm için örtük `Reads[Place]` kullanan `validate` metodundan faydalanıyoruz.
- Doğrulama sonucunu işlemek için hata ve başarı durumlarıyla `fold` kullanıyoruz. Bu desen form gönderiminde de kullanıldığından tanıdık gelebilir.
- `Action` aynı zamanda JSON yanıtları gönderiyor.

Son olarak `conf/routes` dosyasına bir yönlendirme ekliyoruz:

```
POST  /places               controllers.Application.savePlace
```

Bu action'ı geçerli ve geçersiz isteklerle test ederek başarı ve hata durumlarımızı doğrulayacağız.

Geçerli veri ile test:

```
curl --include
  --request POST
  --header "Content-type: application/json"
  --data '{"name":"Nuthanger Farm","location":{"lat" : 51.244031,"long" : -1.263224}}'
  http://localhost:9000/places
```

Yanıt:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 57

{"status":"OK","message":"Place 'Nuthanger Farm' saved."}
```

"name" alanı olmayan geçersiz veri ile test:

```
curl --include
  --request POST
  --header "Content-type: application/json"
  --data '{"location":{"lat" : 51.244031,"long" : -1.263224}}'
  http://localhost:9000/places
```
Yanıt:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Content-Length: 79

{"status":"KO","message":{"obj.name":[{"msg":"error.path.missing","args":[]}]}}
```
"lat" için hatalı veri türüne sahip geçersiz veri ile test:

```
curl --include
  --request POST
  --header "Content-type: application/json"
  --data '{"name":"Nuthanger Farm","location":{"lat" : "xxx","long" : -1.263224}}'
  http://localhost:9000/places
```
Yanıt:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Content-Length: 92

{"status":"KO","message":{"obj.location.lat":[{"msg":"error.expected.jsnumber","args":[]}]}}
```

## Özet
Play, JSON ile REST geliştirmeyi desteklemek üzere tasarlanmıştır. Dolayısıyla bu servisleri geliştirmenin basit olduğunu umuyoruz. İşin çoğu bir sonraki bölümde ayrıntılı olarak açıklanan modeliniz için `Reads` ve `Writes` yazmaktan ibaret.

> **Sonraki:** [[JSON Reads/Writes/Formats Combinator'lar | ScalaJsonCombinators]]
