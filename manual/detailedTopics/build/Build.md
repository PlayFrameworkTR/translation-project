<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# İnşa Sistemi

Play inşa sistemi, Scala ve Java projeleri için yüksek performanslı ve birleşik bir inşa sistemi olan [sbt](http://www.scala-sbt.org/)'yi kullanır. `sbt`'yi inşa sistemimiz olarak kullanmak bu sayfada açıklanan bazı gereksinimleri beraberinde getirir.

## Play uygulama dizin yapısı

Çoğu insan Play'e `activator new foo` komutuyla başlar, bu da aşağıdaki gibi bir dizin yapısı üretir:

- `/`: Uygulamanızın kök dizini
- `/README`: Uygulamanızı tanıtan bir metin dosyası
- `/app`: Uygulama kodlarınızın saklanacağı dizin
- `/build.sbt`: Uygulamanızın inşasını açıklayan [sbt](http://www.scala-sbt.org/) ayarları
- `/conf`: Uygulamanız için yapılandırma dosyaları
- `/project`: Daha fazla inşa açıklamaları ve bilgileri
- `/public`: Projenizdeki sabit ve herkese açık varlıkların saklanacağı dizin
- `/test`: Uygulamanızın test kodlarının saklanacağı dizin

Şimdilik, `/build.sbt` dosyası ve `/project` dizini ile ilgileneceğiz.

## `/build.sbt` dosyası 

`activator new foo` komutunu kullandığınızda, inşa açıklama dosyası, `/build.sbt`, bu şekilde üretilecektir:

```scala
name := "foo"

version := "1.0-SNAPSHOT"

libraryDependencies ++= Seq(
  jdbc,
  anorm,
  cache
)

lazy val root = (project in file(".")).enablePlugins(PlayScala)
```

`name` satırı uygulamanızın adını tanımlar ve uygulamanızın kök dizininin adıyla aynı olacaktır. Kök dizin adı, `activator new` komutuyla birlikte verdiğiniz argümandan çıkarılır.

`version` satırı uygulamanızın versiyonunu belirler. Bu da inşa sisteminizin ürettiği artifact'lerin isminde kullanılacaktır.

`libraryDependencies` satırı uygulamanızın bağımlı olduğu kütüphaneleri belirtir. Aşağıda bununla ilgili daha detaylı bilgiyi bulabilirsiniz.

SBT'yi Java veya Scala için yapılandırmak için `PlayJava` veya `PlayScala` eklentilerini kullanmalısınız.

## İnşa için Scala'yı kullanmak

Activator aynı zamanda projenizin `project` dizinindeki scala dosyalarından da inşa gereksinimlerini kurabilir. Tavsiye edilen uygulama `build.sbt`'yi kullanmaktır ama bazı durumlarda scala'yı doğrudan kullanmak da gerekebilir. Kendinizi bu durumda bulursanız, örneğin eski bir projeyi aktarırken, işte birkaç yararlı import yönergesi:

```scala
import sbt._
import Keys._
import play.Play.autoImport._
import PlayKeys._
```

`autoImport`'u gösteren satır bir sbt eklentisinin otomatik olarak açıklanmış özelliklerini import etme anlamına gelir. Aynı satırlarda, eğer bir sbt-web eklentisini import ediyorsanız şöyle de yapabilirsiniz:

```scala
import com.typesafe.sbt.less.autoImport._
import LessKeys._
```

## `/project` dizini

Projenizi inşa etmeye dair her şey uygulama dizininizdeki `/project` dizininde saklanır. Bu bir [sbt](http://www.scala-sbt.org/) gerekliliğidir. Bu dizin içinde, iki dosya vardır:

- `/project/build.properties`: Bu kullanılan SBT versiyonunu açıklayan işaretleyici bi dosyadır.
- `/project/plugins.sbt`: Play de dahil olmak üzere, proje inşa sisteminin kullandığı SBT ekelntileri bu dosyada belirtilir.

## SBT için Play eklentisi (`/project/plugins.sbt`)

Play konsolu ve canlı yeniden yükleme gibi onun tüm geliştirme özellikleri bir SBT eklentisi aracılığıyla uygulanır. Bu eklenti `/project/plugins.sbt` dosyasında kayıt edilir:

```scala
addSbtPlugin("com.typesafe.play" % "sbt-plugin" % playVersion) // where version is the current Play version, i.e.  "2.3.0" 
```

> Unutmayın ki Play versiyonunu değiştirdiğinizde `build.properties` ve `plugins.sbt` dosyaları manuel olarak güncellenmelidir.


## Bağımlılıklar ve çözümleyiciler eklemek

Bağımlılıklar eklemek aşağıdaki `zentasks` Java inşa dosyası örneğinde gösterildiği kadar kolaydır:

```scala
name := "zentask"

version := "1.0"

libraryDependencies ++= Seq(javaJdbc, javaEbean)     

lazy val root = (project in file(".")).enablePlugins(PlayJava)
```

İlave depolar eklemek için çözücüler eklemek de çok kolaydır:

```scala
resolvers += "Repository name" at "http://url.to/repository" 
```


> **Sonraki:** [[SBT Ayarları Hakkında | SBTSettings]]
