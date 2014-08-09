<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# GZIP sıkıştırmayı ayarlamak

Play yanıtları gzip ile sıkıştırmak için bir gzip filtresi sağlar. Bu filtre uygulamanın filtrelerine `Global` nesnesi kullanılarak eklenebilir. Gzip filtresini etkinleştirmek için `build.sbt` dosyası içerisinde projenize Play filtre yardımcılarını ekleyin:

```scala
libraryDependencies += filters
```

## Scala için gzip etkinleştirmek

Bir Scala projesinde gzip filtresini etkinleştirmenin en kolay yolu `WithFilters` yardımcısını kullanmaktır:

@[global](code/GzipEncoding.scala)

Hangi yanıtların gerçeklendiğini kontrol etmek için istek başlığından yanıt başlığına bir fonksiyon alan `shouldGzip` parametresini kullanın.

Örneğin aşağıdaki kod yalnızca HTML yanıtlarını gzip ile sıkıştırır:

@[should-gzip](code/GzipEncoding.scala)

## Java için gzip etkinleştirmek

Java için gzip etkinleştirmek için onu `Global` nesnesindeki filtre listesine ekleyin:

@[global](code/detailedtopics/configuration/gzipencoding/Global.java)
