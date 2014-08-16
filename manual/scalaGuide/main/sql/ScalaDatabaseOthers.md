<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Diğer veritabanı kütüphaneleri ile entegrasyon

Play ile istediğiniz **SQL** veritabanı erişim katmanını kullanabilir ve `play.api.db.DB` yardımcısından kolayca bir `Connection` ya da `Datasource` elde edebilirsiniz.

## ScalaQuery ile entegrasyon

Buradan hareketle bir JDBC veri kaynağına ihtiyaç duyan herhangi bir JDBC erişim katmanı ile entegre olabilirsiniz. Örnek olarak [ScalaQuery](https://github.com/szeiger/scala-query) ile entegre olmak için:

```scala
import play.api.db._
import play.api.Play.current

import org.scalaquery.ql._
import org.scalaquery.ql.TypeMapper._
import org.scalaquery.ql.extended.{ExtendedTable => Table}

import org.scalaquery.ql.extended.H2Driver.Implicit._

import org.scalaquery.session._

object Task extends Table[(Long, String, Date, Boolean)]("tasks") {

  lazy val database = Database.forDataSource(DB.getDataSource())

  def id = column[Long]("id", O PrimaryKey, O AutoInc)
  def name = column[String]("name", O NotNull)
  def dueDate = column[Date]("due_date")
  def done = column[Boolean]("done")
  def * = id ~ name ~ dueDate ~ done

  def findAll = database.withSession { implicit db:Session =>
      (for(t <- this) yield t.id ~ t.name).list
  }

}
```

## Veri kaynağını JNDI üzerinden sunmak

Bazı kütüphaneler `Datasource` referansını JNDI üzerinden almayı beklerler. Play tarafından yönetilen herhangi bir veri kaynağını şu yapılandırmayı `conf/application.conf` içine ekleyerek JNDI üzerinden sunabilirsiniz:

```
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:mem:play"
db.default.jndiName=DefaultDS
```

> **Sonraki:** [[Önbelleği kullanmak | ScalaCache]]
