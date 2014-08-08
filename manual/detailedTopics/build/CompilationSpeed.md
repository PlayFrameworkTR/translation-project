<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Derleme Süresini İyileştirmek

Derleme hızı aynı zamanda iyi birer mühendislik uygulaması olan aşağıdaki yönergeleri izleyerek iyileştirilebilir:

## Alt projeler kullanmak / modülleştirmek

Bu, modülleştirmenin diğer yararlarının yanında artırarak derleme için bölmeler gibidir. Devirlerin boyutunu en aza indirir, iç bağımlılıkları belirtik kılar ve istendiğinde kodun bir alt kümesiyle çalışmanıza olanak sağlar. Bu Aynı zamanda sbt'nin bağımsız modülleri paralel olarak derlemesine de izin verir.

## Public metodların geri dönüş türünü açıklamak

Bu, tür çıkarımına ihtiyacı azaltarak derlemeyi hızlandırır ve kesinlik için, artırarak derlemede ortaya çıkabilecek kaynak dosya sınırları içinde oluşabilecek köşe durumları tespit etmeye yardım eder. 

## Kaynak dosyaları arasında büyük devrelerden kaçının

Devirler daha büyük yeniden derlemeler ve/veya aşamalarla sonuçlanma eğilimindedir. SBT 0.13.0 (Play 2.2) ve sonrasında, bu daha önemsiz bir problemdir.

## Kalıtımı en aza indirin

Bir kaynak dosyasındaki public bir API değişimi ondan türeyen her şeyin yeniden derlenmesine yol açar.
