<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Yeni bir uygulama oluşturmak

## Activator komutu ile yeni bir uygulama oluşturun


`activator` komutu yeni bir Play uygulaması oluşturmak için kullanılabilir. Activator, uygulamanızın temel alacağı şablonu seçmenize olanak tanır. Temel Play projeleri için bu şablonların isimleri Scala tabanlı Play uygulamaları için `play-scala` ve Java tabanlı Play uygulamaları için `play-java`'dır.

> Bu noktada şablon olarak Scala ya da Java seçmenizin daha sonra bunu değiştiremeyeceğiniz anlamına gelmediğini unutmayın. Örneğin Java uygulama şablonunu kullanarak bir uygulama oluşturabilir ve istediğiniz zaman Scala kodu eklemeye başlayabilirsiniz.

Temel bir Play Scala uygulaması oluşturmak için aşağıdaki komutu çalıştırın:

```bash
$ activator new my-first-app play-scala
```

Temel bir Play Java uygulaması oluşturmak için aşağıdaki komutu çalıştırın:

```bash
$ activator new my-first-app play-java
```

Her iki durumda da `my-first-app`'i uygulama adı olarak kullanmak istediğiniz isim ile değiştirebilirsiniz. Activator bunu uygulamayı yaratacağı dizin adı olarak kullanacaktır. İsterseniz bu adı daha sonra değiştirebilirsiniz.

[[images/activatorNew.png]]

Uygulama oluşturulduktan sonra `activator` komutunu [[Play konsoluna|PlayConsole]] girmek için kullanabilirsiniz.

```bash
$ cd my-first-app
$ activator
```

> Diğer Activator şablonlarını kullanmak isterseniz bunu `activator new` komutunu çalıştırarak yapabilirsiniz. Bu komut sizden bir uygulama adı isteyecek ve daha sonra size uygun bir şablon seçme imkanı verecektir.

## Activator UI kullanarak yeni bir uygulama oluşturun

Yeni Play uygulamaları Activator UI ile de oluşturulabilir. Activator UI kullanmak için aşağıdaki komutu çalıştırın:

```bash
$ activator ui
```

Activator UI dokümantasyonunu [buradan](https://typesafe.com/activator/docs) okuyabilirsiniz.

## Activator kullanmadan yeni bir uygulama oluşturun

Activator kurmadan, doğrudan sbt kullanarak Play uygulaması oluşturmak da mümkündür.

> Önce [sbt](http://www.scala-sbt.org/) kurun.

Yeni uygulamanız için bir dizin oluşturun ve sbt inşa betiğinize iki ek yapın.

`project/plugins.sbt` dosyasına şunları ekleyin:

```scala
// Typesafe deposu
resolvers += "Typesafe repository" at "http://repo.typesafe.com/typesafe/releases/"

// Play projeleri için Play sbt eklentisini kullan
addSbtPlugin("com.typesafe.play" % "sbt-plugin" % "%PLAY_VERSION%")
```

Burada `%PLAY_VERSION%` değerini kullanmak istediğiniz Play sürümü ile değiştirmeyi unutmayın. Bir snapshot sürümü kullanmak isterseniz aşağıdaki depoyu eklemeniz gerekecektir.

```
// Typesafe snapshots
resolvers += "Typesafe Snapshots" at "http://repo.typesafe.com/typesafe/snapshots/"
```

Java projeleri için `build.sbt` dosyasında şunlar olmalıdır:

```scala
name := "my-first-app"

version := "1.0"

lazy val root = (project in file(".")).enablePlugins(PlayJava)
```

ya da Scala projeleri için şunları ekleyin:

```scala
name := "my-first-app"

version := "1.0.0-SNAPSHOT"

lazy val root = (project in file(".")).enablePlugins(PlayScala)
```

Bu dizin içerisinde artık sbt konsolunu çalıştırabilirsiniz:

```bash
$ cd my-first-app
$ sbt
```

sbt projenizi yükleyecek ve bağımlılıkları indirecektir.

> **Sonraki:** [[Bir Play uygulamasının anatomisi|Anatomy]]
