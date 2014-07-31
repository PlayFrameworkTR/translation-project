<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Şablon motoruna özel bir biçim için destek kazandırmak

Yerleşik şablon motoru yaygın şablon biçimlerini (HTML, XML vb.) destekler; ama ihtiyaç duyarsanız kendi biçimleriniz için kolayca destek kazandırabilirsiniz. Bu sayfa özel bir biçimi desteklemek için takip edilmesi gereken adımları özetler.

## Şablonlama işlemine genel bakış

Şablon motoru bir şablonun statik ve dinamik içerik kısımlarını birleştirerek sonucunu üretir. Örneğin aşağıdaki şablonu inceleyelim:

```
foo @bar baz
```

Bu şablon bir dinamik parça (`bar`) etrafında iki statik parçadan (`foo ` and ` baz`) oluşur. Şablon motoru bu parçaları bir araya getirerek sonucunu üretir. Aslında, cross-site scripting saldırılarını önlemek için, `bar`ın değeri sonucun geri kalanına eklenmeden önce kurtarılabilir. Bu kurtarma işlemi biçime özeldir: Örneğin HTML'de “<” işaretini “&amp;lt;” ile değiştirmek istersiniz.

Şablon motoru bir şablon dosyasına hangi biçimin karşılık geldiğini nasıl anlar? O dosyanın uzantısına bakarak: Örneğin `.scala.html` ile bitiyorsa bu dosyaya HTML biçimini ilişkilendirir.

Özetle, kendi şablon biçiminizi oluşturmak için aşağıdaki adımları yerine getirmelisiniz:

* Biçim için metin bütünleştirme sürecini sağlamak ;
* Biçime bir dosya uzantısı ilişkilendirmek.

## Bir biçimi oluşturmak

Sırayla, statik ve dinamik şablon parçalarını bir araya getirmede kullanılacak `A raw(String text)` ve `A escape(String text)` metodlarını içeren `play.twirl.api.Format<A>` arayüzünü uygulayın (implement edin).

Biçimin tür parametresi `A` şablon sunumunun sonuç türünü tanımlar, örneğin bir HTML şablonu için `Html`. Bu tür parçaların nasıl bir araya getirileceğini tanımlayan `play.twirl.api.Appendable<A>` trait'inin bir alt türü olmak zorundadır.

Kolaylık için, Play, `play.twirl.api.Appendable<A>`'ı uygulayan, `StringBuilder` kullanarak kendi sonucunu üreten ve `play.twirl.api.Content` arayüzünü uygulayarak Play'in onu nasıl bir HTTP yanıt gövdesi olarak serialize edeceğini tanımlayan `play.twirl.api.BufferedContent<A>` soyut sınıfını sağlar.

Kısaca, iki sınıf yazmanız gerekir: sonucu tanımlayan (`play.twirl.api.Appendable<A>`ı uygulayan) ve metin bir araya getirme işlemini tanımlayan (`play.twirl.api.Format<A>`ı uygulayan). Örneğin, HTML biçimi şöyle tanımlanmıştır:

```java
public class Html extends BufferedContent<Html> {
  public Html(StringBuilder buffer) {
    super(buffer);
  }
  String contentType() {
    return "text/html";
  }
}

public class HtmlFormat implements Format<Html> {
  Html raw(String text: String) { … }
  Html escape(String text) { … }
  public static final HtmlFormat instance = new HtmlFormat(); // Yapı işlemi biçime statik bir referansa ihtiyaç duyar (sonraki bölüme bakınız).
}
```

## Biçime bir dosya uzantısı ilişkilendirmek

Şablonlar, yapı işlemi tarafından tüm uygulama kaynağını derlemeden hemen önce `.scala` dosyalarına derlenirler. `TwirlKeys.templateFormats` anahtarı, dosya uzantıları ve şablon biçimleri arasındaki ilişkiyi tanımlayan `Map[String, String]` türünde bir sbt ayarıdır. Örneğin Play'ın kendi HTML biçim uygulamanızı kullanmasını isterseniz, `.scala.html` dosyalarını sizin özel `my.HtmlFormat` biçiminiz ile ilişkilendirebilmek için aşağıdaki kodu yapı dosyanıza yazmanız gerekir:

```scala
TwirlKeys.templateFormats += ("html" -> "my.HtmlFormat.instance")
```
Okun sağ tarafının `play.twirl.api.Format<?>` türünde statik bir değerin tam nitelikli adını içermesi gerektiğini unutmayın.

> **Sonraki:** [[HTTP form gönderimi ve doğrulaması | JavaForms]]
