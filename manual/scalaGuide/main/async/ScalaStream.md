<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# HTTP yanıtlarını stream etmek

## Standart yanıtlar ve Content-Length başlığı

HTTP 1.1'den itibaren çok sayıda HTTP istek ve yanıtına hizmet verebilecek tek bir bağlantıyı açık tutmak için sunucu uygun `Content-Length` HTTP başlığını yanıtla birlikte göndermek zorundadır.

Varsayılan olarak aşağıdaki gibi basit bir yanıt gönderirken `Content-Length` başlığını belirtmiyorsunuz:

```scala
def index = Action {
  Ok("Hello World")
}
```

Elbette göndermekte olduğunuz içerik tam olarak bilindiğinden Play sizin için içerik boyutunu hesaplayabilir ve uygun başlığı üretebilir.

> **Not:** Metin tabanlı yanıtlar için durum göründüğü kadar kolay değildir. `Content-Length` başlığı karakterleri baytlara dönüştüren karakter kodlamasına uygun olarak hesaplanmalıdır.

Aslında daha önce yanıt gövdesinin bir `play.api.libs.iteratee.Enumerator` ile belirtildiğini görmüştük:

```scala
def index = Action {
  Result(
    header = ResponseHeader(200),
    body = Enumerator("Hello World")
  )
}
```

Buna göre `Content-Length` başlığını düzgün olarak üretmek için Play tüm enumerator'ı tüketmeli ve içeriğini belleğe almalıdır.

## Yüksek miktarda veri göndermek

Basit Enumerator'lar için tüm içeriği belleğe almak sorun olmasa da büyük veri kümeleri için ne yapacağız? Diyelim ki istemciye büyük bir dosya göndereceğiz.

Önce nasıl dosya içeriğini veren bir `Enumerator[Array[Byte]]` üretebileceğimizi görelim:

```scala
val file = new java.io.File("/tmp/fileToServe.pdf")
val fileContent: Enumerator[Array[Byte]] = Enumerator.fromFile(file)
```

Kolay görünüyor, değil mi? Şimdi de bu enumerator'ı yanıt gövdesini belirtmek için kullanalım:

```scala
def index = Action {

  val file = new java.io.File("/tmp/fileToServe.pdf")
  val fileContent: Enumerator[Array[Byte]] = Enumerator.fromFile(file)

  Result(
    header = ResponseHeader(200),
    body = fileContent
  )
}
```

Aslında burada bir sorunumuz var. `Content-Length` başlığını belirtmediğimiz için Play bunu kendi üretmeye çalışacaktır ve bunun tek yolu tüm içeriği belleğe alıp yanıt boyutunu hesaplamaktır.

Bu durum tamamen belleğe yüklemek istemediğimiz büyük dosyalar için bir sorundur. Bundan sakınmak için `Content-Length` başlığını bizim belirtmemiz yeterlidir.

```scala
def index = Action {

  val file = new java.io.File("/tmp/fileToServe.pdf")
  val fileContent: Enumerator[Array[Byte]] = Enumerator.fromFile(file)

  Result(
    header = ResponseHeader(200, Map(CONTENT_LENGTH -> file.length.toString)),
    body = fileContent
  )
}
```

Bu şekilde Play veri parçaları oluştukça bu parçaları HTTP yanıtına yazacaktır.

## Dosyaları sunmak

Play elbette yerel bir dosya sunmak gibi sık yapılan bir iş için kullanımı kolay yardımcılar sağlar:

```scala
def index = Action {
  Ok.sendFile(new java.io.File("/tmp/fileToServe.pdf"))
}
```

Bu yardımcı, dosyadan `Content-Type` başlığını hesaplayacak ve istemcinin bu yanıtı nasıl ele alacağını belirmek için `Content-Disposition` başlığını da ekleyecektir. Varsayılan davranış HTTP yanıtına `Content-Disposition: attachment; filename=fileToServe.pdf` başlığını ekleyerek istemcinin bu dosyayı indirmesini istemektir.

Ayrıca kendi dosya adınızı belirtebilirsiniz:

```scala
def index = Action {
  Ok.sendFile(
    content = new java.io.File("/tmp/fileToServe.pdf"),
    fileName = _ => "termsOfService.pdf"
  )
}
```

Dosyayı aşağıdaki gibi `inline` sunabilirsiniz:

```scala
def index = Action {
  Ok.sendFile(
    content = new java.io.File("/tmp/fileToServe.pdf"),
    inline = true
  )
}
```

Bu durumda dosya adı belirtmenize gerek yoktur çünkü tarayıcı dosyayı indirmeye çalışmayacak, dosya içeriğini tarayıcı içinde gösterecektir. Bu yöntem metin, html ve resim gibi web tarayıcı tarafından dahili olarak desteklenen içerik türleri için kullanışlıdır.

## Parçalı(Chunked) yanıtlar

Şu ana dek içerik boyutunu içeriği stream etmeden önce hesaplayabildiğimiz için işler iyi gitti. Fakat dinamik olarak hesaplanan, boyutu belli olmayan içerik için ne yapacağız?

Bu türden yanıtlar için  **Chunked transfer encoding** kullanmak zorundayız.

> **Chunked transfer encoding** HTTP protokolünün 1.1 sürümünde yer alan ve sunucunun içeriği bir chunk(parça) dizisi halinde sunduğu bir veri transfer mekanizmasıdır. `Content-Length` başlığı yerine `Transfer-Encoding` başlığını kullanır. `Content-Length` başlığı kullanılmadığı için sunucu istemciye yanıtı iletmeye başlamadan önce içeriğin boyutunu bilmek zorunda değildir. Web sunucular dinamik olarak üretilen içeriğe sahip yanıtları içeriğin boyutunu bilmeden önce iletmeye başlayabilirler.
>
> Her bir chunk'ın boyutu chunk'tan hemen önce gönderildiğinden istemci o chunk için veriyi tam olarak aldığını anlayabilir. Veri transferi, uzunluğu sıfır olan son bir chunk ile sonlandırılır.
>
> <http://en.wikipedia.org/wiki/Chunked_transfer_encoding>

Bunun avantajı veriyi canlı olarak sunabiliriz, başka bir deyişle veri parçalarını hazır olur olmaz gönderebiliriz. Bu yöntemin kötü yanı ise web tarayıcı içerik boyutunu bilmediğinden düzgün bir ilerleme çubuğu gösteremez.

Diyelim ki birtakım veriler hesaplayan dinamik bir `InputStream` sağlayan bir servisimiz olsun. Önce bu stream için bir  `Enumerator` yaratmalıyız:

```scala
val data = getDataStream
val dataContent: Enumerator[Array[Byte]] = Enumerator.fromStream(data)
```

Artık veriyi `Ok.chunked` kullanarak stream edebiliriz:

```scala
def index = Action {
  val data = getDataStream
  val dataContent: Enumerator[Array[Byte]] = Enumerator.fromStream(data)

  Ok.chunked(dataContent)
}
```

Elbette chunked veriyi belirtmek için herhangi bir `Enumerator` kullanabiliriz:

```scala
def index = Action {
  Ok.chunked(
    Enumerator("kiki", "foo", "bar").andThen(Enumerator.eof)
  )
}
```

Sunucu tarafından gönderilen HTTP yanıtını incelersek:

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Transfer-Encoding: chunked

4
kiki
3
foo
3
bar
0

```

Üç chunk'ı takip eden ve yanıtı kapatan son bir boş chunk görüyoruz.

> **Sonraki:** [[Comet soketleri | ScalaComet]]
