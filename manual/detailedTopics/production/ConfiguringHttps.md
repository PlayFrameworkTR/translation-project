<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# HTTPS'yi Yapılandırmak

Play, HTTPS sunmak üzere yapılandırılabilir. Bunu etkinleştirmek için, `https.port` sistem değişkenini kullanarak Play'e hangi portu dinlemesi gerektiğini söylemeniz yeterlidir. Örneğin:

    ./start -Dhttps.port=9443

## SSL Sertifikaları

### Bir anahtar deposundan SSL sertifikaları

Varsayılan olarak, Play kendi kendine imzaladığı bir sertifika üretecektir. Ancak genellikle bu bir web sitesini yayınlamak için uygun değildir. Play, SSL sertifikaları ve anahtarlarını yapılandırmka için Java anahtar depolarını kullanır.

İmza otoriteleri çoğu zaman nasıl bir Java anahtar deposu (bir Tomcat yapılandırmasına referansla) oluşturulacağını anlatan yönergeler sağlarlar. JDK anahtar aracını kullanarak anahtar depoları üretme üzerine resmi Oracle dokümantasyonu [burada](http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/keytool.html) bulunabilir. Aynı zamanda [[Generating X.509 Certificates|CertificateGeneration]] bölümünde de bir örnek vardır. 

Anahtar deponuzu yarattıysanız, aşağıdaki sistem değişkenleri Play'i deponuzu kullanması için yapılandırmakta kullanılabilir:

* **https.keyStore** - gizli anahtar ve sertifikayı saklayan anahtar deposunun konumu - eğer sağlanmadıysa Play sizin için bir anahtar deposu üretecektir.
* **https.keyStoreType** - anahtar deposu türü, varsayılan olarak `JKS`
* **https.keyStorePassword** - parola, varsayılan olarak boş
* **https.keyStoreAlgorithm** - anahtar deposu algoritması, varsayılan olarak platformun varsayılan algoritması

### Özel bir SSL motorundan SSL sertifikaları

SSL sertifikalarını yapılandırmanın bir alternatifi de özel bir [SSLEngine](http://docs.oracle.com/javase/7/docs/api/javax/net/ssl/SSLEngine.html) sağlamaktır. Bu aynı zamanda, özel istemci kimlik doğrulaması gibi özelleştirimiş bir SSLEngine'in gerektiği durumlarda işe yarar.

#### Java'da, [`play.server.SSLEngineProvider`](api/java/play/server/SSLEngineProvider.html) için bir uygulama sağlanmak zorundadır

@[javaexample](code/java/CustomSSLEngineProvider.java)

#### Scala'da, [`play.server.api.SSLEngineProvider`](api/scala/index.html#play.server.api.SSLEngineProvider) için bir uygulama sağlanmak zorundadır

@[scalaexample](code/scala/CustomSSLEngineProvider.scala)

`play.server.SSLEngineProvider` veya `play.server.api.SSLEngineProvider` uygulamasını hazırladıysanız, aşağıdaki sistem değişkenleri Play'i onu kullanacak şekilde yapılandırmayı sağlar:

* **play.http.sslengineprovider** - `play.server.SSLEngineProvider` or `play.server.api.SSLEngineProvider` sınıfını uygulayan sınıfın konumu

Örnek:

    ./start -Dhttps.port=9443 -Dplay.http.sslengineprovider=mypackage.CustomSSLEngineProvider


## HTTP'yi kapatmak

HTTP portunundaki bağlantıyı devre dışı bırakmak için, `http.port` sistem değişkenini `disabled` yapın. Örneğin: 

    ./start -Dhttp.port=disabled -Dhttps.port=9443 -Dhttps.keyStore=/path/to/keystore -Dhttps.keyStorePassword=changeme

## HTTPS'nin üretim ortamında kullanımı

Play HTTPS'yi üretim ortamında kullanıyorsa, JDK 1.8 ile çalışmalıdır. JDK 1.8 JSSE'yi bir [TLS termination layer](http://blog.ivanristic.com/2014/03/ssl-tls-improvements-in-java-8.html) olarak uygulanabilir kılan yeni özellikler sağlar. Eğer JDK 1.8 kullanılmıyorsa, Play'in önünde bir [[ters vekil|HTTPServer]] kullanmak daha iyi bir kontrol ve HTTPS güvenliği sağlayacaktır.

Eğer Play'i TLS termination layer için kullanmak isterseniz, lütfen aşağıdaki ayarlara dikkat edin:

* **[`SSLParameters.setUseCipherSuiteorder()`](http://docs.oracle.com/javase/8/docs/technotes/guides/security/jsse/JSSERefGuide.html#cipher_suite_preference)** - Sunucu tercihine göre şifre takımını yeniden sıralar.
* **-Djdk.tls.ephemeralDHKeySize=2048** - Bir DH anahtar alışverişinde anahtar boyutunu artırır.
* **-Djdk.tls.rejectClientInitiatedRenegotiation=true** - Tekrar istemci görüşmesini reddeder.

> **Sonraki:** [[Bir bulut hizmetine dağıtım yapmak|DeployingCloud]]
