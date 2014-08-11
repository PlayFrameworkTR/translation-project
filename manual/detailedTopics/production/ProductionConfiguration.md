<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# İlave Yapılandırma

Bir uygulamayı üretim modunda çalıştırırken bütün yapılandırma ayarlarını değiştirebilirsiniz. Bu bölüm daha yaygın kullanımları kapsar.

Bütün bu ilave yapılandırmalar Java System özelliklerini kullanarak beilrtilir ve Play tarafından üretilen başlatma betiklerinden birini kullanıyorsanız doğrudan kullanılabilir.

## HTTP sunucu adres ve kapısını belirtmek

Hem HTTP kapısını hem de adresini sağlayabilirsiniz. Varsayılan, `9000` kapısını `0.0.0.0` adresinde (bütün adreslerde) dinlemektir.

```
$ /path/to/bin/<project-name> -Dhttp.port=1234 -Dhttp.address=127.0.0.1
```

> Bu yapılandırmaların yalnızca varsayılan gömülü Netty sunucusu için sağlandığını unutmayın.

## İlave JVM argumanları belirtmek

`start` betiğinde JVM argümanlarını belirtebilirsiniz. Aksi takdirde varsayılan JVM ayarları kullanılacaktır:

```
$ /path/to/bin/<project-name> -J-Xms128M -J-Xmx512m -J-server
```

Uygunluk için; kullanılacak en az ve en fazla bellek, permgen ve ayrılan kod önbelleği boyutunu tek seferde ayarlayabilirsiniz; bir formül bu değerleri (en fazla belleği temsil eden) verilen parametreden hesaplamak için kullanılır:

```
$ /path/to/bin/<project-name> -mem 512 -J-server
```

## Alternatif bir yapılandırma dosyası belirtmek

Varsayılan olarak sınıf yolundan `application.conf` dosyası yüklenir. Gerekirse bu dosyaya bir alternatif belirtebilirsiniz:

### `-Dconfig.resource` kullanmak

Bu, uygulama sınıf yolunda alternatif bir yapılandırma dosyası arayacaktır (genelde bu alternatif yapılandırma dosyalarını uygulamanızı paketlemeden önce `conf/` dizinine yerleştirirsiniz). Play, `conf/`'u kendiniz eklememeniz için `conf/` dizinine bakacaktır. 

```
$ /path/to/bin/<project-name> -Dconfig.resource=prod.conf
```

### `-Dconfig.file` kullanmak

Uygulama paketine eklenmemiş başka bir yerel yapılandırma dosyasını da belirtebilirsiniz:

```
$ /path/to/bin/<project-name> -Dconfig.file=/opt/conf/prod.conf
```

### `-Dconfig.url` kullanmak

Aynı zamanda bir yapılandırma dosyasını bir URL'den yüklenecek şekilde belirtebilirsiniz:

```
$ /path/to/bin/<project-name> -Dconfig.url=http://conf.mycompany.com/conf/prod.conf
```

> Her zaman orijinal yapılandırma dosyasına yeni bir `prod.conf` dosyasında `include` direktifiyle referans verebileceğinizi unutmayın. Örneğin:
> 
> ```
> include "application.conf"
> 
> key.to.override=blah
> ```

## Belirli yapılandırma anahtarlarını değiştirmek

Bazen başka bir bütün yapılandırma dosyası belirtmek istemezisiniz; yalnızca birkaç belirli anahtarı değiştirmek istersiniz. Bunu, o anahtarları Java System özelliklerinde belirterek yapabilirsiniz:

```
$ /path/to/bin/<project-name> -Dapplication.secret=abcdefghijk -Ddb.default.password=toto
```

## Çevre değişkenlerini kullanmak

Aynı  zamanda `application.conf` dosyanızda çevre değişkenlerine referans verebilirsiniz:

```
my.key = defaultvalue
my.key = ${?MY_KEY_ENV}
```

Burada, üzerine yazma alanı `my.key = ${?MY_KEY_ENV}`, eğer `MY_KEY_ENV` için bir mevcut bir değer yoksa kaybolur. Ancak eğer `MY_KEY_ENV` çevre değişkenini ayarlarsanız, o kullanılacaktır.

## Günlük tutma yapılandırma dosyasını değiştirmek

### Uygulamanızla birlikte özel bir günlük tutma yapılandırma dosyası paketlemek

`application-logger.xml` adında alternatif bir günlük tutma yapılandırma dosyası oluşturun ve onu `<app>/conf` dizinine kopyalayın.

Aynı zamanda bir System özelliği yoluyla başka bir günlük tutma yapılandırma dosyası belirtebilirsiniz. Lütfen, eğer bir yapılandırma dosyası belirtilmediyse Play'in, üretim modunda Play ile gelen varsayılan `logger.xml` dosyasını kullanacağını unutmayın. Bu demektir ki `application.conf`dosyanızdaki bütün günlük tutma seviyesi ayarları geçersiz kılınacaktır. İyi bir uygulama olarak, daima `application-logger.xml` dosyanızı belirtin.

### `-Dlogger.resource` kullanmak

Sınıf yolundan yüklenecek başka bir günlük tutma yapılandırma dosyası belirtin:

```
$ /path/to/bin/<project-name> -Dlogger.resource=conf/prod-logger.xml
```

### `-Dlogger.file` kullanmak

Sistemden yüklenecek başka bir günlük tutma yapılandırma dosyası belirtin:

```
$ /path/to/bin/<project-name> -Dlogger.file=/opt/prod/prod-logger.xml
```

### `-Dlogger.url` kullanmak

Bir URL'den yüklenecek başka bir günlük tutma yapılandırma dosyası belirtin:

```
$ /path/to/bin/<project-name> -Dlogger.url=http://conf.mycompany.com/logger.xml
```

## RUNNING_PID'in yolunu değiştirmek

Başlatılan uygulamanın işlem tanımlayıcısını içeren dosyanın yolunu değiştirmek mümkündür. Genellikle bu dosya Play projenizin kök dizinine yerleştirilir; ancak bu dosyayı, `/var/run` gibi yeniden başlatmada otomatik olarak temizlenecek bir dizine yerleştirmeniz tavsiye edilir:

```
$ /path/to/bin/<project-name> -Dpidfile.path=/var/run/play.pid
```

> Dizinin mevcut olduğundan ve Play uygulamasını çalıştıran kullanıcının o dizine yazma iznine sahip olduğundan emin olun.

Bu dosyayı kullanarak, uygulamanızı `kill` komutuyla durdurabilirsiniz. Örneğin:

```
$ kill $(cat /var/run/play.pid)
```

## Gelişmiş HTTP sunucu yapılandırması

Play HTTP sunucusu sistem özellikleri yoluyla birçok şekilde ayarlanabilir.

> **Not:** `application.conf`'u HTTP sunucu özelliklerini yapılandırmak için kullanamazsınız.

Aşağıdaki HTTP protokol seçenekleri desteklenmektedir:

`http.netty.maxInitialLineLength`
: Bir HTTP isteğinin ilk satırının azami uzunluğu, varsayıaln olarak 4096
`http.netty.maxHeaderSize`
: Bütün HTTP başlığının azami boyutu, varsayılan olarak 8192
`http.netty.maxChunkSize`
: Netty'nin gövde yığınlarını bölmeden önce tamponlayacağı boyut, varsayılan olarak 8192

Play aynı zamanda Netty'nin desteklediği tüm TCP soket ayarlarını, onlara `http.netty.option` ön ekini ekleyerek yapılandırmaya izin verir. Kabul edilen bağlantıların soketlerine uygulanması gereken soket seçeneklerinin ilave bir `child` ön ekine sahip olması gerektiğini unutmayın. İşte yaygın kullanılanlardan bazıları:

`http.netty.option.child.keepAlive`
: TCP'yi canlı tutmayı açıp kapamak için true/false olarak ayarlar.
`http.netty.option.backlog`
: Sıralanmış gelen bağlantılar için azami boyutu belirler. 

Desteklenen seçeneklerin tam listesi için aşağıdaki Netty dokümantasyonuna bakabilirsiniz:

* [SocketChannelConfig](http://netty.io/3.9/api/org/jboss/netty/channel/socket/SocketChannelConfig.html)
* [ServerSocketChannelConfig](http://netty.io/3.9/api/org/jboss/netty/channel/socket/ServerSocketChannelConfig.html)
* [NioSocketChannelConfig](http://netty.io/3.9/api/org/jboss/netty/channel/socket/nio/NioSocketChannelConfig.html)

> **Sonraki:** [[Bir ön yüz HTTP sunucusu kurmak|HTTPServer]]
