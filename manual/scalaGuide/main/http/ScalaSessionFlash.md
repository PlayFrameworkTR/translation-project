<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Session ve Flash kapsamları

## Play'de farklı olan

Eğer birden fazla HTTP isteği arasında veriyi korumak isterseniz Session ya da Flash kapsamlarında saklayabilirsiniz. Session kapsamında saklanan veri tüm kullanıcı oturumu boyunca korunur. Flash kapsamında saklanan veriye ise yalnızca bir sonraki istek tarafından erişilebilir.

Session ve Flash verisinin sunucu tarafında saklanmadığını, aksine çerez mekanizması kullanılarak her bir HTTP isteğine eklendiğini anlamak önemlidir. Bu sebeple veri boyutu sınırlıdır (en fazla 4KB) ve yalnızca string değerler saklayabilirsiniz. Çerezin varsayılan adı `PLAY_SESSION`'dır. application.conf içindeki `session.cookieName` anahtarı ile bu değiştirilebilir.

> Eğer çerezin adı değiştirilirse önceki çerez [[Çerezleri setlemek ya da silmek|ScalaResults]] kısmında anlatılan yöntemle silinebilir.

Elbette çerez değerleri gizli bir anahtar ile imzalanır. Böylece istemci çerez verisini değiştiremez (aksi halde geçersiz olur).

Play Session bir önbellek gibi kullanılmak üzere tasarlanmamıştır. Belirli bir oturuma ait veriyi önbelleğe alma ihtiyacı duyarsanız Play'in dahili önbellek mekanizmasını kullanabilir ve veriyi kullanıcı ile ilişkilendirmek için kullanıcı Session'ında benzersiz bir ID saklayabilirsiniz.

> Teknik olarak Session için bir zaman aşımı yoktur. Kullanıcı web tarayıcıyı kapattığında süresi dolar. Eğer zaman aşımı özelliğine ihtiyaç duyarsanız kullanıcı Session'ında bir zaman damgası saklayın ve bu bilgiyi ihtiyacınıza göre kullanın (ör. en çok oturum süresi, en çok eylemsizlik süresi, vb.)

## Session'da veri saklamak

Session yalnızca bir çerez olduğundan aslında bir HTTP başlığından ibarettir. Diğer yanıtların özelliklerini değiştirdiğiniz gibi session verisini de aynı yolla değiştirebilirsiniz.

@[store-session](code/ScalaSessionFlash.scala)


Bunun tüm oturum verisini değiştireceğine dikkat edin. Mevcut oturuma yeni bir eleman eklemek isterseniz gelen oturuma bir eleman ekleyin ve bunu yeni oturum olarak belirtin:

@[add-session](code/ScalaSessionFlash.scala)


Aynı şekilde gelen oturumdan herhangi bir değeri çıkartabilirsiniz:

@[remove-session](code/ScalaSessionFlash.scala)

## Bir Session değerini okumak

HTTP isteğinden gelen oturumu elde edebilirsiniz:

@[index-retrieve-incoming-session](code/ScalaSessionFlash.scala)

## Tüm oturumu verisini silmek

Tüm oturumu silen özel bir işlem bulunmaktadır:

@[discarding-session](code/ScalaSessionFlash.scala)

## Flash kapsamı

Flash kapsamı tıpkı Session gibi çalışır fakat temel iki fark içerir:

- veri yalnız bir istek için saklanır
- Flash çerezi imzalanmaz. Bu sebeple kullanıcı çerezi değiştirebilir.

> **Önemli:** Flash kapsamı yalnızca Ajax olmayan basit uygulamalarda başarı/hata durumlarını taşımak için kullanılmalıdır. Veri yalnızca bir sonraki istek için saklandığından ve karmaşık bir Web uygulamasında istek sırası için bir garanti olmadığından Flash kapsamı yarış durumlarına açıktır.

Aşağıda Flash kapsamını kullanan birkaç örnek yer alıyor:

@[using-flash](code/ScalaSessionFlash.scala)



Görünüm katmanında Flash kapsamına erişmek için örtük bir Flash değeri ekleyin:
```
@()(implicit flash: Flash)
...
@flash.get("success").getOrElse("Welcome!")
...
```

Eğer '_could not find implicit value for parameter flash: play.api.mvc.Flash_' şeklinde bir hata alırsanız Action bir request import etmemiş demektir. Aşağıdaki gibi bir "implicit request=>" ekleyin:

@[find-noflash](code/ScalaSessionFlash.scala)

> **Sonraki:** [[Gövde ayrıştırıcılar | ScalaBodyParsers]]
