<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Play Kurulumu

## Ön koşullar

Play framework'ü çalıştırmak için [JDK 6 ya da sonrası](http://www.oracle.com/technetwork/java/javase/downloads/index.html) gereklidir.

> Eğer MacOS kullanıyorsanız Java yerleşik olarak gelir. Linux kullanıyorsanız Sun JDK ya da OpenJDK kullandığınızdan (çoğu Linux dağıtımında varsayılan olarak gelen gcj kullanmadığınızdan) emin olun. Windows kullanıyorsanız güncel JDK paketini indirin ve kurun.

`java` ve `javac` komutlarının path'inizde olduğundan emin olun (bunu doğrulamak için `java -version` ve `javac -version` komutlarını kullanabilirsiniz).

## Activator kurulumu

Play, [Typesafe Activator](http://typesafe.com/activator) adı verilen bir araç ile dağıtılır. Typesafe Activator Play'in üzerine kurulu oldugu inşa aracını (sbt) ve ayrıca yeni uygulamalar yazarken size yardımcı olacak bir çok şablon ve örneği barındırır.

Güncel [Activator dağıtımını](https://typesafe.com/platform/getstarted) indirin ve arşivi hem okuma **hem de yazma** izniniz olan bir yere açın. (`activator` komutu dağıtım içindeki dizinlere bazı dosyalar yazar. Bu nedenle `/opt`, `/usr/local` gibi yazmak için özel izinlere ihtiyacınız olan yerlere kurmayın.)

## Activator betiğini PATH'inize ekleyin

Kolaylık olması için Activator kurulum dizinini sistem `PATH`'inize eklemelisiniz. UNIX sistemlerde bunu aşağıdaki şekilde yapabilirsiniz:

```bash
export PATH=$PATH:/path/to/activator
```

Windows için global çevre değişkenini setlemelisiniz. Çevre değişkenlerinde `PATH` değişkenini güncelleyin ve içinde boşluk olan bir yol kullanmayın.

> UNIX üzerinde iseniz `activator` betiğinin çalıştırılabilir olduğundan emin olun.
>
> Aksi halde şu komutu çalıştırın:
> ```bash
> chmod a+x /path/to/activator
> ```

> Eğer bir vekil sunucu arkasında iseniz Windows üzerinde `set HTTP_PROXY=http://<host>:<port>` komutu ile ya da UNIX üzerinde `export HTTP_PROXY=http://<host>:<port>` komutu ile tanımlamayı unutmayın.

## Activator komutunun erişilebilir olduğunu doğrulayın

Bir kabuk üzerinden `activator -help` komutunu çalıştırın.

```bash
$ activator -help
```

Eğer her şey düzgün şekilde kurulmuş ise basit yardımı görmelisiniz:

[[images/activator.png]]

Artık bir Play uygulaması oluşturmaya hazırsınız.

> **Sonraki:** [[Yeni bir uygulama oluşturmak | NewApplication]]
