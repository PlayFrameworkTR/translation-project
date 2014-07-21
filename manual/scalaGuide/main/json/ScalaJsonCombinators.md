<!--- Copyright (C) 2009-2014 Typesafe Inc. <http://www.typesafe.com> -->
# JSON Reads/Writes/Format Combinator'lar

[[JSON temelleri|ScalaJson]] sayfasında [`JsValue`](api/scala/index.html#play.api.libs.json.JsValue) yapıları ile diğer veri türleri arasında dönüşüm yapmak için kulanılan [`Reads`](api/scala/index.html#play.api.libs.json.Reads) ve [`Writes`](api/scala/index.html#play.api.libs.json.Writes) dönüştürücüleri tanıtmıştık. Bu sayfada ise bu dönüştürücülerin nasıl oluşturulacağını ve dönüşüm esnasında nasıl doğrulama yapılacağını çok daha ayrıntılı anlatacağız.

Bu sayfadaki örnekler aşağıdaki `JsValue` yapısını ve ona karşılık gelen modeli kullanacak:

@[sample-json](code/ScalaJsonCombinatorsSpec.scala)

@[sample-model](code/ScalaJsonCombinatorsSpec.scala)

## JsPath

[`JsPath`](api/scala/index.html#play.api.libs.json.JsPath), `Reads`/`Writes` oluşturmak için temel yapı taşıdır. `JsPath` verinin bir `JsValue` yapısı içindeki konumunu ifade eder. `JsPath` nesnesini (kök yol) kullanarak `JsValue` içinde gezinme sözdizimine benzer şekilde bir çocuk `JsPath` örneği tanımlayabilirsiniz:

@[jspath-define](code/ScalaJsonCombinatorsSpec.scala)

[`play.api.libs.json`](api/scala/index.html#play.api.libs.json.package) paketi `JsPath` için bir öteki ad tanımlar: `__` (çift altçizgi). Eğer isterseniz bunu şu şekilde kullanabilirsiniz:

@[jspath-define-alias](code/ScalaJsonCombinatorsSpec.scala)

## Reads
[`Reads`](api/scala/index.html#play.api.libs.json.Reads) dönüştürücüler bir `JsValue`'dan başka bir türe dönüşüm yapmak için kullanılırlar. Daha karmaşık `Reads` oluşturmak için birden fazla `Reads` bir araya getirebilirsiniz.

`Reads` oluşturabilmek için aşağıdaki import'lara ihtiyacınız olacak:

@[reads-imports](code/ScalaJsonCombinatorsSpec.scala)

### Yol Reads
`JsPath` bir `JsValue`'ya belirtilen yolda başka bir `Reads` uygulayan özel bir `Reads` oluşturmak için metotlara sahiptir:

- `JsPath.read[T](implicit r: Reads[T]): Reads[T]` - `JsValue`'ya belirtilen(bu) yolda örtük argüman `r`'yi uygulayan bir `Reads[T]` yaratır.
- `JsPath.readNullable[T](implicit r: Reads[T]): Reads[Option[T]]` - Bulunmayan ya da değeri null olan yollar için kullanın.

> Not: JSON kütüphanesi String, Int, Double, vb. temel türler için örtük `Reads` sağlar.

Tek bir yol `Reads` tanımı şöyledir:

@[reads-simple](code/ScalaJsonCombinatorsSpec.scala)

### Karmaşık Reads
Bağımsız yol `Reads` tanımlarını bir araya getirerek karmaşık modelleri dönüştürmek için daha karmaşık `Reads` oluşturabilirsiniz.

Daha anlaşılır olması için birleştirme özelliğini iki parçaya ayıracağız. Önce `Reads` nesnelerini `and` combinator kullanarak birleştirin:

@[reads-complex-builder](code/ScalaJsonCombinatorsSpec.scala)

Bu `FunctionalBuilder[Reads]#CanBuild2[Double, Double]` şeklinde bir tür oluşturacaktır. Bu ara nesne hakkında çok fazla düşünmenize gerek yoktur. Karmaşık `Reads` oluşturmak için kullanıldığını bilmeniz yeterli.

Daha sonra `CanBuildX`'in `apply` metodunu değerleri modelinize dönüştürecek bir fonksiyon ile çağırın. Bu bir karmaşık `Reads` döndürecektir. Eğer bir case class'ınız varsa doğrudan onun `apply` metodunu kullanabilirsiniz.

@[reads-complex-buildertoreads](code/ScalaJsonCombinatorsSpec.scala)

Aynı kod aşağıda tek bir ifade olarak verilmiştir:

@[reads-complex-statement](code/ScalaJsonCombinatorsSpec.scala)

### Reads ile doğrulama

[[JSON temelleri|ScalaJson]] sayfasında `JsValue.validate` metodunun bir `JsValue`'dan başka bir türe dönüşüm ve doğrulama için tercih edilen yol olduğundan bahsetmiştik. Temel desen aşağıda yer alıyor:

@[reads-validation-simple](code/ScalaJsonCombinatorsSpec.scala)

`Reads` için varsayılan doğrulama tür dönüşüm hataları gibi çok basit doğrulamalardan ibarettir. Fakat `Reads` doğrulama yardımcılarını kullanarak özel doğrulama kuralları tanımlayabilirsiniz. Çok kullanılanlardan bazıları şöyle:

- `Reads.email` - Bir String'in email biçiminde olduğunu doğrular.
- `Reads.minLength(nb)` - Bir String'in en az uzunluğunu doğrular.
- `Reads.min` - Bir sayının en az değerini doğrular.
- `Reads.max` - Bir sayının en çok değerini doğrular.
- `Reads[A] keepAnd Reads[B] => Reads[A]` - `Reads[A]` ve `Reads[B]`'yi deneyen fakat yalnızca `Reads[A]`'nın sonucunu saklayan operatör (Scala parser combinator'ları bilenler için `keepAnd == <~` ).
- `Reads[A] andKeep Reads[B] => Reads[B]` - `Reads[A]` ve `Reads[B]`'yi deneyen fakat yalnızca `Reads[B]`'nın sonucunu saklayan operatör (Scala parser combinator'ları bilenler için `andKeep == ~>` ).
- `Reads[A] or Reads[B] => Reads` - Mantıksal OR işlemi gerçekleştiren ve başarılı olan son Reads sonucunu saklayan operatör.

Doğrulama eklemek için yardımcı metotları `JsPath.read` metoduna argümanlar olarak uygulayın:

@[reads-validation-custom](code/ScalaJsonCombinatorsSpec.scala)

### Hepsini bir araya getirelim

Karmaşık `Reads` ve özel doğrulama kullanarak örnek modelimiz için etkin bir `Reads` seti tanımlayabilir ve uygulayabiliriz:

@[reads-model](code/ScalaJsonCombinatorsSpec.scala)

Karmaşık `Reads` nesneleri iç içe geçebilir. Bu örnekte `placeReads` belirli yollarda daha önceden tanımlanmış örtük `locationReads` ve `residentReads` tanımlarını kullanır.

## Writes
[`Writes`](api/scala/index.html#play.api.libs.json.Writes) dönüştürücüler bir türden `JsValue`'ya dönüşüm yapmak için kullanılırlar.

`Reads`'e benzer şekilde `JsPath` ve combinator'lar kullanarak karmaşık `Writes` oluşturabilirsiniz. Örneğimiz için `Writes` aşağıdaki gibidir:

@[writes-model](code/ScalaJsonCombinatorsSpec.scala)

`Writes` ve `Reads` arasında bazı farklılıklar vardır:

- Bağımsız yol `Writes`'lar `JsPath.write` metodunu kullanarak oluşturulurlar.
- `JsValue`'ya dönüşüm esnasında bir doğrulama yoktur. Bu yapınızı daha basit tutar ve doğrulama yardımcılarına ihtiyacınız yoktur.
- Ara tür (`and` combinator'ları tarafından yaratılan) `FunctionalBuilder#CanBuildX` karmaşık `T` tipini alarak her bir `Writes` yoluna karşılık gelen tuple'a dönüştüren bir fonksiyon alır. Bu `Reads` durumuna benzer olsa da bir case class'ın `unapply` metodu `Option` döndüğünden `unlift` ile birlikte kullanılmalıdır.

## Özyinelemeli Türler
Örneğimizin ele almadığı özel bir durum özyinelemeli türler için `Reads` ve `Writes` kullanımının nasıl olacağıdır. `JsPath` call-by-name parametreler alan `lazyRead` ve `lazyWrite` metodları sunar:

@[reads-writes-recursive](code/ScalaJsonCombinatorsSpec.scala)

## Format
[`Format[T]`](api/scala/index.html#play.api.libs.json.Format) yalnızca `Reads` ve `Writes` trait'lerinin karışımıdır ve bileşenlerinin yerine örtük dönüştürme için kullanılabilir.

### Reads ve Writes ile Format yaratmak
Aynı türün `Reads` ve `Writes`'larını kullanarak bir `Format` tanımlayabilirsiniz:

@[format-components](code/ScalaJsonCombinatorsSpec.scala)

### Combinator'lar kullanarak Format yaratmak
Eğer `Reads` ve `Writes`'ınız simetrik ise (gerçek uygulamalarda sık rastlanmayabilir) combinator'lar aracılığıyla doğrudan bir `Format` tanımlayabilirsiniz:

@[format-combinators](code/ScalaJsonCombinatorsSpec.scala)

> **Sonraki:** [[JSON Transformer'lar | ScalaJsonTransformers]]
