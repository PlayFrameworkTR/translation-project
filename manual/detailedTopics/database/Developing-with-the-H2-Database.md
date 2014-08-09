<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# H2 veritabanı

Bellek-içi H2 veritabanı geliştirme için çok kullanışlıdır çünkü evolution'larınız play yeniden başlatıldığında en baştan çalıştırılır. Eğer anorm kullanıyorsanız muhtemelen onun planladığınız üretim veritabanına yakın benzerlikte olmasına ihtiyaç duyarsınız. Belli bir veritabanına benzetmek istediğinizi H2'ye anlatabilmek için application.conf dosyanızdaki veritabanı url'ine bir parametre eklersiniz, örneğin:

```
db.default.url="jdbc:h2:mem:play;MODE=MYSQL"
```

## Hedef veritabanları

<table>
<tr>
<tr><td>MySql</td><td>MODE=MYSQL</td>
<td><ul><li>H2 uuid() fonksiyonuna sahip değildir. Yerine random_uuid() kullanabilirsiniz.  Veya aşağıdaki kodu 1.sql dosyanıza ekleyin: <pre><code>CREATE ALIAS UUID FOR 
"org.h2.value.ValueUuid.getNewRandom";</code></pre></li>  

<li>MySQL'deki metin karşılaştırması varsayılan olarak büyük küçük harfe duyarsızdır, oysa bu H2'de büyük küçük harfe duyarlıdır (diğer birçok veritabanındaki gibi). H2 büyük küçük harf duyarlılığını destekler, fakat SET IGNORECASE TRUE kullanarak ayrı olarak ayarlanmaya ihtiyaç duyar.  Bu =, LIKE, REGEXP kullanılarak yapılan karşılaştırmaları etkiler.</li></td></tr>
<tr><td>DB2</td><td>
MODE=DB2</td><td></td></tr>
<tr><td>Derby</td><td>
MODE=DERBY</td><td></td></tr>
<tr><td>HSQLDB</td><td>
MODE=HSQLDB</td><td></td></tr>
<tr><td>MS SQL</td><td>
MODE=MSSQLServer</td><td></td></tr>
<tr><td>Oracle</td><td>
MODE=Oracle</td><td></td></tr>
<tr><td>PostgreSQL</td><td>
MODE=PostgreSQL</td><td></td></tr>
</table>

## Bellek-içi VT'nın sıfırlanması önlemek

Eğer hiç bağlantı yoksa H2 veritabanınızı sıfırlar. Muhtemelen bunun olmasını istemezsiniz. Bunu önlemek için url'e `DB_CLOSE_DELAY=-1` ekleyin (ayraç olarak noktalı virgül kullanın). Örneğin `jdbc:h2:mem:play;MODE=MYSQL;DB_CLOSE_DELAY=-1`

## H2 Tarayıcı

Veritabanınızın içeriğine play konsolunda `h2-browser` yazarak göz atabilirsiniz. Web tarayıcınızın içinde bir SQL tarayıcısı çalışacak.

## H2 Dokümantasyon

Daha fazla H2 dokümantasyonu [kendi web sitesinde](http://www.h2database.com/html/features.html) mevcuttur.