# Uygulama Gizli Anahtarı

Play şunları da içeren bazı şeyler için bir gizli anahtar kullanır:

* Oturum çerezlerini ve CSRF jetonlarını imzalamak
* Yerleşik şifreleme araçları

Uygulama gizli anahtarı `application.conf` dosyasında yapılandırılır, `application.secret` özellik adına sahiptir ve varsayılanı `changeme`'dir. Varsayılanın tavsiye ettiği gibi, üretimde bu anahtar değiştirilmelidir.


> Üretim modunda başlatıldığında, Play anahtarın ayarlanmadığını ya da `changeme`'ye ayarlandığını fark ederse, bir hata fırlatacaktır.

## En iyi uygulamalar

Gizli anahtarınıza erişim sağlayan herkes istediği her oturumu yaratabilecektir ve bu onların sisteminize istedikleri her kullanıcı ile giriş yapabilmelerini sağlar. Bundan dolayı, gizli anahtarınızı kaynak kontrolü sistemlerine kayıt etmemeniz şiddetle tavsiye edilir. Aksine, bu anahtar sunucunuzda yapılandırılmalıdır. Bu demektir ki bir üretim uygulamasının gizli anahtarını `application.conf` içine yerleştirmek kötü bir uygulamadır.

Bir üretim sunucusunda uygulama anahtarını yapılandırmanın bir yolu, anahtarı bir sistem özelliği olarak başlangıç betiğinizde geçirmektir. Örneğin:

```bash
/path/to/yourapp/bin/yourapp -Dapplication.secret="QCY?tAnfk?aZ?iwrNwnxIlR6CTf:G3gf:90Latabg@5241AB`R5W:1uDFN];Ik@n"
```

Bu yaklaşım çok basittir ve bunu Play dokümantasyonunun uygulamanızı üretim modunda çalıştırma kısmında gizli anahtarın ayarlanması gerektiğine bir hatırlatma olarak kullanacağız. Ancak, bazı ortamlarda komut satırında gizli anahtarları geçirmek iyi bir uygulama olarak görülmez. Bunu değiştirmenin iki yolu vardır.


### Ortam değişkenleri

İlki, gizli anahtarı bir ortam değişkenine yerleştirmektir. Bu durumda, aşağıdaki yapılandırmayı `application.conf` dosyanıza yerleştirmenizi tavsiye ediyoruz:

    application.secret="changeme"
    application.secret=${?APPLICATION_SECRET}

Bu yapılandırma kümesindeki ikinci satır gizli anahtarı, eğer mevcutsa `APPLICATION_SECRET` adında bir değişkenden gelecek şekilde ayarlar. Aksi taktirde anahtarı önceki satırdaki haliyle, değiştirmeden bırakır.

Bu yaklaşım özellikle normal uygulamanın, parolaları bulut sağlayıcısının API'larıyla yapılandırılabilen ortam değişkenleriyle ayarlamak olduğu bulut tabanlı dağıtım senaryoları için iyi çalışır.

### Üretim yapılandırma dosyası

Başka bir yaklaşım da sunucuda yaşayan ve `application.conf`'u kendisine dahil eden ama aynı zamanda uygulama gizli anahtarı ya da parolalar gibi bütün hassas yapılandırmaların üzerine yazan bir `production.conf` dosyası yaratmaktır. 

Örneğin:

    include "application"

    application.secret="QCY?tAnfk?aZ?iwrNwnxIlR6CTf:G3gf:90Latabg@5241AB`R5W:1uDFN];Ik@n"

Böylece Play'i bununla başlatabilirsiniz:

```bash
/path/to/yourapp/bin/yourapp -Dconfig.file=/path/to/production.conf
```

## Bir uygulama gizli anahtarı üretmek

Play, yeni bir gizli anahtar üretmek için kullanabileceğiniz bir araç sağlar. Play konsolunda `play-generate-secret` komutunu çalıştırın. Bu, uygulamanızda kullanabileceğiniz yeni bir anahtar üretir. Örneğin:

```
[my-first-app] $ play-generate-secret
[info] Generated new secret: QCYtAnfkaZiwrNwnxIlR6CTfG3gf90Latabg5241ABR5W1uDFNIkn
[success] Total time: 0 s, completed 28/03/2014 2:26:09 PM
```

## application.conf dosyasındaki gizli anahtarı güncellemek

Play aynı zamanda, eğer bir gizli anahtarın geliştirme ya da test sunucuları için yapılandırılmasını isterseniz, `application.conf`'taki gizli anahtarı güncellemek için de bir araç sağlar. Bu çoğu zaman, uygulama gizli anahtarıyla şifrelenmiş verileriniz varsa ve geliştirme modunda uygulamanın daima aynı anahtar ile çalışacağından emin olmak için yararlıdır.

`application.conf` dosyasındaki gizli anahtarı güncellemek için, Play konsolunda `play-update-secret` komutunu çalıştırın: 

```
[my-first-app] $ play-update-secret
[info] Generated new secret: B4FvQWnTp718vr6AHyvdGlrHBGNcvuM4y3jUeRCgXxIwBZIbt
[info] Updating application secret in /Users/jroper/tmp/my-first-app/conf/application.conf
[info] Replacing old application secret: application.secret="changeme"
[success] Total time: 0 s, completed 28/03/2014 2:36:54 PM
```

> **Sonraki:** [[JDBC bağlantı havuzunu yapılandırmak|SettingsJDBC]]
