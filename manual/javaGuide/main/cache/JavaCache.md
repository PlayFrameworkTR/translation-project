<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Play Cache API

Modern uygulamalarda veri önbelleme tipik bir optimizasyondur ve bu yüzden Play, evrensel bir önbellekleme sunmaktadır. Önbellekleme hakkında önemli bir nokta, önbelleğin gerektiği gibi davranmasıdır, yani sakladığınız veri kaybolabilir.

Önbellekte depolanan tüm veriler için, veri kaybı olması durumunda yenilenme stratejisi eklenmelidir. Bu felsefe Play arkasındaki temel ögelerden biridir, çalışma ömrü boyunca oturum bilgilerini tutması beklenen Java EE'den farklıdır.

The cache Api'nin varsayılan uygulaması [EHCache](http://www.ehcache.org/) kullanır ve `build.sbt` dosyasında bulunan `libraryDependencies`'e `cache` eklenerek aktif edilebilir. Ayrıca eklenti ile kendi gerçeklemenizi sağlayabilirsiniz.

## Cache API'yi Uygulamaya Dahil Etmek

Bağımlılık listenize `cache` ekleyin. Örneğin, `build.sbt` içinde:

```scala
libraryDependencies ++= Seq(
  cache,
  ...
)
```

## Cache API'ye Erişim

Cache API `play.api.cache.Cache` nesnesi tarafından sağlanır. Bu nesne kayıtlı bir önbellek pluginine ihtiyaç duyar.

> **Not:** Bu API çeşitli gerçeklemelerin eklenebilmesi için kasten asgari tutulmuştur. Eğer daha detaylı bir API'ye ihtiyacınız varsa, kendi Cache eklentiniz tarafından sağlananı kullanınız.

Bu basit API'yi kullanarak, önbellekte veri saklayabilirsiniz.

@[simple-set](code/javaguide/cache/JavaCache.java)

İsteğe bağlı olarak sona erme süresi (saniye) tanımlayabilirsiniz:

@[time-set](code/javaguide/cache/JavaCache.java)

Sonradan bu datayı tekrar alabilirniz:

@[get](code/javaguide/cache/JavaCache.java)

Önbellekten veri silmek için `remove` metodunu kullanının:

@[remove](code/javaguide/cache/JavaCache.java)

## HTTP Yanıtlarını Önbellekleme

Kolaylıkla standart `Action` bileşimini kullanarak, akıllı önbelleklenmiş eylemleri oluşturabilirsiniz.

> **Not:** Play HTTP `Result` olayları önbelleklemek ve tekrar kullanmak için uygundur.

Play standart kullanımlar için dahili bir yardımcı sağlar:

@[http](code/javaguide/cache/JavaCache.java)

> **Sonraki:** [[Web Servisleri Çağırmak | JavaWS]]
