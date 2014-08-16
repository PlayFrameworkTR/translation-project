<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Uygulamanızın bağımsız bir sürümünü yaratmak

## dist görevini kullanmak

Bir Play uygulamasını dağıtmanın en kolay yolu, kaynağını (genelde bir git iş akışıyla) sunucuda bulundurmak ve `activator start` veya `activator stage` komutlarından birini kullanarak onu yerinde başlatmaktır.

Ancak, bazen uygulamanızın ikili bir versiyonunu inşa ederek, Play'e bağımlı kalmadan sunucuya doğrudan dağıtmaya ihtiyaç duyabilirsiniz. Bunu, `dist` göreviyle yapabilirsiniz.

Play konsolunda `dist` yazın:

```bash
[my-first-app] $ dist
```

[[images/dist.png]]

Bu, uygulamanızın `target/universal` dizininde, uygulamanızı çalıştırmak için gerekecek bütün JAR dosyalarını içeren bir ZIP dosyası oluşturur. Alternatif olarak işletim sisteminizin konsolunda, aynı işi yapan `activator dist` komutunu da yazabilirsiniz:

```bash
$ activator dist
```

> Windows kullanıcıları için .bat dosya uzantılı bir başlangıç betiği üretilecektir. Bir Play uygulamasını Windows'da çalıştırmak için bunu kullanın.
>
> Unix kullanıcıları için, zip dosyaları Unix dosya izinlerini korumaz. Bu sebeple zip dosyası açıldığında başlangıç betiğinin çalıştırıalbilir olarak ayarlanması gerekir:
>
> ```bash
> $ chmod +x /path/to/bin/<project-name>
> ```
>
> Alternatif olarak, bir tar.gz dosyası da üretilebilir. Tar dosyaları izinleri korur. `dist` görevi yerine `universal:package-zip-tarball` görevini çağırın:
>
> ```bash
> activator universal:package-zip-tarball
> ```

Varsayılan olarak, dist görevi oluşturulan pakete API dokümantasyonunu da ekelyecektir. Eğer bu gereli değilse, aşağıdaki satır `build.sbt` dosyasına eklenerek bundan kaçınılabilir:

```scala
doc in Compile <<= target.map(_ / "none")
```
Alt projeli inşalarda, yukarıdaki ifade bütün alt proje tanımlarına uygulanmalıdır.

## Yerli Paketleyici

Play, [SBT Native Packager eklentsi](http://www.scala-sbt.org/sbt-native-packager/)'ni kullanır. Yerli paketleyici eklentisi `dist` görevini bir zip dosyası yaratacak şekilde beyan eder. `dist` görevini çağırmak, aşağıdaki komutu çalıştırmakla eşdeğerdir:

```bash
$ activator universal:package-bin
```

Aşağıdaki türler de dahil, birçok başka türde arşiv elde edilebilir:

* tar.gz
* OS X disk imajları
* Microsoft Installer (MSI)
* RPM'ler
* Debian paketleri
* System V / RPM/Debian paketlerindeki init.d ve Upstart hizmetleri

Ayrıntılı bilgi için lütfen yerli paketleyicideki [dokümantasyon](http://www.scala-sbt.org/sbt-native-packager)a başvurun.

### Bir sunucu dağıtımı inşa etmek

sbt-native-packager eklentisi aşağıdaki özellikleri etkinleştiren bir `java_server` prototipi (archetype) sağlar:

* System V veya Upstart başlangıç betikleri
* [Varsayılan dizinler](http://www.scala-sbt.org/sbt-native-packager/GettingStartedServers/MyFirstProject.html#default-mappings)

Tam bir dökümantasyon [bu adreste](http://www.scala-sbt.org/sbt-native-packager/GettingStartedServers/index.html) bulunabilir.

`java_server` prototipi varsayılan olarak etkindir, ancak hangi paketleri inşa etmek istediğinize göre bazı ayarlar eklemeniz gerekebilir.

#### Asgari Debian ayarları

```scala
import com.typesafe.sbt.SbtNativePackager._
import NativePackagerKeys._

maintainer in Linux := "Ad Soyad <ad.soyad@example.com>"

packageSummary in Linux := "Özel Paket Özeti"

packageDescription := "Uzun paket açıklaması"
```

Paketinizi bu komutla inşa edin:

```bash
play debian:packageBin
```

#### Asgari RPM ayarları

```scala
import com.typesafe.sbt.SbtNativePackager._
import NativePackagerKeys._

maintainer in Linux := "Ad Soyad <ad.soyad@example.com>"

packageSummary in Linux := "Özel Paket Özeti"

packageDescription := "Uzun paket açıklaması"

rpmRelease := "1"

rpmVendor := "example.com"

rpmUrl := Some("http://github.com/example/server")

rpmLicense := Some("Apache v2")
```

```bash
play rpm:packageBin
```

> Bazı hata mesajları yazdırılabilir. Bu yalnızca rpm'in stdout yerine stderr'e günlük tutmasıdır.

#### Play PID Yapılandırması

[[Üretim yapılandırması|ProductionConfiguration]] sayfasında tarif edildiği gibi, Play kendi PID'ini idare eder.
Başlangıç betiğine PID dosyasını nereye koyacağını bildirmek için `src/templates/` dizini içine `etc-default` isimli bir dosya koyun ve aşağıdaki içeriği ekleyin:

```bash
-Dpidfile.path=/var/run/${{app_name}}/play.pid
# Buraya diğer bütün başlangıç ayarlarını da ekleyin
```

Yer değiştirmelerin bütün listesine yakından bakmak için [dokümantasyon](http://www.scala-sbt.org/sbt-native-packager/GettingStartedServers/AddingConfiguration.html)'u ziyaret edin.


## Bir Maven (veya Ivy) deposuna yayımlamak

Aynı zamanda uygulamanızı bir Maven deposuna yayımlayabilirsiniz. Bu, hem uygulamanızı içeren JAR dosyasını hem de karşılık gelen POM dosyasını yayımlar.

`build.sbt` dosyasında yayımlamak istediğiniz depo için yapılandırmalar yapmalısınız:

```scala
 publishTo := Some(
   "Çözücüm" at "http://mycompany.com/repo"
 ),
 
 credentials += Credentials(
   "Depo", "http://mycompany.com/repo", "admin", "admin123"
 )
```

Sonrasında Play konsolunda, `publish` görevini kullanın:

```bash
[my-first-app] $ publish
```

> Çözücüler ve kimlik bilgileri tanımlamaları hakkında daha fazla bilgi almak için [sbt dokümantasyonu](http://www.scala-sbt.org/release/docs/index.html)'nu ziyaret edin.

## SBT assembly eklentisini kullanmak

Resmi olarak desteklenmese de, SBT assembly eklentisi Play uygulamalarını paketlemek ve çalıştırmak için kullanılabilir. Bu, çıktı olarak bir jar oluşturacak ve onu `java` komutuyla doğrudan çalıştırmanıza izin verecektir.

Bunu kullanmak için, `project/plugins.sbt` dosyanızda bu eklentiye bir bağımlılık ekleyin:

```scala
addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.11.2")
```

Şimdi, aşağıdaki yapılandırmayı `build.sbt` dosyanıza ekleyin:

```scala
import AssemblyKeys._

assemblySettings

mainClass in assembly := Some("play.core.server.NettyServer")

fullClasspath in assembly += Attributed.blank(PlayKeys.playPackageAssets.value)
```

Artık `activator assembly` komutunu çalıştırarak uygulamanızın bir paketini oluşturabilir ve onu aşağıdaki komutla çalıştırabilirsiniz:

```
$ java -jar target/scala-2.XX/<projenizinadı>-assembly-<version>.jar
```

You'll need to substitute in the right project name, version and scala version, of course.

> **Sonraki:** [[Üretim yapılandırması|ProductionConfiguration]]
