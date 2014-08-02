<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Uygulamanızı üretim modunda başlatmak

Daha öncesinde bir Play uygulamasını geliştirme ortamında nasıl başlatabileceğimizi görmüştük. Ne var ki `run` komutu bir uygulamayı üretim ortamında çalıştırmak için kullanılmamalıdır. `run` kullanılırken, her istekte, Play yeniden SBT ile herhangi bir dosyada değişiklik olup olmadığını kontrol eder ve bu işlemlerin uygulamanızın performansı üzerine önemli etkileri olabilir.

Bir Play uygulamasını üretim modunda dağıtmanın birçok yolu vardır. Yerel bir Play kurulumuyla, en basit yol ile başlayalım.

## Uygulama gizli anahtarı

Uygulamanızı üretim modunda çalıştırmadan önce, bir gizli anahtar üretmeye ihtiyaç duyacaksınız. Bunun nasıl yapılacağı hakkında ayrıntılı bilgi için [[Uygulama gizli anahtarını yapılandırmak|ApplicationSecret]] sayfasını ziyaret edin. Aşağıdaki örneklerde, `-Dapplication.secret=abcdefghijk` kullanımını göreceksiniz. Burada kendi gizli anahtarınızı üretmeli ve kullanmalısınız.

## Start komutunu kullanmak

Bir Play uygulamasını üretim modunda başlatmanın en kolay yolu Play konsolunda `start` komutunu kullanmaktır. Bu, sunucuda bir Play kurulumu gerektirir.

```bash
[my-first-app] $ start -Dapplication.secret=abcdefghijk
```


[[images/start.png]]

`start` komutunu çalıştırdığınızda, Play yeni bir JVM ayırır ve varsayılan Netty HTTP sunucusunu çalıştırır. Standart çıktı akımı Play konsoluna yönlendirilir, böylece uygulamanın durumunu buradan izleyebilirsiniz.

Sunucunun process id'si başlangıç sırasında görüntülenir ve `RUNNING_PID` dosyasına yazılır. Çalışan bir Play sunucusunu kurdurmak için o işleme `SIGTERM` mesajını göndermek yeterlidir. Böylece uygulama düzgün bir şekilde kapatılır.

Eğer `Ctrl+D` tuşlarını kullanırsanız, Play konsolu çıkacaktır ancak daha önce yaratılmış sunucu işlemi arka planda çalışmaya devam edecektir. JVM'in varsayılan çıktı akımı kapanacaktır ve kayıtlar `logs/application.log` dosyasından okunabilir.

Eğer `Ctrl+C` tuşlarını kullanırsanız, her iki JVM'i de kapatmış olursunuz: Play konsolu ve de Play ayrılan sunucusu.

Aynı zamanda doğrudan işletim sisteminizin konsoluna `activator start` yazarak da sunucuyu doğrudan çalıştırabilirsiniz:

```bash
$ activator start -Dapplication.secret="abcdefghijk"
```

## Stage görevini kullanmak

`start` komutu uygulamayı etkileşimli olarak başlatır, yani insan etkileşimi gereklidir. Uygulamayı sonlandırmak içinse `Ctrl+D` tuşları gereklidir. Bu çözüm otomatikleştirilmiş dağıtım için çok da elverişli değildir.

Uygulamanızı yerinde kullanıma hazırlamak için `stage` görevini kullanabilirsiniz. Bunun için tipik kullanım şu şekildedir:

```bash
$ activator clean stage
```
[[images/stage.png]]

Bu, uygulamanızı temizler ve derler; gerekli bağımlılıkları getirir ve onları `target/universal/stage` dizinine kopyalar. Aynı zamanda `<start>`'ın projenizin adı olduğu bir `bin/<start>` betiği oluşturur. Bu betik Play sunucusunu Unix tipi işletim sistemlerinde; ona karşılık gelen `bat` dosyası da Windows'da çalışır.

Örneğin `my-first-app` projesinin dizininden bir uygulama başlatmak için şunu yapabilirsiniz:

```bash
$ target/universal/stage/bin/my-first-app -Dapplication.secret=abcdefghijk
```

Aynı zamanda komut satırından, bir üretim ortamı için farklı bir yapılandırma dosyası belirtebilirsiniz:

```bash
$ target/universal/stage/bin/my-first-app -Dconfig.file=/full/path/to/conf/application-prod.conf
```

Kullanımın tam açıklaması için başlatma betiğini `-h` seçeneği ile çağırın.

> **Sonraki:** [[Bağımsız bir dağıtım yaratmak|ProductionDist]]
