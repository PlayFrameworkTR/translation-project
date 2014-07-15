<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Play Cache API

Cache API'nin varsayılan gerçeklemesi [EHCache](http://ehcache.org/) kullanır. Bir plugin aracılığıyla siz de kendi gerçeklemenizi sağlayabilirsiniz.

## Cache API'yi uygulamaya dahil etmek

Bağımlılık listenize `cache` ekleyin. Örneğin, `build.sbt` içinde:

```scala
libraryDependencies ++= Seq(
  cache,
  ...
)
```

## Cache API'ye erişmek

Cache API `play.api.cache.Cache` nesnesi tarafından sağlanır. Bu nesne kayıtlı bir önbellek pluginine ihtiyaç duyar.

> **Not:** Bu API çeşitli gerçeklemelerin kullanılabilmesi için özellikle küçük tutulmuştur. Daha özel bir API gereksinimi olması durumunda kendi Cache plugininiz tarafından sağlananı kullanabilirsiniz.

Bu basit API'yi kullanarak önbellekte veri saklayabilirsiniz:

@[set-value](code/ScalaCache.scala)

Ve bu veriyi daha sonra okuyabilirsiniz:

@[get-value](code/ScalaCache.scala)

Ayrıca veri varsa okumak ya da yoksa değerini önbellekte setlemek için bir de yardımcı vardır:

@[retrieve-missing](code/ScalaCache.scala)

Bir veriyi önbellekten silmek için `remove` metodunu kullanabilirsiniz:

@[remove-value](code/ScalaCache.scala)

## HTTP yanıtlarını önbelleğe almak

Standart Action birleştirmeyi kullanarak akıllı önbellekli action'lar yaratabilirsiniz.

> **Not:** Play HTTP `Result` nesneleri önbelleğe almak ve daha sonra tekrar kullanmak için uygundurlar.

Play standart kullanımlar için dahili bir yardımcı sağlar:

@[cached-action](code/ScalaCache.scala)

Ve hatta:

@[composition-cached-action](code/ScalaCache.scala)

### Önbellek kontrolü

Neyi önbelleğe alacağınızı ya da neyi önbellekten hariç tutacağınızı kolayca belirleyebilirsiniz.

Yalnızca 200 Ok yanıtlarını önbelleğe almak isteyebilirsiniz.

@[cached-action-control](code/ScalaCache.scala)

Ya da 404 Not Found yanıtlarını yalnızca birkaç dakika için önbelleğe almak isteyebilirsiniz.

@[cached-action-control-404](code/ScalaCache.scala)

> **Sonraki:** [[Web servisleri çağırmak | ScalaWS]]
