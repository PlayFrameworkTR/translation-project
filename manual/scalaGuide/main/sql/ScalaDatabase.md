<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Bir SQL veri tabanına erişme

## JDBC bağlantı havuzlarını ayarlamak

Play JDBC bağlantı havuzlarını yönetmek için bir eklenti sağlar. İhtiyacınız kadar veritabanını ayarlayabilirsiniz.


Veritabanı eklentisini aktifleştirmek için inşa bağımlılıklarına jdbc ekleyin :

```scala
libraryDependencies += jdbc
```

Daha sonra `conf/application.conf` dosyasının içinde bir bağlantı havuzu ayarlamalısınız. Genel kabul, varsayılan JDBC veri kaynağı `default` olarak çağırılır ve uygun olan ayarlar `db.default.driver`, `db.default.url` şeklindedir.

Eğer bir şeyler doğru bir şekilde ayarlanmamış ise doğrudan tarayıcınız üzeriden haberdar edilirsiniz:

[[images/dbError.png]]

> **Not:** Muhtemelen JDBC URL ayar değeri için çift tırnak kullanmanız gerekecek, çünkü ':' ayarlama söz diziminde rezerve edilmiş bir karakterdir.

### H2 veri tabanı motoru bağlantı özellikleri

```properties
# Default database configuration using H2 database engine in an in-memory mode
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
```

```properties
# Default database configuration using H2 database engine in a persistent mode
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:/path/to/db-file"
```

H2 veritabanı için detaylı bağlantıları [H2 Veri Tabanı Motoru Kopya Kağıtı](http://www.h2database.com/html/cheatSheet.html) içinde bulabilirsiniz.

### SQLite veri tabanı motoru bağlantı özellikleri

```properties
# Default database configuration using SQLite database engine
db.default.driver=org.sqlite.JDBC
db.default.url="jdbc:sqlite:/path/to/db-file"
```

### PostgreSQL veri tabanı motoru bağlantı özellikleri

```properties
# Default database configuration using PostgreSQL database engine
db.default.driver=org.postgresql.Driver
db.default.url="jdbc:postgresql://database.example.com/playdb"
```

### MySQL veri tabanı motoru bağlantı özellikleri

```properties
# Default database configuration using MySQL database engine
# Connect to playdb as playdbuser
db.default.driver=com.mysql.jdbc.Driver
db.default.url="jdbc:mysql://localhost/playdb"
db.default.user=playdbuser
db.default.password="a strong password"
```

## SQL ifadelerini konsolda nasıl görülür?

```properties
db.default.logStatements=true
logger.com.jolbox=DEBUG // for EBean
```

## Çeşitli veri kaynakları nasıl ayarlanır

```properties
# Orders database
db.orders.driver=org.h2.Driver
db.orders.url="jdbc:h2:mem:orders"

# Customers database
db.customers.driver=org.h2.Driver
db.customers.url="jdbc:h2:mem:customers"
```

## JDBC Sürücüsünü ayarlamak

Play içerisinde hali hazır olarak sadece bir [H2](http://www.h2database.com) veri tabanı sürücüsü ile gelmektedir. Sonuç olarak, üretime çıkarken veri tabanınızın sürücüsünü bağımlıklık olarak eklemeniz gerekecek.

Örneğin, eğer MySQL5 kullanıyorsanız, bağlantı için bir [[bağımlılık | SBTDependencies]] ekleniz gerekir:

```scala
libraryDependencies += "mysql" % "mysql-connector-java" % "5.1.27"
```

Yada eğer sürücü depolarda bulunamazsa sürücüyü projenizin içine, `lib` klasörüne [[yönetilmeyen bağımlılıklar | Anatomy]] olarak koyabilirsiniz.

## JDBC veri kaynağına erişmek

`play.api.db` paketi ayarlanmış veri kanyaklarına erişimi sağlar:

```scala
import play.api.db._

val ds = DB.getDataSource()
```

## Bir JDBC bağlantısı edinme

JDBC bağlantısı edinmenin çeşitli yolları vardır. Bunu en basit yolu:

```scala
val connection = DB.getConnection()
```

Aşağıdaki kod çok basit bir JDBC örneğidir, MySQL 5.* ile çalışır:

```scala
package controllers
import play.api.Play.current
import play.api.mvc._
import play.api.db._

object Application extends Controller {

  def index = Action {
    var outString = "Number is "
    val conn = DB.getConnection()
    try {
      val stmt = conn.createStatement
      val rs = stmt.executeQuery("SELECT 9 as testkey ")
      while (rs.next()) {
        outString += rs.getString("testkey")
      }
    } finally {
      conn.close()
    }
    Ok(outString)
  }

}
```


Tabii ki bazı noktalarda açılan bağlantıyı bağlantı havuzuna göndermek için `close()` ile kapatmalıyız. Bunu başka yolu ise Play bağlantı kapatmayı sizin için yönetir:

```scala
// access "default" database
DB.withConnection { conn =>
  // do whatever you need with the connection
}
```

Varsayılan veri tabanından başka bir veri tabanı için:

```scala
// access "orders" database instead of "default"
DB.withConnection("orders") { conn =>
  // do whatever you need with the connection
}
```

Bağlantı bloğun sonunda otomatik olarak kapanır.

> **İpucu:** Her bir `Statement` ve `ResultSet` bağlantının doğru kapatılacağı gerekliliği ile oluşturulmuştur.

Bağlantının `auto-commit` özelliğinin `false` olması ve blok için bir işlem için:

```scala
DB.withTransaction { conn =>
  // do whatever you need with the connection
}
```

> **Sonraki:** [[Veritabanına erişmek için Anorm kullanmak | ScalaAnorm]]
