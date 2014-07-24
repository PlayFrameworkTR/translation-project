<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Play 2'ye Genel Bakış

2007 yılından beri sizlerin daha kolay Java Web Uygulamaları geliştirmeniz için çalışıyoruz. Play aslında ilk olarak Zenexity (http://www.zenexity.com) içinde kendi aramızda ortaya çıkıp ve web uygulama geliştirme metotlarımızdan etkilenip şekillenmiş bir fikirdi. Bizim uygulama geliştirme metotlarımızı merak ediyorsanız: Yazılım geliştiriciler için en verimli yöntemleri seçerek, güncel web mimarisi kurallarına uygun en son teknolojileri kullanmak gibi bir yakşlaşım.

2009 yılında aramızda konuştuğumuz bu fikri açık kaynak kodlu bir proje olarak herkesle paylaşmaya karar verdik. Proje fikrine gelen ilk tepkiler oldukça pozitif ve motive ediciydi. Bugün arkamıza dönüp iki yıllık yoğun geliştirme dönemine baktığımızda, Play artık birçok sürümü olan, aktif 4000 kullanıcının bulunduğu bir topluluğa sahip ve sürekli artan kullanım oranıyla oldukça yaygın ve güçlü bir teknoloji haline gelmeye başladığını görüyoruz.

Yepyeni bir proje geliştirip, onu dünya ile tanıştırmak bize olumlu ve olumsuz pekçok şey katacaktır ama bundan daha önemlisi bizlere yepyeni dünyaların kapılar aralatıyor, bizlerin belkide hiç göremiyeceğimiz kullanıcı senaryolarıyla tanışmamızı, gerçek dünyanın ihtiyaç duyduğu özellikleri daha net görmemizi ve en önemlisi bizim göremediğimiz pekçok bug'ı yani sistem açığını farketmemizi sağlıyor. İşte bu 2 yılımız bunları yaşayarak ve öğrenerek geçti ve bunlardan kazandığımız yetkinlikleri kullanarak Play'i daha fazla geliştirdik, karşılaştığımız yepyeni kullanıcı senaryoları sayesinde gerçek hayat sorunlarına daha uygun hale getirmek için çalıştık. Play bu şekilde gelişip büyürken pekçok yeni ve büyük projedede kullanıldığını gördük ve Play ile neler yapılabileceğini daha net anladık.

Tabi biz bunları yaparken teknoloji ve web gelişimini bir başka deyişle değişimini hızla sürdürmeye devam etti ve ediyor. Bu gelişimin en odak noktasında web yani internet var. Bu yüzden HTML, CSS ve JavaScript gibi teknolojiler oldukça hızlı gelişiyor değişiyorlar. Son zamanlarda web mimarileri gerçek zamanlı işlemlere doğru yönelmeye başladılar ve günümüz ihtiyaçları göz önünde bulundurulduğunda SQL gibi eski ve köklü bir teknolojinin veri depolama konusunda oldukça yetersiz kaldığını kimse inkar edemez. Scala dahil JVM kullanan diller oldukça popülerleşmeye başladı ve hızla gelişmeye devam ediyorlar. 

İşte pekçok şeyi göz önünde bulundurarak bu yeni teknoloji çağına yepyeni özelliklere sahip olarak Play 2'yi kazandırdık.

## Asenkron Programlama ve Play 2

Günümüzde web uygulamalarının çoğu gerçek zamanlı veri kullanarak paralel programlama tabanlı yani aynı anda birden fazla fonksiyonun birbirinden bağımsız çalışabildiği bir çatı altındalar. Bu yüzden web tabanlı frameworklerde artık asenkron programlama desteği olmazsa olmaz özelliklerden biri. Aslında Play de ilk tasarlandığında basit web sorgularına yanıt vermeyi amaçlayan klasik, standart bir frameworktü. Ama gelişen ve değişen Play, şuan WebSocket kullanabilen oldukça gelişmiş bir yapıya ulaştı.

Play 2 tasarlanırken kullanılacak sunucu taraflı request yani sorgulamaların kısa ömürlü değil tam tersi uzun ömürlü olacakları varsayılarak tasarlandı. Tam bu noktada bu uzun ömürlü sorguların en verimli ve etkin şekilde aktarılıması ve bu sorgulara yanıt verilmesi ile ilgili çözümler üzerine yoğunlaşıldı. Aktör Modeline Dayalı Nesneye Yönelik Paralel Programlama bu sorunların çözümü için en iyi yöntem gibi gözüküyordu ve bu model kullanıldı. Bu modelin en iyi uygulanmış canlı örneklerinden biri ise Akka. Play 2 direkt olarak Akka desteğine sahip olarak sizlere geliyor. Play 2, güçlü paralel programlama desteği sayesinde gelişmiş düzeyde dağıtık sistemler geliştirmeye de olanak sağlıyor. 

## Tip Güvenliği ve Play

Statik dillerde her işlem öncesinde hata arama amacıyla tip kontrolü yapılır ve Play'de statik dil kullanmamızın en önemli sebebi ve en büyük yararlarından biride bu tip kontrolü destiğinden yararlanmak istememizdir. Tabiki hataları denetmelemek için tek yöntem tip kontrolü değil ama büyük takımların çalıştığı dev projelerin geliştirilme sürecinin ilk evrelerinde ve devamında oldukça büyük kolaylıklar sağladığı reddedilemez.
 
Play 2'nin tarifine katılan Scala işte bu yüzden bizlere avantaj sağlıyor. Play'in 1.x serisinde olan tema yani şablon alt yapısında dinamik bir dil olan Groovy kullanıyorduk ve tip kontrolü gibi derleyicilerin yapabildiği önemli özellikler malesef yoktu. Bunun sonucu olarak hataların hepsi ancak kodun çalıştırıldığı zaman yani run-time anında tespit edilebiliyordu. Farklı kütüphanaler ya da kodlarla bir arada çalışırken yani Glue code seviyesinde çalışırkende aynı sorunlar ortaya çıkıyor

İşte bu yüzden 2.0 versiyonunda tüm bu sorunları göz önünde bulundurarak Play'e derleme anında kontrol edebilme özelliğini kattık. Bunun içinde Scala tabanlı bir tema şablon altyapısı kurgulamaya karar verdik. Aslında Play 2.0 da kullanılan ana programlama dili Java ama tema tarafında birazcık da olsa Scala mevcut. Scala dediysekte korkmanıza gerek yok, çok temel birkaç şeyle hiç zorlanmadan üstesinden gelebileceğiniz seviyede herşey. Hatırlarsınızki 1.x serisinde Groovy dilini öğrenip üzerinde çalışmanız gerekiyordu.

Tema altyapısında yani şablonlarda Scala sizin Java tarafında yarattığınız objelerin görsel bir hal almasını sağlayan ve Java'ya oldukça yakın bir syntax yani yazım tarzı ile bize destek oluyor. Yinede Scala ileri seviye fonksiyonel ve expression tabanlı programlama altyapısı ile gelişmiş tasarımlar yapabilmenize olanak sağlayan harika bir altyapı.

Ayrıca sadece şablon motoru olarak değil yönlendirme ve site içi navigasyon gibi işlemlerde yani proje içindeki routing de güçlü bir tip kontrolünden geçiyor. Play 2 de bütün route dosyaları ve tanımlamaları kontrolden geçiyor ve hepsinin tutarlı olup olmadığı onaylanıyor.

Baştan sona herşeyin derlenip öyle kullanılabildiği sistemlerinde en güzel yanlarından biri temadan route dosyalarına kadar herşeyin daha kolay paketlenip kullanılabilir olmasıdır. Ayrıca çalışma anındaki performans artışı gözle görülür seviyede yüksek olmasıda önemli bir artı.

## Java ve Scala Desteği

Scala'yı kullanma fikri aslında Play projesini ilk geliştirdiğimiz zamanlarda bile aklımıza olan ve ufak ufak denediğimiz bir olaydı.İlk zamanlarda framework'e zarar vermemesi için deneme amacıyla ek bir modül olarak Scala'yı kullandık.

Aslına bakarsanız Java tabanlı bir framework'e Scala entegre etmek çok garipsenecek bir konuda değil. Çünkü Scala dilinin Java ile uyumluluğuda göz önünde bulundurulunca herkesin kolaylıkla alışıp birlikte kullanabileceği bir sistem ortaya çıkıyor. Bir dili verimli ve etkili kullanmanın en iyi yolu  başka bir dilin içinde kullanmak değil tabikide ama birlikte olan uyumlarınıda göz önünde bulundurmak gerekir. Scala nesne yönelimli programlamayı fonksiyonel programlama sistemiyle destekleyen ilginç bir karışım. Bu yüzden Scala'nın tüm özelliklerini kullanabilmek adına bütün frameworkleri ve API'lerini detaylıca inceleyerek başlamıştık bu fikre.

Scala'yı eklenti gibi kullanarak başladığımız yolda çok kısa bir süre sonra bu şekilde kullanırsak yapacaklarımızın sınırının limitine çabuk ulaşacağımızı anladık. Play'in ilk sürümleri olan 1.x serisinde Java Reflection API yani Türkçesiyle Java Yansıtma Kütüphanesi ve byte seviyesindeki kodları işleme gibi teknikler kullanırken aslında işleri ne kadar zorlaştırdığımızı ve Play framework'ü için süreci biraz yavaşlattığımızı farkettik. Bu süreçler sürerken bir yandanda Scala ile tema tip kontrol sistemi ve SQL erişim bileşeni olan Anorm gibi mükemmel modüller ve eklentiler yazıyorduk. İşte bu yüzden artık Scala'nın gücüne güvenip Play 2 ile birlikte olmazsa olmazlarımızdan birinin Scala olmasına karar verdik.

Öte yandan Java'ya olan sevgimiz ve önemimizde en ufak bir azalmada olmadı. Play 2 ile birlikte Java geliştiricilerin kendilerine biraz daha Java kodlama deneyimi katma şansıda oldukça fazla. Yani bir Java geliştirici için her özelliğe sahip gerçek bir Java kütüphanesi yarattık ve gerisini size bıraktık.

## Güçlü Derleyici Altyapısı

Yola çıktığımız ilk günden bu yana Play projelerini çalıştırmak için basit ve net bir yol izledik: Derle ve çalıştır. İlk bakışta bu yol garip gelebilir ama standart Servlet API'si yerine asenkron programlamaya olanak sağlayan HTTP API'yi kullanabilmemizi sağlaması, kodumuzu derleyip çalıştırma gibi süreçlerde hızlı geri dönüş alabilmemizi sağlaması ve kolay, verimli paketleme özellikleri sayesinde bu method harika bir seçim oldu. Kısacası artık Play'in standart JEE kurallarıyla çalışmasını beklemek çok saçma olurdu.

Günümüzdede bu yazılım geliştirip derleme yaklaşımı Java geliştiricileri arasındada popüler olmaya başladı. Ama bu yapıyı seçmemiz aslında pekçok platformda Play'i kullanabilmemize olanak sağladı. Örneğin Java uygulamalarının geleceği gibi görülen Heroku gibi elastik Paas platformlarında bile bu sayede çalışabilecek.

İşte bunları göz önüne alarak baktığımızda varolan Java derleyici altyapısı bizim kullandığımız ve kullanacağımız bu yeni yöntemleri desteklemek için yeterli değildi. Bu yüzden 1.x serisinde yapmak istediğimiz fakat Java altyapısının izin vermediği şeylerde Python scriptler yazarak bir kısmını yapmaya çalıştık.

Bu kullanımlarımızı o sıralarda Play'i ticari büyük projelerde kullanıp varolan projelerini ona geçiren grupların derleyici tabanlı konularda biraz sıkıntı çekmesine sebep oldu. 1.x serisinde kullandığımız Python scriptler tam bir derleyici özelliğine sahip ve ihtiyaçlarımıza göre kolay düzenlenebilir yapılar değildi. İşte tüm bu sorunlar birleşince Play 2 için çok güçlü bir derleyici sistemi yapmamız kaçınılmazdı ve bu yüzden bu konuya çok fazla odaklandık.

Bu noktada Java ve Scala projelerimizi derleyip, istediğimiz esneklikte ve ihtiyaçlarımıza göre düzenlenebilirlik göz önünde bulundurularak Play 2 de sbt'yi kullanmaya karar verdik. Tabiki bu değişim önceki derleyici sistemini kullanan Play kullanıcılarını korkutmamalı. Play'in en önemli özelliği olan `play new`, `run`, `start` yani projeyi yaratıp çalıştır diyip  başlatabildiğimiz o basitliği ortadan kaldırmıyoruz tam aksine güçlendiriyor bir adım öteye taşıyoruz. Play 2 en iyi ayarlamaları yapılmış şekilde yapılandırmaları yapılmış olarak geliyor artık sizlere. Öte yandan Play 2 ile birlikte hazır tanımlanmış olarak gelen yapılandırmaları proje ve uygulamalarınıza göre yeniden düzenleyebilir, sbt'nin size sunduğu bütün güzelliklerden yararlanabilirsiniz.

Tüm bunları birleştirip herşeye büyük pencereden baktığımızda gerçekten Maven projeler için entegrasyonu oldukça kolay, projelerinizi JAR olarak kaydedip kolayca paketleyip paylaşabileceğiniz, derleyip sonrasında tekrar yükleme ve geliştirme sürecinde bizzat yaşayabileceğiniz, herşeyin öteisinde bunların hepsini standart Java ya da Scala projeleriyle yapabileceğiniz bir harika olarak geliyor Play 2.

## Veri depolama ve model entegrasyonu

Hepimiz artık veri depolama dendiğinde SQL veritabanlarının eskisi gibi tüm ihtiyaçları gideren güçlü veri depoları olmadığını biliyoruz. Gelişen ve değişen ihtiyaçlara göre pekçok yeni ve değişik veri depolama modelleri gelişmeye ve yaygınlaşmaya devam ediyor. Bu yüzden web tabanlı frameworklerde kesin ve net bir veritabanı modeli üzerine odaklanmak hem geliştiriceleri sınırlandırmak hemde yanlış bir yaklaşım olacağından oldukça zor bir yöntem. İşte bunlar düşünüldüğünde sadece bir web API'sine tüm bu teknolojileri sığdırmanın imkanı olmadığını ve Play içinde bunun çok zor olduğunu hepimiz biliyoruz.

Play 2'de herşeyi göz önünde bulundurarak herkesin istediği veri depolama yöntemini kolayca kullanabildiği, ORM yani Nesne-İlişkisel Eşleme methodlarının kullanabildiği ya da istediği bir veritabanı erişimi olan kütüphaneye bağlanabildiği bir sistem olmasını istedik. Herkesin karşılaştığı yaygın olan pekçok sorunu basitçe çözebilmeniz için küçük özellikler eklemekte istiyoruz. Bunuda ötesnde veritabanını baştan sona bakımını yapıp devam ettirebilmeniz için klasik veritabanlarında kullanılabilen bizim sizin için Play'in içine hazır olarak koyuyoruz. İşte tüm bu veritabanı olaylarını yapabilmeniz ve rahat yönetebilmeniz açısından Play'le birlikte hazır olarak Ebean, JPA ve Anorm gibi hazır kütüphaneler hazır kurulu geliyor.

