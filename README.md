# HyperLogLog (HLL) - Cardinality Estimation in Java

Bu proje, Büyük Veri Analitiği dersi kapsamında "Cardinality Estimation" (Küme Büyüklüğü Tahmini) problemini çözmek için **HyperLogLog (HLL)** olasılıksal veri yapısının Java ile sıfırdan geliştirilmiş bir gerçeklemesidir. Herhangi bir dış kütüphane kullanılmadan tasarlanmıştır.

## 🚀 Özellikler

* **Yüksek Kaliteli Hashing:** Düşük entropili girdilerde bile bit dağılımını düzgün sağlamak için FNV-1a tabanlı ve **MurmurHash3** (32-bit avalanche mix) finalizasyonuna sahip özel hash fonksiyonu.
* **Kovalama (Bucketing) & Register Mimarisi:** Veriyi $m = 2^b$ adet alt kümeye ayıran ve her kovadaki maksimum ardışık sıfır (leading zeros) sayısını $\rho$ takip eden yapı.
* **Hata Düzeltme Algoritmaları:** * Küçük veri setleri için **Linear Counting** (Doğrusal Sayım) düzeltmesi ($E \le 2.5m$).
    * Büyük veri setleri için 32-bit taşma düzeltmesi.
* **Birleştirilebilirlik (Merge):** İki farklı HLL yapısının (`hllA` ve `hllB`), veri kaybı olmadan $O(m)$ karmaşıklığında tek bir HLL yapısında birleştirilebilmesi (`hllA.merge(hllB)`).

## 🧠 Matematiksel Altyapı ve Teorik Analiz

HyperLogLog, nihai tahmini hesaplarken aykırı değerlerin etkisini kırmak için **Harmonik Ortalama** kullanır:

$$E = \alpha_m \cdot m^2 \cdot \left( \sum_{j=0}^{m-1} 2^{-M[j]} \right)^{-1}$$

**Kova Sayısının ($m$) Hata Payına Etkisi:**
Algoritmanın standart hata sapması, kova sayısının karekökü ile ters orantılıdır:

$$SE \approx \frac{1.04}{\sqrt{m}}$$

Program içerisindeki Test-4'te bu durum ampirik olarak kanıtlanmıştır. $b$ (kova bit sayısı) artırıldıkça $m$ değeri eksponansiyel olarak büyür ve buna bağlı olarak hata oranı %3 seviyelerinden %0.4 seviyelerine kadar düşer.

## 🤖 Agentic Kodlama (LLM İş Akışı)

Bu proje geliştirilirken **Agentic Kodlama** yaklaşımı benimsenmiş ve süreçte Büyük Dil Modelleri (Gemini) kullanılmıştır.
1.  **İskelet Tasarımı:** İlk etapta HLL'nin OOP prensiplerine uygun sınıf yapısı ve gerekli metodlar (add, count, merge) belirlendi.
2.  **Bileşen Üretimi:** "Dış kütüphane kullanmadan MurmurHash3 avalanche etkisini Java'da nasıl simüle edebilirim?" gibi spesifik promptlarla model yönlendirildi.
3.  **Hata Ayıklama & Optimizasyon:** Linear Counting sınır durumları (edge cases) ve bit kaydırma (`>>>`, `<<`) operasyonlarındaki tip dönüşümleri iteratif testlerle doğrulanarak koda entegre edildi.

## 💻 Kullanım ve Test

Projeyi çalıştırmak için `HyperLogLog.java` dosyasını derleyip çalıştırmanız yeterlidir. Dosya içerisindeki `main` metodu 4 farklı test senaryosu içermektedir:

1.  Temel ekleme ve sayım testi.
2.  Büyük veri seti (200.000 eleman) doğruluk ve hata payı testi.
3.  Kesişimli kümeler üzerinde `merge()` (Birleştirme) fonksiyonu testi.
4.  Kova bit sayısı ($b$) artışının tahmin hatası üzerindeki etkisinin iteratif analizi.
