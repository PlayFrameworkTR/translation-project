<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Uygulama global ayarları

## Global nesnesi

Projenizde bir `Global` nesnesi tanımlamak uygulamanız için global ayarları yönetmenizi sağlar. Bu nesne varsayılan (boş) pakette tanımlanmalıdır ve [`GlobalSettings`](api/scala/index.html#play.api.GlobalSettings)'i extend etmelidir.

@[global-define](code/ScalaGlobal.scala)

> **İpucu:** `application.global` yapılandırma ayarını kullanarak özel bir `GlobalSettings` gerçeklemesi de belirtebilirsiniz.

## Uygulama başlangıç ve bitiş olaylarına kanca atmak

Uygulama yaşam döngüsündeki olaylardan haberdar olmak için `onStart` ve `onStop` metotlarını override edebilirsiniz:

@[global-hooking](code/ScalaGlobal.scala)

## Uygulama hata sayfası belirtmek

Uygulamanızda bir exception oluştuğunda `onError` metodu çağrılır. Varsayılan davranış dahili framework hata sayfasını kullanmaktır:

@[global-hooking-error](code/ScalaGlobal.scala)

## Bulunmayan action'ları ve hataları ele almak

Eğer framework bir istek için bir `Action` bulamazsa `onHandlerNotFound` metodu çağrılır:

@[global-hooking-notfound](code/ScalaGlobal.scala)


Bir yönlendirme bulunduğunda fakat istek parametrelerini bağlamak mümkün olmadığında `onBadRequest` metodu çağrılır:

@[global-hooking-bad-request](code/ScalaGlobal.scala)

> **Sonraki:** [[İsteklerde araya girmek | ScalaInterceptors]]
