<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Form şablon yardımcıları

Play form alanlarını HTML şablonlarında sunmanıza yardımcı olmak için birçok yardımcı sağlar.

## Bir `<form>` etiketi yaratmak

İlk yardımcı `<form>` etiketini yaratır. Bu, otomatik olarak `action` ve `method` etiket parametrelelerini, ilettiğiniz ters rotaya göre ayarlayan basit bir yardımcıdır:

@[form](code/javaguide/forms/helpers.scala.html)

Ayrıca üretilecek HTML'ye eklenecek bir ilave parametre dizisi de geçirebilirsiniz:

@[form-with-id](code/javaguide/forms/helpers.scala.html)

## Bir `<input>` elementi sunmak

`views.html.helper` paketinde birçok girdi yardımcısı vardır. Onları bir form alanı ile beslersiniz ve onlar uyan HTML form kontrolünü, doldurulmuş değer, kısıtlama ve hatalarla gösterirler:

@[full-form](code/javaguide/forms/fullform.scala.html)

As for the `form` helper, you can specify an extra set of parameters that will be added to the generated HTML:

@[extra-params](code/javaguide/forms/helpers.scala.html)

> **Not:** İsimleri `_` ile başlayanlar hariç, bütün ilave parametreler üretilen HTML'ye eklenecektir. Alt çizgi ile başlayan argümanlar (ileride göreceğimiz) alan constructor argümanları için ayrılmıştır.

## HTML girdi üretimini kendi kendinize halletmek

Aynı zamanda istenen HTML sonucunu kodlamanıza izin veren daha genel bir `input` yardımcısı da vardır:

@[generic-input](code/javaguide/forms/helpers.scala.html)

## Alan constructor'ları

Sunulan bir alan sadece bir `<input>` etiketi içermekle kalmayıp, aynı zamanda `<label>` ve CSS çatınızda field'ı süslemek için kullanılan başka birtakım etiketleri  de içerebilir.

Bütün girdi yardımcıları bu kısmı üstlenen bir `FieldConstructor` alırlar. Varsayılanı (yani kapsamda başka bir field constructor'ı yoksa kullanılan) bunun gibi bir HTML üretir:

```
<dl class="error" id="email_field">
    <dt><label for="email">Email:</label></dt>
    <dd><input type="text" name="email" id="email" value=""></dd>
    <dd class="error">This field is required!</dd>
    <dd class="error">Another error</dd>
    <dd class="info">Required</dd>
    <dd class="info">Another constraint</dd>
</dl>
```

Bu varsayılan alan constructor'ı input yardımcı argümanlarında geçirebileceğiniz ilave seçenekleri destekler:

```
'_label -> "Custom label"
'_id -> "idForTheTopDlElement"
'_help -> "Custom help"
'_showConstraints -> false
'_error -> "Force an error"
'_showErrors -> false
```

### Kendi field constructor'ınızı yazmak

Sıklıkla, kendi alan constructor'ınızı yazmak isteyeceksiniz. Şöyle bir şablon yazarak başlayın:

@[template](code/javaguide/forms/myFieldConstructorTemplate.scala.html)

Bunu `views/` içine kaydedin ve `myFieldConstructorTemplate.scala.html` ismini verin.

> **Not:** Bu sadece basit bir örnektir. Bunu istediğiniz kadar karmaşıklaştırabilirsiniz. Aynı zamanda `@elements.field`ı kullanarak orijinal alana erişebilirsiniz.

Şimdi bunu kullanarak bir yerlerde bir `FieldConstructor` yaratın:

@[field](code/javaguide/forms/withFieldConstructor.scala.html)

## Tekrar eden değerleri işlemek

Son yardımcı tekrar eden değerler için girdi yaratmayı kolaylaştırır. Bu şekilde bir form tanımınız olduğunu varsayın:

@[code](code/javaguide/forms/html/UserForm.java)

Şimdi `emails` alanı için formun içerdiği kadar çok girdiyi üretmek zorundasınız. Bunun için basitçe `repeat` yardımcısını kullanın:

@[repeat](code/javaguide/forms/helpers.scala.html)

Karşılık gelen form verisi boş olsa bile gösterilecek en az sayıda alanı belirtmek için `min` parametresni kullanın.

> **Sonraki:** [[CSRF karşısında korunmak|JavaCsrf]]



