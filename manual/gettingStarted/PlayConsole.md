<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Play konsolunu kullanmak

## Konsolu başlatmak

Play konsolu, bir Play uygulamasının bütün hayat döngüsünü yönetmenizi sağlayan, sbt tabanlı bir geliştirme konsoludur.

Play konsolunu başlatmak için, projenizin dizinine geçin ve Activator'ı çalıştırın:

```bash
$ cd my-first-app
$ activator
```

[[images/console.png]]

## Yardım almak

Mevcut komutlar hakkında yardım almak için `help` komutunu kullanın. Aynı zamanda, bu komutu belirli bir komut ile beraber kullanarak bu komut hakkında bilgi edinebilirsiniz:

```bash
[my-first-app] $ help run
```

## Sunucuyu geliştirme modunda çalıştırmak

Mevcut uygulamayı geliştirme modunda çalıştırmak için `run`komutunu kullanın:

```bash
[my-first-app] $ run
```

[[images/consoleRun.png]]

Bu modda, sunucu otomatik yenileme özelliği etkinleştirilmiş halde başlatılacaktır. Yani her bir istek için Play projenizi gözden geçirecek ve gereken kaynakları yeniden derleyecektir. Gerekli durumlarda uygulama otomatik olarak yeniden başlayacaktır.

Eğer bir derleme hatası varsa derlemenin sonucunu doğrudan tarayıcınızda göreceksiniz:

[[images/errorPage.png]]

Sunucuyu durdurmak için, `Crtl+D` tuşlarını kullanın. Böylece Play konsoluna geri döneceksiniz.

## Derlemek

Aynı zamanda, Play ile sunucuyu çalıştırmadan uygulamanızı derleyebilirsiniz. Yalnızca `compile` komutunu kullanın:

```bash
[my-first-app] $ compile
```

[[images/consoleCompile.png]]

## İnteraktif konsolu başlatmak

Kodunuzu etkileşimli olarak test etmenizi sağlanyan Scala konsoluna girmek için `console` yazın:

```bash
[my-first-app] $ console
```

Uygulamayı Scala konsolu içinde başlatmak için (örneğin veritabanına bağlanmak için):

```bash
scala> new play.core.StaticApplication(new java.io.File("."))
```

[[images/consoleEval.png]] 

## Hata ayıklama

Konsolu başlatırken, Play'den bir **JPDA** hata ayıklama portu başlatmasını isteyebilirsiniz. Böylece bir Java hata ayıklayıcısıyla uygulamaya bağlanabilirsiniz. Bunu yapmak için `activator -jvm-debug <port>` komutunu kullanın:

```
$ activator -jvm-debug 9999
```

Bir JPDA portu uygun olduğu zaman, JVM açılışta bunun kaydını tutar:

```
Listening for transport dt_socket at address: 9999
```

## sbt özelliklerini kullanmak

Play konsolu aslında tipik bit sbt konsoludur. Yani **triggered execution** gibi sbt özelliklerini kullanabilirsiniz.

Örneğin, `~ compile`'ı kullanmak

```bash
[my-first-app] $ ~ compile
```

Bir kaynak dosyasını her değiştirişinizde derleme tetiklenecektir.

`~ run`'ı kullanıyorsanız

```bash
[my-first-app] $ ~ run
```

Bir geliştirme sunucusu çalışırken tetiklenen derleme etkinleştirilecektir.

Bir kaynak dosyasını her değiştirdiğinizde projenizi devamlı olarak test etmek için aynı şeyi `~ test` ile yapabilirsiniz:

```bash
[my-first-app] $ ~ test
```

## Play komutlarını doğrudan kullanmak

Play konsoluna girmeden, doğrudan da komutları çalıştrabilirsiniz. Örneğin, `activator run` komutunu girin:

```bash
$ activator run
[info] Loading project definition from /Users/jroper/tmp/my-first-app/project
[info] Set current project to my-first-app (in build file:/Users/jroper/tmp/my-first-app/)

--- (Running the application from SBT, auto-reloading is enabled) ---

[info] play - Listening for HTTP on /0:0:0:0:0:0:0:0:9000

(Server started, use Ctrl+D to stop and go back to the console...)
```

Uygulama hemen başlar. `Ctrl+D` tuşlarını kullanarak çıktığınızda işletim sistemi konsoluna düşersiniz.

> **Sonraki:** [[Tercih ettiğiniz IDE'yi kurmak | IDE]]
