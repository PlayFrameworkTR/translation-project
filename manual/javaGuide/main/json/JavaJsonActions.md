<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# JSON işlemek ve sunmak

Java'da, Play nesneleri JSON'ye ve JSON'yi nesnelere çevirmek için [Jackson](http://jackson.codehaus.org/) JSON kütüphanesini kullanır. Play'in action'ları `JsonNode` türüyle çalışır ve framework `play.libs.Json` API'sinde dönüştürmeler için yardımcı metotlar sağlar.

## Java nesnelerini JSON'a dönüştürmek

Jackson, alan isimlerine, getter ve setter'lara bakarak Java nesnelerini kolayca JSON'ye dönüştürmenizi sağlar. Örneğin aşağıdaki basit Java nesnesini kullanacağız:

@[person-class](code/JavaJsonActions.java)

Nesnenin JSON temsilini ayrıştırabilir ve yeni bir `Person` sınıfı yaratabiliriz:

@[from-json](code/JavaJsonActions.java)

Benzer bir şekilde, `Person`nesnesini `JsonNode` şeklinde yazabiliriz:

@[to-json](code/JavaJsonActions.java)

## Bir JSON isteği işlemek

Bir JSON isteği, istek gövdesi olarak geçerli bir JSON yükü taşıyan bir HTTP isteğidir. Onun `Content-Type` başlığı `text/json`veya `application/json` MIME türünü belirtmek zorundadır.

Varsayılan olarak bir action, gövde JSON'sini (aslında bir Jackson `JsonNode'u olarak`) almak için kullanabileceğiniz **herhangi içerik** gövde ayrıştırıcısını kullanır:

@[json-request-as-anycontent](code/JavaJsonActions.java)

Tabii ki kendi `BodyParser`ımızı belirterek Play'den içerik gövdesini doğrudan JSON olarak ayrıştırmasını istemek daha iyi ve basittir: 

@[json-request-as-json](code/JavaJsonActions.java)

> **Not:** Bu şekilde, Content-type'ı application/json olup aslında JSON olmayan istekler için otomatik olarak bir 400 HTTP yanıt döndürülecektir.

Bunu **cURL** ile komut satırında test edebilirsiniz:

```bash
curl
  --header "Content-type: application/json"
  --request POST
  --data '{"name": "Guillaume"}'
  http://localhost:9000/sayHello
```

Bununla yanıt verir:

```http
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Content-Length: 15

Hello Guillaume
```

## Bir JSON yanıtı sunmak

Bir önceki örneğimizde bir JSON isteği işledik ama bir `text/plain` yanıtıyla cevap verdik. Haydi bunu yanıtı da geçerli bir JSON HTTP yanıtı olacak şekilde değiştirelim:

@[json-response](code/JavaJsonActions.java)

Şimdi bu şekilde yanıt verir:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"exampleField1":"foobar","exampleField2":"Hello world!"}
```

Ayrıca bir Java nesnesi döndürebilir ve onu Jackson kütüphanesiyle otomatik olarak JSON'ye dönüştürebilirsiniz:

@[json-response-dao](code/JavaJsonActions.java)

## İleri düzey kullanım

Play Jackson kullandığı için siz de kendi `ObjectMapper`ınızı kullanarak `JsonNode`ları yaratabilirsiniz. JSON dönüştürme işlemini daha fazla özelleştirmek için [jackson-databind için dokümantasyon](https://github.com/FasterXML/jackson-databind/blob/master/README.md) sayfasına bakabilirsiniz.

Play'in `Json`API'lerini (`toJson`/`fromJson`) özelleştirilmiş bir `ObjectMapper` ile kullanmak isterseniz, bunun gibi bir şeyi `GlobalSettings#onStart` metodunuza ekleyebilirsiniz:

@[custom-object-mapper](code/JavaJsonActions.java)

> **Next:** [[XML ile çalışmak | JavaXmlRequests]]
