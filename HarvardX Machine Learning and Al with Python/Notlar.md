# Decision trees

## 1. video

Karar ağacı, hem sınıflandırma hem de regresyon için kullanılabilen yaygın bir makine öğrenmesi algoritmasıdır. Bu, gözetimli öğrenmeye dayalı bir algoritmadır ve her iç düğümün bir özellik veya öznitelik, her dalın bir karar ve her yaprak düğümün bir sonuç veya tahmin temsil ettiği ağaç benzeri bir yapıya dayanır.

Karar ağacı algoritmaları, en fazla bilgi kazancını sağlayan özelliğe göre veriyi yinelemeli olarak alt kümelere ayırarak çalışır. Veriyi ayırmak için en iyi özelliğin seçilmesi ve alt kümelere bölme işlemine "bölme süreci" denir.

Karar ağaçlarının en önemli avantajlarından biri, kolay yorumlanabilir ve anlaşılır olmalarıdır. Aşırı öğrenmeyi (overfitting) önlemek için budama (pruning) gibi teknikler veya rastgele orman (random forest) ve gradyan artırma (gradient boosting) gibi topluluk yöntemleri (ensemble methods) kullanılır. Topluluk yöntemleri, birden fazla karar ağacını birleştirerek daha sağlam (güçlü) bir model üretir.

## 2. video

İlk bölümde, basit karar ağaçları kullanarak sınıflandırma konusunu ele aldık.

**Kapsamımız şu şekilde:**
İlk olarak **neden bu yönteme ihtiyaç duyduğumuzdan** bahsediyoruz.
Sonra **karar ağaçlarının nasıl oluşturulduğuna dair sezgiyi**,
karar ağaçlarının sınıflandırma için nasıl kullanıldığını,
**bölme kriterlerini** ve son olarak da
**bölmeyi ne zaman durdurmamız gerektiğini** konuşuyoruz.

En basit ve en doğrudan sınıflandırma modellerinden biri, **lojistik regresyondur**.
Lojistik regresyon en iyi şu iki durumda çalışır:
**A)** Sınıflar, özellikler uzayında (feature space) açık bir şekilde ayrılmışsa.
Verilen örnekte, enlem-boylam grafiği üzerinde tarım arazileri yeşil noktalarla, kurak araziler beyaz noktalarla temsil edilmiştir. Burada yeşil noktalar açıkça beyazlardan ayrılmaktadır.
**B)** Sınıflandırma sınırı düzgün bir geometrik şekle sahipse.

**Sınıflandırma sınırı** ya da **karar sınırı**, sınıf 1 ve sınıf 0 olasılıklarının eşit olduğu yerde tanımlanır.
Yani:
P(Y = 1) = P(Y = 0) = 0.5
Bu da log-odds'un sıfır olduğu duruma karşılık gelir ve şu şekilde ifade edilir:
 **x \* β = 0**
Burada β, modelin katsayılarını, x ise özellik (feature) değerlerini temsil eder (örneğin enlem ve boylam).
Bu denklem bir doğru ya da hiper düzlem tanımlar.
Bu yaklaşım, daha karmaşık sınırları temsil etmek için polinom terimlerle genişletilebilir.
Örneğin, karar sınırını belirleyen denklem:
 **enlem = 0.8 × boylam + 0.1**
Bu basit bir doğrusal denklem.

Bu yöntem, karar sınırları düzgün geometrik yapılar gösterdiğinde iyi çalışır.
Ancak, aşağıdaki örneklerde olduğu gibi,
sınıflar yine açıkça ayrılmış olsa bile
bu sınırlar tek bir denklemle kolayca tanımlanamaz.

Doğrusal karar sınırlarına sahip lojistik regresyon modelleri, her bir girdinin (özelliğin) sınıflandırma üzerindeki etkisinin yorumlanması açısından sezgiseldir.
Ancak, **doğrusal olmayan sınırlar** yorum açısından daha zordur.
Örneğin, polinomsal lojistik regresyon ile,
 **x₁² + x₂² - 0.25 = 0**
şeklinde bir sınır çizilebilir;
fakat bu sınırın girdilere etkisini yorumlamak kolay değildir.

Bu durum bizi farklı bir modelleme yöntemine götürür.
Başlamadan önce bir **istek listesi** oluşturalım:

1. Karmaşık karar sınırlarını modelleyebilen,
2. Aynı zamanda yorumlaması kolay olan modeller istiyoruz.

Bu ihtiyacı günlük hayattan örnekle açıklayalım.
Hayatın her alanında insanlar uzun süredir
nesneleri ya da durumları ayırt etmek için anlaşılır karar modelleri kullanıyor.

**Bir mühendisin düşünme tarzını inceleyelim:**
Bir mühendislik akış şeması:

* Hareket ediyor mu?
   - Evet → Hareket etmeli mi?
    - Evet → Sorun yok.
    - Hayır → Düzelt: Bantla sar.
* Hayır → Hareket etmeli mi?
    - Evet → Düzelt: Çekiçle vur.
    - Hayır → Sorun yok.

Bu basit akış şeması aslında matematiksel bir sınıflandırma modeli olarak formüle edilebilir.
Ve bu model istediğimiz özelliklere sahiptir:

* İnsanlar tarafından kolayca yorumlanabilir.
* Yeterince karmaşık karar sınırları oluşturabilir.
* Karar sınırlarının her bir bileşeni **yerel olarak doğrusaldır** – yani her adımda basit kararlar alınır.

---

**Şimdi sezgisel yaklaşıma geçiyoruz.**
Bu bölümde, bu karar alma fikrini (örneğin mühendisin akış şeması gibi)
veriyle nasıl modele dönüştürdüğümüzü anlatacağız.

Bir örnekle açıklayalım:
Meyveleri **limon** ve **portakal** olarak sınıflandıran bir karar ağacı modeli.

**Ağaç yapısı şu şekilde:**

* İlk olarak yükseklik > 6.5 mi?
   - Evet → genişlik > 9.5 mi?
    - Evet → **Limon**
    - Hayır → **Portakal**
* Hayır → genişlik > 6.0 mı?
    - Evet → **Limon**
    - Hayır → **Portakal**

Burada:

* En üstteki karar noktası **kök düğüm (root node)**,
* Aradaki karar noktaları **iç düğüm (internal node)**,
* Sonuçların bulunduğu noktalar **yaprak düğüm (leaf node)** olarak adlandırılır.

Her karar ağacı, özellikler uzayında eksenlere paralel çizgilerle bir bölme (partition) oluşturur.
Tersi de geçerlidir: Her böyle bölme, bir karar ağacı olarak yazılabilir.

Şekilde, yükseklik-genişlik düzleminde örnekler gösterilmiştir.

* Sarılar limonları,
* Turuncular portakalları temsil eder.

Veriyi önce yüksekliğe göre bölüyoruz:

* 6.5'ten büyükse bir bölge, küçükse başka bir bölge.
  Sonra genişliğe göre yeniden bölüyoruz.
* Bir bölgede çoğunluk limonsa, o bölgedeki her örneğe limon deriz.
* Aynısı portakal için geçerli.

Sonuçta elde edilen karar sınırı **kırmızıyla** gösterilmiştir.
Görüldüğü gibi, bu karar sınırı **doğrusal değildir** ve daha karmaşık hale getirilebilir.

---

**Şimdi sınıflandırma ağacını tahmin yapmak için nasıl kullanacağımızı görelim.**

Daha önce oluşturduğumuz karar ağacını kullanarak
bir meyvenin limon mu portakal mı olduğunu tahmin edeceğiz.
Verilen değerler:

* Yükseklik = 5.9
* Genişlik = 5.8

Karar ağacında kökten başlıyoruz:

* Yükseklik > 6.5 mi?
   - Hayır → sola git
* Genişlik > 6.0 mı?
   - Hayır → sola git
* Son düğümde: **Portakal**

Bu, o bölgede eğitim verilerinin çoğunun portakal olduğunu gösterir.
Bu yönteme **ağaç üzerinde gezinme (tree traversal)** denir.





#önceki notlar

En bilindik olanı Logistic Regression'dır.

Logistic Regression şu durumlarda iyi davranır;

- Eğer veriler iyi bir şekilde ayrık duruyorsa
- Eğer ayrıldıkları alanlar iyi bir geometrik şekle sahipse. 

![image](./images/dtgeoshape.png)

Sınıflandırma Sınırları veya Karar Sınırları, sınıf 1 ve sınıf 0'da olma olasılıklarının eşit olduğu durumlarda tanımlanır, yani

$$ P(Y = 1) = P(Y = 0) = 1 - P(Y = 1) = 0,5 $$

Log-odds sıfır olduğunda eşdeğerdir:

$$ X_\beta = 0 $$

Bu eşitlik çizgi veya çoklu düzlemler tanımlar.

Örneğin, şekilde mavi bir çizgi ile gösterilen karar sınırını tanımlayan denklem:

![image](./images/dtblueline.png)

Bu, sınıflandırma sınırı güzel ve basit bir geometriye sahip olduğunda iyi çalışır... peki ya bunlar?

![image](./images/dtchaos.png)
![image](./images/dtchaos2.png)

Bu tür veri kümelerinde sınıflar özellik uzayında hala iyi bir şekilde ayrılmıştır, ancak karar sınırları tek bir denklemle kolayca tanımlanamaz.


```Python

```

![image](./images/.png)

### 
## 3.3
# 4