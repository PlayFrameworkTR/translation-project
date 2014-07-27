<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Gönderilen formları karşılamak

## Bir form tanımlamak

`play.data` paketi HTTP form verisi göndermek ve doğrulamak için birçok yardımcı içerir. Form gönderimini karşılamak için en kolay yol mevcut bir sınıfı sarmalayan bir `play.data.Form` tanımlamaktır:

@[user](code/javaguide/forms/u1/User.java)

@[create](code/javaguide/forms/JavaForms.java)

> **Not:** Altında yatan bağlamalar [Spring data binder](http://static.springsource.org/spring/docs/3.0.x/reference/validation.html) kullanılarak yapılmıştır.

Bu form `HashMap<String,String>` verisinden bir `User` sonuç değeri üretebilir:

@[bind](code/javaguide/forms/JavaForms.java)

Kapsam içinde mevcut bir isteğiniz varsa, istek içeriğini doğrudan bir nesneye bağlayabilirsiniz:

@[bind-from-request](code/javaguide/forms/JavaForms.java)

## Kısıtlamalar tanımlamak

JSR-303 (Bean Validation) annotation'ları kullanarak bağlama sürecinde kontrol edilecek ilave kısıtlamalar tanımlayabilirsiniz:

@[user](code/javaguide/forms/u2/User.java)

> **İpucu:** `play.data.validation.Constraints` sınıfı halihazırda birçok doğrulama annotation'ları içerir.

Ayrıca üst nesnenize bir `validate` metodu ekleyerek özel doğrulama da tanımlayabilirsiniz:

@[user](code/javaguide/forms/u3/User.java)

Yukarıdaki örneğte döndürülen mesaj global bir hata olacak.

`validate`-metodu şu türleri döndürebilir: `String`, `List<ValidationError>` veya `Map<String,List<ValidationError>>`.

`validate`metodu annotation tabanlı kısıtlamalar denetlendikten sonra, ancak ve ancak onlar geçerse çağrılır. Eğer doğrulama geçerse `null` döndürmek zorundasınız. Herhangi bir `null`olmayan değer döndürmeye (boş string veya boş list) başarısız bir doğrulama olarak davranılır.

`List<ValidationError>` alanlar için ilave doğrulamalara sahipseniz yararlı olabilir. Örneğin:

@[list-validate](code/javaguide/forms/JavaForms.java)

`Map<String,List<ValidationError>>` kullanmak, map'in anahtarlarını hata kodları olacak şekilde, yukarıdaki örnekte `email`da olduğu gibi `List<ValidationError>`a benzerdir.

## Bağlama hatalarını idare etmek

Tabii ki eğer kısıtlamalar tanımlarsanız, bağlama hatalarını idare edebilmeniz gerekir.

@[handle-errors](code/javaguide/forms/JavaForms.java)

Genellikle, yukarda gösterildiği gibi, form basitçe bir şablona geçirilir. Global hatalar aşağıdaki şekilde sunulabilir:

@[global-errors](code/javaguide/forms/view.scala.html)

Belirli bir alan için hatalar aşağıdaki biçimde sunulabilir:

@[field-errors](code/javaguide/forms/view.scala.html)


## Varsayılan değerler ile bir formu doldurmak

Bazen, çoğunlukla düzenleme için, bir formu mevcut değerler ile doldurmak isteyeceksiniz:

@[fill](code/javaguide/forms/JavaForms.java)

>**İpucu:** `Form`nesneleri immutable'dır. `bind()` ya da `fill()` gibi metodlara çağrılar yeni veriyle doldurulmuş yeni bir form döndürür.

## Bir modelle ilişkisi olmayan bir form doldurmak

Bir `Model` ile ilişkisi olmayan bir HTML formundan veri almak için `DynamicForm` kullanabilirsiniz:

@[dynamic](code/javaguide/forms/JavaForms.java)

## Özel bir DataBinder kaydetmek

Özel bir nesneden bir form alan string'ine ve tam tersine bir eşleşme tanımlamak isterseniz bu nesne için yeni bir `Formatter` tanımlamalısınız. JodaTime'ın `LocalTime'ı gibi bir nesne için bu işlem aşağıdaki gibi görünebilir:

@[register-formatter](code/javaguide/forms/JavaForms.java)

Bağlama başarısız olduğunda bir hata anahtarları dizisi yaratılır ve messages dosyalarında ilk tanımlanan kullanılır. Bu dizi genellikle şunları içerir:

    ["error.invalid.<fieldName>", "error.invalid.<type>", "error.invalid"]

Hata anahtarları [Spring DefaultMessageCodesResolver](http://static.springsource.org/spring/docs/3.0.7.RELEASE/javadoc-api/org/springframework/validation/DefaultMessageCodesResolver.html) tarafından yaratılır. Kök "typeMismatch" "error.invalid" ile değiştirilir.

> **Sonraki:** [[Form şablonu başlıklarını kullanmak | JavaFormHelpers]]
