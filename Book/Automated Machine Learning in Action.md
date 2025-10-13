
# Part 1: Fundamentals of AutoML

Kitabın ilk üç bölümü, AutoML öğrenimine temel oluşturmak amacıyla, makine öğreniminin bazı temel kavramlarını ve modellerini tanıtarak temel yapı taşlarını anlamanıza yardımcı olur.

1. bölümde AutoML’nin genel kavramlarını, ne olduğunu ve genel makine öğrenimiyle olan bağlantısını görmeye başlayacaksınız. Ayrıca AutoML’nin araştırma açısından değerini ve pratik faydalarını öğreneceksiniz.

2. bölüm, bir makine öğrenimi problemini çözmek için kullanılan klasik makine öğrenimi işlem hattını (pipeline) tanıtır.

Yapay zekâ topluluğunda ve ötesinde derin öğrenme modellerinin popülerliği göz önüne alındığında, 3. bölümde üç yaygın model türü üzerinden derin öğrenmenin temel bilgilerini ele alıyoruz. Bu bölümde karmaşık derin öğrenme kavramlarına girmiyoruz; sadece temel yapı taşlarını ve farklı veri türlerinde uygulanan üç klasik modeli tanıtıyoruz.

Eğer makine öğrenimi, derin öğrenme veya bunların Python ile nasıl uygulanacağı konusunda fazla deneyiminiz yoksa, AutoML’nin pratik uygulamalarına geçmeden önce mutlaka 1. bölümü baştan sona okumanız tavsiye edilir.

Ayrıca bu bölümün ardından, ek B kısmında temel makine öğrenimi işlem hattına daha aşina olmanıza yardımcı olacak ek örnekler de bulabilirsiniz.

## 1. From machine learning to automated machine learning

Yapay zekâ (YZ), günlük yaşamın birçok yönüne ulaşan bir alan olarak, son yıllarda kapsamlı bir şekilde araştırılmaktadır. YZ, bilgisayarların tıpkı insanlar gibi çevreyi algılayarak görevleri otomatikleştirmesini sağlamaya çalışır.

YZ’nin bir alt dalı olan **makine öğrenimi (ML)**, bilgisayarın verileri kendi kendine keşfederek bir görevi yerine getirmesini sağlar. Bu sayede bilgisayar, yalnızca kendisine açıkça ne yapması gerektiği söylenmiş görevleri değil, kendi öğrendikleriyle daha fazlasını da yapabilir.

Ancak, bu alana girmek oldukça zordur: kullanılan teknikleri öğrenmenin maliyeti ve uygulamalarda gerekli deneyimi kazanmanın güçlüğü nedeniyle, yeterli uzmanlığa sahip olmayan kişiler ML’yi kolayca kullanamazlar. Bu nedenle, makine öğrenimi tekniklerini “fildişi kulelerinden” çıkarıp daha fazla insana erişilebilir hale getirmek hem araştırma dünyasının hem de endüstrinin önemli bir hedefi hâline gelmiştir.

Bu amaç doğrultusunda, **otomatik makine öğrenimi (AutoML)** öne çıkan bir araştırma alanı olarak ortaya çıkmıştır. AutoML’nin hedefi, insan uzmanların makine öğrenimi problemlerini nasıl çözdüklerini taklit ederek belirli bir problem için **en uygun ML çözümlerini otomatik olarak keşfetmek** ve böylece derin teknik bilgiye sahip olmayan kişilerin de kullanabileceği hazır ML teknikleri sunmaktır.

AutoML yalnızca yeni başlayanlar için faydalı değildir; aynı zamanda uzmanların ve veri bilimcilerin ML modellerini tasarlama ve yapılandırma yükünü de hafifletir.

Henüz yeni ve ileri düzey bir konu olduğu için çoğu insan için yabancıdır ve mevcut yetenekleri genellikle kitle iletişim araçları tarafından abartılı şekilde sunulmaktadır.

Bu bölümde AutoML’nin ne olduğuna kısa bir bakış sunulmakta, temel kavramları tanıtılmakta ve araştırma değeri ile pratik faydalarına giriş yapılmaktadır.

Haydi, basit bir örnekle başlayalım.


### 1.1 A glimpse of automated machine learning – 4

Diyelim ki, el yazısı ile yazılmış rakamları resimlerden tanıyacak bir makine öğrenimi (ML) modeli tasarlamak istiyorsunuz.
Bu ML modeli, giriş olarak resimleri alacak ve her bir resimdeki rakamı çıktı olarak üretecektir (bkz. şekil 1.1).

![image](./images/0001.png)

Eğer makine öğrenimi konusunda deneyiminiz yoksa, bu hedefe pratikte nasıl ulaşabileceğimizi **Python tarzında programatik bir örnek** üzerinden açıklayalım.
Burada bir ML modelini, bir sınıftan (class) türetilmiş bir nesne (object) olarak ele alıyoruz (bkz. listeleme 1.1). Bu sınıf, modelimizde kullanmak istediğimiz belirli bir **ML algoritması türüne** (bir dizi işlem adımına) karşılık gelir.

Bir modeli oluşturmak (örneğini yaratmak) için, kullanılacak algoritma sınıfını seçmenin yanı sıra, algoritmaya **bazı geçmiş veriler** ve **parametreler (arg1 ve arg2)** de sağlamamız gerekir.
Burada kullanılan geçmiş veriler, **el yazısı rakamların görüntülerinden** oluşur ve bu görüntülerin etiketleri (yani hangi rakam oldukları) zaten bilinir.
Bu, makinenin (veya ML algoritmasının) öğrenme sürecini gerçekleştirmesine yardımcı olur — yani, tıpkı bir çocuğun resimlerden nesneleri tanımayı öğrenmesi gibi, model de rakamları nasıl tanıyacağını öğrenir.
(Bu sürecin ayrıntılarını ilerleyen bölümlerde göreceksiniz.)

Burada bahsedilen parametreler, algoritmanın nasıl çalışacağını belirleyen ayarlardır — yani sürecin nasıl yürütüleceğini kontrol ederler.
Sonuçta elde edilen ML modeli, **daha önce hiç görmediği resimlerdeki rakamları** tahmin edebilecektir (bkz. şekil 1.1), ki bu işlem sonraki listelemede yer alan ikinci kod satırıyla gerçekleştirilecektir.

```Python
ml_model = MachineLearningAlgorithm1(
    arg1=..., arg2=..., data=historical_images
)  # Bir ML modeli oluşturur

digits = [ml_model.predict_image_digit(image) for image in new_images]
# ML modeliyle tahminler yapar
```

Koddaki örnekte de görebileceğiniz gibi, verisetini (ki bunu kendimiz hazırlamamız gerekebilir) sağlamanın yanı sıra, görevi çözmek için önceden sahip olduğumuz bilgiye dayanarak iki şeyi daha belirlememiz gerekir:

* Kullanılacak **ML algoritması (veya yöntemi)** — yani *MachineLearningAlgorithm1*
* Algoritmanın **parametreleri (argümanları)**

Algoritmayı seçmek ve bu parametreleri yapılandırmak, pratikte oldukça zor olabilir.

Örneğin, algoritma seçimini ele alalım:
Yeni başlayan biri olarak tipik yaklaşım, bazı öğrenme kaynaklarını toplamak, benzer görevlerde kullanılan kodları incelemek ve elinizdeki problem için kullanabileceğiniz bir grup ML algoritmasını belirlemektir.
Daha sonra bu algoritmaları (listeleme 1.1’de yaptığımız gibi) geçmiş verileriniz üzerinde tek tek deneyebilir ve **görsellerdeki rakamları tanıma başarısına** göre en iyi performans göstereni seçebilirsiniz.

Bu tekrarlayıcı süreç, bir sonraki kod örneğinde özetlenmiştir.

```Python
ml_algorithm_pool = [
    MachineLearningAlgorithm1,
    MachineLearningAlgorithm2,
    ...,
    MachineLearningAlgorithmN,
]
# Test edilecek ML algoritmalarının bir havuzu

for ml_algorithm in ml_algorithm_pool:
    model = ml_algorithm(
        arg1=..., arg2=...,
        data=historical_images
    )
    # Tüm aday ML algoritmalarını döngüye sokar
    # Her ML algoritmasına göre bir model oluşturur ve değerlendirir
    
    result = evaluate(model)
    push result into the result_pool
    push model into the model_pool

best_ml_model = pick_the_best(result_pool, ml_model_pool)
# Performansa göre en iyi ML modelini seçer

return best_ml_model
```

Bu süreç ilk bakışta sezgisel görünebilir, ancak yeterli makine öğrenimi (ML) bilgisi veya deneyiminiz yoksa **saatlerce hatta günlerce** sürebilir — bunun birkaç nedeni vardır:

Birincisi, uygun ML algoritmalarından oluşan bir **havuz oluşturmak** oldukça zordur.
Literatürü incelemeniz, en son (state-of-the-art) algoritmaları belirlemeniz ve bunların nasıl uygulanacağını öğrenmeniz gerekebilir.

İkincisi, kullanılabilir ML algoritmalarının sayısı çok fazla olabilir.
Bunları **teker teker denemek** hem verimsiz olabilir hem de zaman açısından neredeyse imkânsız hale gelebilir.

Üçüncüsü, her algoritmanın kendine özgü **parametreleri (argümanları)** vardır.
Bu parametreleri doğru şekilde yapılandırmak; uzmanlık, deneyim ve bazen de biraz şans gerektirir.

---

Peki, bunu yapmanın **daha iyi bir yolu** olabilir mi?
Makinenin bu süreci **kendiliğinden (otomatik olarak)** yürütmesi mümkün mü?

Eğer benzer sorunlarla karşılaştıysanız ve ML’yi daha az emek harcayarak uygulamak istiyorsanız, **AutoML** aradığınız araç olabilir.

Basitçe söylemek gerekirse, **AutoML**, yukarıdaki sözde kodda (pseudocode) anlatılan **manuel süreci taklit eder**.
Yani, ML algoritmalarını seçme ve yapılandırma sürecini **otomatikleştirmeye** çalışır.
Bu sayede, varlıklarından bile haberdar olmadığınız **ileri düzey algoritmalara** kolayca erişmenizi sağlar.

---

Aşağıdaki iki satırlık sözde kod, bir AutoML algoritmasını kullanarak nasıl bir ML çözümü elde edebileceğinizi gösterir:

```python
automl_model = AutoMLAlgorithm()
best_ml_model = automl_model.generate_model(data=historical_images)
```

Bir **AutoML algoritmasından bir AutoML model nesnesi** oluşturmak, test edilecek ML algoritmalarının havuzunu sizin belirlemenize gerek kalmadığı anlamına gelir.
Yalnızca veriyi sağlayarak istediğiniz modeli otomatik olarak oluşturabilirsiniz.

---

Ama şu sorular hâlâ geçerlidir:

* Hangi **AutoML algoritmasını** seçmelisiniz?
* Bu AutoML sistemi, hangi ML algoritmaları arasından seçim yapıyor?
* Onları nasıl değerlendiriyor ve en iyi modeli nasıl belirliyor?

Bunlara geçmeden önce, size **ML hakkında temel bir arka plan** sunacağım; böylece AutoML’nin **neyi otomatikleştirdiğini** ve onu **pratikte nasıl zaman ve emek tasarrufu sağlayacak şekilde kullanabileceğinizi** daha iyi anlayabilirsiniz.

Buradaki odak, **AutoML’yi öğrenmek ve kullanmak için bilmeniz gerekenleri** açıklamak olacak.
Bu algoritmalar hakkında daha derin bilgi edinmek isterseniz şu kaynaklara başvurabilirsiniz:

* *Machine Learning in Action* — Peter Harrington (Manning, 2012)
* *Deep Learning with Python*, 2. baskı — François Chollet (Manning, 2021)

Makine öğreniminin temellerine zaten aşina olan okuyucular için ise sonraki bölüm bir **özet** niteliğinde olacak; terimleri netleştirecek ve AutoML’ye yapılacak giriş için **temel motivasyonu** oluşturacaktır.

### 1.2 Getting started with machine learning – 6

Bu bölüm, makine öğrenimine (ML) kısa bir giriş sunar — **ML’nin ne olduğunu**, bir **ML algoritmasının temel bileşenlerini** ve **seçilen algoritma ile veri girdisine dayalı olarak bir ML modelinin nasıl oluşturulduğunu** açıklar.

Bu temel bilgileri öğrenmek, sonraki bölümlerde tanıtılacak **AutoML kavramlarını** anlamak için oldukça önemlidir.

#### *What is machine learning?* – 6

Makine öğreniminin (ML) ortaya çıkışından önce, yapay zekâ (YZ) araştırmalarında baskın paradigma **sembolik yapay zekâ (symbolic AI)** idi. Bu yaklaşımda bilgisayar, yalnızca insanlar tarafından **önceden tanımlanmış kurallar** aracılığıyla verileri işleyebiliyordu.

ML’nin ortaya çıkışıyla birlikte programlama paradigması kökten değişti — bilgisayarlara bilgiyi **verilerden dolaylı olarak öğrenme** yeteneği kazandırıldı.

Örneğin, bir makinenin elma ve muz görüntülerini otomatik olarak tanımasını istediğinizi düşünün.
Sembolik YZ yaklaşımında, bu görevi gerçekleştirebilmesi için yapay zekâya **renk** ve **şekil** gibi özellikleri tanımlayan, insan tarafından okunabilir **mantıksal kurallar** vermeniz gerekirdi.

Buna karşılık, bir ML algoritması yalnızca bir dizi **görüntü** ve bunlara karşılık gelen **etiketleri** (“elma” veya “muz”) alır ve bunlardan **öğrenilmiş kurallar** üretir. Bu kurallar daha sonra **etiketi bilinmeyen yeni görüntüleri** tahmin etmek için kullanılabilir (bkz. şekil 1.2).

![image](./images/0002.png) 

ML’nin temel amaçları **otomasyon** ve **genelleme**dir.

* **Otomasyon**, bir ML algoritmasının kendisine verilen veriler üzerinde **insan müdahalesi olmadan kurallar (veya örüntüler) çıkarmayı** öğrenmesi anlamına gelir.
  Algoritma, insan düşünme biçimini taklit eder ve kendisine sağlanan geçmiş verilerle etkileşime girerek kendini geliştirir — bu sürece **eğitim (training)** ya da **öğrenme (learning)** denir.

* Bu kurallar daha sonra **insan müdahalesine gerek kalmadan yeni veriler üzerinde tekrarlayan tahminler** yapmak için kullanılır.

Örneğin, şekil 1.2’de ML algoritması elma ve muz görüntüleriyle etkileşime girer ve **renge dayalı bir kural** çıkarır; bu sayede eğitimi sırasında onları tanımayı öğrenir.
Bu kurallar, makinenin **yeni görüntüleri insan denetimi olmadan sınıflandırmasına** olanak tanır — bu yeteneğe **yeni verilere genelleme (generalization)** denir.

Bir ML algoritmasının iyi olup olmadığını değerlendirmenin en önemli kriterlerinden biri, **genelleme yeteneğidir**.

Örneğin, sisteme **sarı bir elma** görüntüsü verildiğini varsayalım.
Eğer algoritma yalnızca renge dayalı bir kural öğrenmişse, bunun elma mı yoksa muz mu olduğunu doğru bir şekilde ayırt edemeyecektir.
Buna karşılık, **şekil özelliğini** de öğrenip tahminlerinde kullanan bir ML algoritması **daha doğru tahminler** üretebilir.


#### *The machine learning process* – 7

Bir makine öğrenimi (ML) algoritması, **çıktıları bilinen örneklere maruz kalarak** kuralları öğrenir.
Bu kuralların amacı, **girdileri anlamlı çıktılara dönüştürmeyi** mümkün kılmaktır — örneğin, el yazısı rakamların görüntülerini ilgili sayılara çevirmek gibi.
Bu nedenle öğrenmenin hedefi, **veri dönüşümünü (data transformation)** mümkün kılmak olarak da düşünülebilir.

Öğrenme süreci genellikle aşağıdaki iki bileşeni gerektirir:

---

* **Veri girdileri (Data inputs):**
  ML algoritmasına beslenecek, hedef göreve ait veri örnekleridir.
  Örneğin, görüntü tanıma probleminde (bkz. Şekil 1.2) bir grup **elma ve muz resmi** ile bunlara karşılık gelen **etiketler** (“elma” veya “muz”) bu girdilerdir.

* **Öğrenme algoritması (Learning algorithm):**
  Verilen veriye dayalı olarak bir **model** türeten matematiksel bir süreçtir ve şu dört unsuru içerir:

  * Veriden öğrenilecek **parametrelere sahip bir ML modeli**
  * Modelin mevcut parametrelerle performansını ölçmek için bir **ölçüm yöntemi** (örneğin tahmin doğruluğu)
  * Modeli güncellemenin bir yolu — buna **optimizasyon yöntemi** denir
  * Öğrenme sürecinin **ne zaman duracağını belirleyen bir durdurma ölçütü (stop criterion)**

---

Modelin parametreleri ilk kez başlatıldıktan sonra (örneğin rastgele veya benzer modellerden öğrenilmiş “warm start” parametreleriyle), öğrenme algoritması **parametreleri ölçüm sonucuna göre adım adım güncelleyerek** modeli iteratif biçimde geliştirir.
Bu ölçüm, **eğitim aşamasında “kayıp fonksiyonu” (loss function)** veya **amaç fonksiyonu (objective function)** olarak adlandırılır ve modelin tahminleriyle gerçek (ground-truth) hedefler arasındaki farkı ölçer.
Bu süreç Şekil 1.3’te gösterilmektedir.

---

Öğrenme sürecini daha iyi anlamak için bir örnek düşünelim:
İki boyutlu bir uzayda bir grup veri noktamız olduğunu varsayalım (bkz. Şekil 1.4).
Her nokta **siyah** veya **beyaz** renktedir.
Amacımız, yeni bir nokta geldiğinde konumuna göre bunun siyah mı yoksa beyaz mı olduğunu belirleyebilen bir ML modeli oluşturmak.

Bu hedefe ulaşmanın basit bir yolu, elimizdeki noktalara bakarak iki boyutlu uzayı ikiye ayıran **yatay bir çizgi** çizmektir.
Bu çizgi, bir ML modeli olarak düşünülebilir.
Çizginin **parametresi**, yatay konumudur ve bu parametre veri noktalarına göre **öğrenilebilir ve güncellenebilir**.

Şekil 1.3’te açıklanan öğrenme süreciyle birlikte, gerekli bileşenleri şu şekilde tanımlayabiliriz:

![image](./images/0003.png)

* **Veri girdileri:**
  İki boyutlu uzayda konumlarıyla tanımlanan siyah ve beyaz noktalardan oluşur.

* **Öğrenme algoritması:**
  Aşağıdaki dört bileşenden oluşur:

  * **ML modeli:**  y = a şeklinde formüle edilen yatay bir çizgi; burada *a*, algoritma tarafından güncellenebilen parametredir.
  * **Doğruluk ölçümü:**  Modelin doğru etiketlediği noktaların yüzdesi.
  * **Optimizasyon yöntemi:**  Çizgiyi her yinelemede belirli bir mesafe yukarı veya aşağı kaydırmak.
    Bu mesafe, her adımda doğruluk ölçümünün değerine bağlıdır. Durdurma koşulu sağlanana kadar devam eder.
  * **Durdurma ölçütü (stop criterion):**  Ölçüm %100’e ulaştığında, yani tüm noktalar mevcut çizgiye göre doğru etiketlendiğinde süreci durdurur.

![image](./images/0004.png)

Şekil 1.4’teki örnekte, öğrenme algoritması iki yinelemeden sonra tüm noktaları doğru şekilde ayıran çizgiyi elde eder.
Ancak pratikte bu koşul her zaman sağlanmayabilir.
Bu durum, **girdi verilerinin dağılımına**, **seçilen model türüne** ve **modelin nasıl ölçülüp güncellendiğine** bağlıdır.

Çoğu zaman farklı bileşenler seçmemiz ve farklı kombinasyonları denememiz gerekir ki öğrenme süreci beklenen ML çözümüne yaklaşsın.

Ayrıca, öğrenilmiş model tüm eğitim verilerini doğru şekilde etiketlese bile, **görülmemiş yeni verilerde** aynı başarıyı garanti etmez.
Yani modelin **genelleme yeteneği** zayıf olabilir (bu konuyu bir sonraki bölümde daha detaylı ele alacağız).

Bu nedenle, öğrenme bileşenlerini dikkatli seçmek ve öğrenme sürecini doğru şekilde ayarlamak son derece önemlidir.

#### *Hyperparameter tuning* – 9

Öğrenme sürecini ayarlayarak beklenen modeli elde edebilmek için uygun bileşenleri nasıl seçeriz?
Bu soruyu yanıtlamak için **hiperparametreler (hyperparameters)** adı verilen bir kavramı tanıtmamız ve bunların, şimdiye kadar bahsettiğimiz **parametrelerle** ilişkisini açıklamamız gerekir:

---

* **Parametreler**, öğrenme süreci sırasında makine öğrenimi (ML) algoritması tarafından güncellenebilen değişkenlerdir.
  Bu değişkenler, veriden kuralları öğrenmek için kullanılır.
  Örneğin, önceki örneğimizdeki (Şekil 1.4) yatay çizginin konumu, noktaları sınıflandırmaya yardımcı olan tek parametredir.
  Bu parametre, optimizasyon yöntemi tarafından eğitim süreci boyunca ayarlanarak farklı renklerdeki noktaları ayırmak için gereken konum kuralını öğrenir.
  Parametreleri ayarlayarak, verilen girdi verisinin çıktısını doğru bir şekilde tahmin edebilen bir ML modeli elde edebiliriz.

---

Şekil 1.4

**Öğrenme süreci örneği:** Beyaz ve siyah noktaları ayıran yatay bir çizginin öğrenilmesi

---

* **Hiperparametreler** de bir tür parametredir; ancak bunlar öğrenme süreci başlamadan **önce** algoritma için tanımlanır ve sürecin tamamı boyunca sabit kalır.
  Bunlara ölçüm yöntemi, optimizasyon metodu, öğrenme hızı, durdurma kriteri gibi unsurlar dahildir.
  Bir ML algoritmasının genellikle birden fazla hiperparametresi vardır.
  Bu hiperparametrelerin farklı kombinasyonları, öğrenme süreci üzerinde farklı etkiler yaratır ve farklı performanslara sahip modellerin ortaya çıkmasına neden olur.
  Ayrıca, algoritma türünü (veya ML modeli türünü) de bir hiperparametre olarak düşünebiliriz; çünkü bu seçimi biz yaparız ve bu seçim süreç boyunca sabit kalır.

---

Bir ML algoritması için en uygun hiperparametre kombinasyonunun seçilmesine **hiperparametre ayarlaması (hyperparameter tuning)** denir.
Bu işlem genellikle **yinelemeli (iteratif)** bir süreçtir.
Her yinelemede, belirli bir hiperparametre kümesi seçilir ve bu küme kullanılarak eğitim veri kümesiyle bir ML modeli eğitilir.
Şekil 1.5’teki “ML algoritması” bloğu, Şekil 1.3’te açıklanan öğrenme sürecini temsil eder.
Her öğrenilen model, **doğrulama kümesi (validation set)** adı verilen ayrı bir veri kümesinde değerlendirilir ve en iyi performans gösteren model, **nihai model (final model)** olarak seçilir.
Bu modelin **genellenebilirliği (generalizability)** ise, **test kümesi (test set)** adı verilen başka bir veri kümesi üzerinde test edilerek ölçülür.
Bu aşama, tüm ML iş akışını tamamlar.

![image](./images/0006.png)

---

Genel olarak, ML iş akışında üç farklı veri kümesi bulunur ve her biri diğerlerinden farklıdır:

* **Eğitim kümesi (training set):**
  Belirli bir hiperparametre kombinasyonu ile modeli eğitmek için kullanılır.

* **Doğrulama kümesi (validation set):**
  Eğitilen modelleri değerlendirmek ve en iyi hiperparametreleri seçmek için kullanılır.

* **Test kümesi (test set):**
  Ayarlama süreci tamamlandıktan sonra, son modeli test etmek için yalnızca bir kez kullanılır.
  Bu küme, **eğitim veya ayarlama** aşamalarında kullanılmamalıdır.

---

Eğitim ve test kümeleri genellikle kolay anlaşılır.
Ek bir doğrulama kümesi kullanmamızın nedeni, ayarlama aşamalarında algoritmanın tüm eğitim verisine maruz kalmasını önlemektir.
Bu sayede **nihai modelin, daha önce görülmemiş veriler (unseen data)** üzerinde genellenebilirliği artar.

Eğer doğrulama kümesi kullanılmazsa, ayarlama aşamasında seçilen “en iyi” model yalnızca eğitim verisindeki küçük ayrıntıları yakalamaya odaklanır ve sürekli artan eğitim doğruluğuna rağmen, farklı veriler üzerinde kötü performans gösterir.
Modelin, test (veya doğrulama) kümesinde eğitim kümesine göre daha kötü performans göstermesi durumuna **aşırı uyum (overfitting)** denir.

Bu, ML alanında çok iyi bilinen bir problemdir ve genellikle modelin öğrenme kapasitesi çok yüksek, eğitim veri kümesi ise sınırlı olduğunda ortaya çıkar.

---

**Örnek:**
İlk üç sayısı verilen bir dizide dördüncü sayıyı tahmin etmek istediğimizi varsayalım:
a₁ = 1, a₂ = 2, a₃ = 3, a₄ = ?
(Burada a₄ doğrulama kümesidir; a₅ ve sonrakiler test kümeleridir.)
Doğru cevap a₄ = 4’tür.
Basit bir model olan **aᵢ = i**, doğru sonucu verir.
Ancak üçüncü dereceden bir polinomla diziyi uydurursak:
**aᵢ = i³ – 6i² + 12i – 6**
modeli, a₄ için 10 sonucunu tahmin eder.

Doğrulama süreci, bir modelin **genelleme yeteneğinin** değerlendirme sırasında daha doğru bir şekilde yansıtılmasını sağlar ve böylece daha uygun modellerin seçilmesine olanak tanır.

---

**NOT:**
**Aşırı uyum (overfitting)**, ML alanında en önemli problemlerden biridir.
Ayarlama süreci sırasında doğrulama yapmak dışında, bu sorunu çözmenin başka yolları da vardır; örneğin:

* veri kümesini genişletmek (data augmentation),
* modele **regularization (düzenleme)** ekleyerek öğrenme kapasitesini sınırlamak, vb.

Bu konunun ayrıntılarına burada girmeyeceğiz.
Daha fazla bilgi için **François Chollet’in *Deep Learning with Python*** adlı kitabına başvurabilirsiniz.

#### *The obstacles to applying machine learning* – 11

Bu noktada, makine öğreniminin (ML) ne olduğunu ve nasıl işlediğini temel düzeyde anlamış olmalısınız.
Birçok gelişmiş ML araç setinden yararlanabilseniz de, uygulamada hâlâ bazı zorluklarla karşılaşabilirsiniz.
Bu bölümde, bu zorluklardan bazıları açıklanmaktadır — amaç sizi korkutmak değil, ilerleyen kısımlarda anlatılacak **Otomatik Makine Öğrenimi (AutoML)** teknikleri için bir bağlam sağlamaktır.

Karşılaşabileceğiniz başlıca engeller şunlardır:

---

* **ML tekniklerini öğrenmenin maliyeti:**
  Temel bilgileri ele aldık, ancak ML’i gerçek bir probleme uygulamak için çok daha fazla bilgi gerekir.
  Örneğin, probleminizi bir ML problemi olarak nasıl formüle edeceğinizi, hangi ML algoritmalarını kullanabileceğinizi ve bunların nasıl çalıştığını,
  veriyi ML algoritmasına girdi olarak verebilmek için nasıl temizleyip ön işlemeden geçirmeniz gerektiğini,
  model eğitimi ve hiperparametre ayarlaması (tuning) için hangi değerlendirme kriterlerini seçmeniz gerektiğini düşünmelisiniz.
  Tüm bu soruların önceden yanıtlanması gerekir ve bu da önemli bir zaman yatırımı gerektirir.

---

* **Uygulama karmaşıklığı:**
  Gerekli bilgi ve deneyime sahip olsanız bile, bir ML algoritması seçtikten sonra iş akışını uygulamak karmaşık bir iştir.
  Daha gelişmiş algoritmalar benimsendikçe, uygulama ve hata ayıklama (debugging) için gereken zaman da artar.

---

* **Teori ile pratik arasındaki fark:**
  Öğrenme süreci çoğu zaman yorumlanması zor bir yapıya sahiptir ve performans büyük ölçüde veriye bağlıdır.
  Ayrıca, ML’de kullanılan veri kümeleri genellikle karmaşık, gürültülü (noisy) ve yorumlanması, temizlenmesi, kontrol edilmesi zor yapılardır.
  Bu nedenle ayarlama (tuning) süreci çoğunlukla analitik olmaktan çok deneysel (ampirik) bir karakter taşır.
  Hatta ML uzmanları bile bazen istenilen sonuçlara ulaşamayabilirler.

---

Bu zorluklar, ML’in sınırlı deneyime sahip kişiler tarafından yaygın şekilde kullanılmasını önemli ölçüde zorlaştırır
ve aynı zamanda ML uzmanlarının üzerindeki yükü artırır.
Bu durum, araştırmacıların ve uygulayıcıların; engelleri azaltacak, gereksiz adımları ortadan kaldıracak
ve algoritmaların manuel tasarımı ile ayarlama sürecindeki yükü hafifletecek bir çözüm arayışına girmesine neden olmuştur —
işte bu çözüm **AutoML (Otomatik Makine Öğrenimi)**’dir.


### 1.3 AutoML: The automation of automation – 12

AutoML’in amacı, bir makinenin insanların ML algoritmalarını tasarlama, ayarlama ve uygulama biçimini taklit etmesini sağlayarak makine öğrenimini (ML) daha kolay benimsememizi mümkün kılmaktır (bkz. Şekil 1.6).
Makine öğreniminin temel özelliklerinden biri **otomasyon** olduğundan, **AutoML**, “otomasyonun otomatikleştirilmesi” olarak da görülebilir.

![image](./images/0007.png) 

AutoML’in nasıl çalıştığını anlamanıza yardımcı olmak için, önce temel bileşenlerini gözden geçirelim.

#### *Three key components of AutoML* – 12
İşte, Bölüm 1.1’de tanıtılan sözde kodun (pseudocode) kısa bir özeti:

```
ml_algorithm_pool = [
 MachineLearningAlgorithm1,
 MachineLearningAlgorithm2,
 ...
 MachineLearningAlgorithmN,
]

for ml_algorithm in ml_algorithm_pool:
 model = ml_algorithm(arg1=..., arg2=..., data=historical_images)
 result = evaluate(model)
 push result into the result_pool
 push model into the model_pool

best_ml_model = pick_the_best(result_pool, ml_model_pool)
return best_ml_model
```

Bu sözde kod, bir **AutoML algoritması**nın basit bir örneği olarak görülebilir.
Bu algoritma, bir grup ML algoritmasını girdi olarak alır, her birini sırayla değerlendirir ve en iyi sonucu veren algoritmadan öğrenilmiş modeli çıktı olarak verir.

Her AutoML algoritması, aşağıdaki **üç temel bileşenden** oluşur (bkz. Şekil 1.7):

![image](./images/0008.png) 

---

##### 1. **Arama uzayı (Search space)**

Bu, seçilecek hiperparametreler kümesini ve her bir hiperparametrenin alabileceği değer aralığını ifade eder.
Her hiperparametrenin aralığı, kullanıcının bilgi birikimine ve gereksinimlerine göre tanımlanabilir.

Örneğin, sözde kodda gösterildiği gibi arama uzayı bir **ML algoritmaları havuzu** olabilir.
Bu durumda, **ML algoritmasının türü** seçilecek bir hiperparametre olarak kabul edilir.

Arama uzayı aynı zamanda belirli bir ML algoritmasının hiperparametrelerinden (örneğin modelin yapısı gibi) de oluşabilir.
Arama uzayının tasarımı büyük ölçüde göreve bağlıdır, çünkü farklı görevler için farklı ML algoritmalarının benimsenmesi gerekebilir.
Ayrıca kullanıcının ilgisi, uzmanlığı ve deneyim düzeyiyle de kişiselleşmiş bir yapıya sahip olabilir.

Burada her zaman bir denge vardır:

* Geniş bir arama uzayı tanımlamak, daha fazla olasılığı değerlendirmenizi sağlar ama
* iyi bir model bulmak için harcanacak süreyi ve hesaplama maliyetini artırır.

Yeni başlayanlar için, tüm ML algoritmalarını içeren çok geniş bir arama uzayı tanımlamak cazip gelebilir.
Ancak bu yaklaşım, **zaman** ve **hesaplama maliyeti** açısından verimsizdir.

Bu kitapta, ikinci bölümde bu konuyu daha ayrıntılı ele alacak ve farklı gereksinimler doğrultusunda arama uzayınızı nasıl özelleştirebileceğinizi öğreneceksiniz.


---

##### 2. **Arama stratejisi (Search strategy)**

Bu, arama uzayından **en uygun hiperparametre setini** seçme stratejisidir.
AutoML genellikle **yinelemeli bir deneme-yanılma süreci** olduğundan, strateji çoğunlukla hiperparametreleri sırayla seçip performanslarını değerlendirir.

Strateji, arama uzayındaki tüm hiperparametreleri tek tek deneyebilir (sözde kodda olduğu gibi)
ya da daha önce denenen hiperparametrelerin sonuçlarına göre sonraki denemeleri optimize edecek şekilde **uyarlanabilir (adaptive)** bir yapı izleyebilir.

Daha iyi bir arama stratejisi, aynı sürede **daha iyi bir ML çözümü** elde etmenizi sağlar.
Ayrıca, arama süresini ve hesaplama maliyetini azaltarak daha büyük bir arama uzayını kullanmanıza da olanak tanır.

Bu kitapta, üçüncü bölümde farklı **arama algoritmalarını** nasıl benimseyeceğinizi, karşılaştıracağınızı ve uygulayacağınızı öğreneceksiniz.

---

##### 3. **Performans değerlendirme stratejisi (Performance evaluation strategy)**

Bu strateji, seçilen hiperparametrelerle oluşturulan bir ML algoritmasının performansını değerlendirmek için kullanılır.
Değerlendirme kriterleri genellikle manuel ayarlama sürecinde kullanılanlarla aynıdır — örneğin, seçilen ML algoritmasıyla eğitilen modelin **doğrulama performansı**.

Bu kitapta, farklı türde ML görevlerini çözmek için AutoML kullanırken uygulanabilecek çeşitli değerlendirme stratejilerini tartışacağız.

---

AutoML algoritmalarının benimsenmesini kolaylaştırmak için, bir **AutoML araç kiti (toolkit)** genellikle bu üç bileşeni bir araya getirir
ve **varsayılan bir arama uzayı** ile **arama algoritması** içeren genel API’ler (Application Programming Interfaces) sağlar.
Böylece kullanıcı, bu bileşenleri elle seçmek zorunda kalmaz.

Kullanıcı açısından, en basit durumda yapılması gereken tek şey veriyi sağlamaktır — veriyi eğitim ve doğrulama kümelerine ayırmaya bile gerek yoktur:

```python
automl_model = AutoMLAlgorithm()
best_ml_model = automl_model.generate_model(data=...)
```

Ancak farklı kullanıcıların farklı kullanım senaryoları ve ML uzmanlık düzeyleri olabileceği için,
kendi **arama uzaylarını**, **değerlendirme stratejilerini** ve hatta **arama stratejilerini** tasarlamaları gerekebilir.

Bu nedenle, mevcut AutoML sistemleri genellikle bu bileşenlerin yapılandırılabilir (configurable) biçimde özelleştirilmesine izin veren API’ler sunar.
Bu API’ler, **en basitinden en gelişmişine kadar geniş bir yelpazede** çözümler sağlar (bkz. Şekil 1.8).

![image](./images/0009.png)

Bu sayede, kendi kullanım durumunuza en uygun olan API’yi seçebilirsiniz.
Bu kitapta, farklı AutoML uygulamaları için gelişmiş bir AutoML araç kiti olan **AutoKeras**’ta doğru API’yi nasıl seçeceğinizi
ve **KerasTuner** yardımıyla kendi AutoML algoritmanızı nasıl oluşturabileceğinizi öğreneceksiniz.

#### *Are we able to achieve full automation?* – 15

AutoML alanı, endüstri ve açık kaynak topluluğunun katılımıyla **yaklaşık otuz yıldır gelişmektedir.**
Bu süreçte birçok başarılı uygulama ve umut verici gelişme görülmüştür. Bunlardan bazıları şunlardır:

---

* **Birçok şirket içi araç ve açık kaynak platformu** geliştirildi.
  Bu araçlar, ML modellerinin **hiperparametre ayarlamasını** ve **model seçimini** kolaylaştırmak için kullanılır.
  (Örneğin: *Google Vizier*, *Facebook Ax* vb.)

* **AutoML çözümleri**, birçok **Kaggle veri bilimi yarışmasında** insan seviyesine yakın performans göstermiştir.

* **Auto-sklearn**, **AutoKeras** gibi geniş kapsamlı açık kaynak ML paketleri geliştirildi.
  Bu paketler, hiperparametre ayarlamasını iyileştirmek ve ML iş akışlarını (pipeline) otomatikleştirmek için tasarlanmıştır.

* **Ticari AutoML ürünleri**, küçükten büyüğe birçok şirketin üretim ortamında ML’i benimsemesine yardımcı olmaktadır.
  Örneğin, **Disney**, herhangi bir ML mühendisi ekibi tutmadan, **Google Cloud AutoML** kullanarak çevrimiçi mağazası için ML çözümleri geliştirmiştir.
  (Kaynak: [Google Cloud AutoML Blogu](https://blog.google/products/google-cloud/cloud-automlmaking-ai-accessible-every-business/))

* **Bilgisayar bilimi dışındaki alanlardaki araştırmacılar** da AutoML’in gücünden yararlanmaktadır.
  Artık **tıp**, **nörobilim** ve **ekonomi** gibi alanlardaki araştırmacılar, ML ve programlama konularında uzun öğrenme süreçlerinden geçmeden,
  **tıbbi görüntü segmentasyonu**, **genomik araştırma** veya **hayvan tanıma ve koruma** gibi alanlara özel ML çözümleri geliştirebilmektedirler.
  *(Örnek çalışmalar: Weng et al., 2019; Liu et al., 2019; Liu & Luo, 2019)*

---

##### AutoML’in Geleceği ve Devam Eden Zorluklar

AutoML’in **makine öğrenimini demokratikleştirme** potansiyelinin tümünü hâlâ keşfetme aşamasındayız.
Şimdiye kadar pek çok başarılı uygulama görülmüş olsa da, çözülmesi gereken önemli zorluklar ve sınırlamalar hâlâ bulunmaktadır:

---

* **AutoML sistemlerinin inşa edilme zorluğu:**
  Sıfırdan bir AutoML sistemi kurmak, bir ML sistemini kurmaktan çok daha karmaşık ve kapsamlı bir süreçtir.

* **Veri toplama ve temizlemenin otomatikleştirilmesi:**
  AutoML hâlen **verinin toplanması, temizlenmesi ve etiketlenmesi** için insan müdahalesine ihtiyaç duyar.
  Bu işlemler genellikle ML algoritmalarının tasarımından bile daha karmaşıktır ve günümüzde AutoML tarafından tam olarak otomatikleştirilememektedir.
  Günümüzde bir AutoML sisteminin çalışabilmesi için, **açık bir görev tanımı** ve **yüksek kaliteli bir veri kümesi** gereklidir.

* **AutoML algoritmasının seçimi ve ayarlanmasının maliyeti:**
  “**No Free Lunch (Bedava Öğle Yemeği Yok)**” teoremi bize her duruma uygun tek bir AutoML algoritmasının olmadığını söyler.
  ML algoritmalarını seçme ve ayarlama sürecinde tasarruf ettiğiniz çaba, bazen AutoML algoritmasını seçmek ve ayarlamak için harcayacağınız çabayla **eşitlenebilir veya aşılabilir.**

* **Kaynak maliyetleri:**
  AutoML süreci, **zaman** ve **hesaplama kaynakları** açısından oldukça maliyetlidir.
  Mevcut AutoML sistemleri, benzer sonuçlara ulaşmak için genellikle insan uzmanlardan **daha fazla hiperparametre kombinasyonu** denemek zorundadır.

* **İnsan–bilgisayar etkileşiminin maliyeti:**
  AutoML’in ürettiği çözümleri ve ayarlama süreçlerini yorumlamak kolay değildir.
  Sistemler karmaşıklaştıkça, insanların sürece müdahil olması ve modelin nasıl oluşturulduğunu anlaması daha da zorlaşmaktadır.

---

##### AutoML’in Gelişim Aşaması

AutoML hâlâ **gelişiminin erken safhasındadır.**
Bu alandaki ilerleme, farklı disiplinlerden **araştırmacıların, geliştiricilerin ve uygulayıcıların** katılımına bağlıdır.

Bir gün siz de bu gelişime katkıda bulunabilirsiniz; ancak bu kitabın amacı bundan daha mütevazıdır.
Bu kitap, özellikle:

* Makine öğreniminde sınırlı deneyime sahip uygulayıcılara
* Ya da temel bilgiye sahip olup ML çözümlerini daha az çabayla geliştirmek isteyenlere yöneliktir.

Kitap, yalnızca **beş satır kodla** bir ML problemini otomatik olarak çözmeyi öğretecek
ve giderek daha karmaşık veri türleri (örneğin **görseller**, **metinler** vb.) için gelişmiş AutoML çözümlerine doğru ilerleyecektir.

Bir sonraki bölümde, ML’in temellerine daha derinlemesine dalacak ve bir ML projesinin **uçtan uca (end-to-end)** iş akışını inceleyeceğiz.
Bu, ilerleyen bölümlerde AutoML tekniklerini daha iyi anlamanıza ve etkili biçimde kullanmanıza yardımcı olacaktır.

---

### Özet

* **Makine öğrenimi**, bilgisayarların açıkça programlanmadan, verilerle etkileşime girerek işlem biçimlerini değiştirebilme yeteneğidir.
* ML süreci, modelin parametrelerini veriye ve ölçümlere göre ayarlayan yinelemeli bir algoritmik süreçtir.
  Süreç, model beklenen çıktıları üretebildiğinde veya kullanıcı tarafından tanımlanan bir kriter sağlandığında durur.
* Bir ML algoritmasındaki **hiperparametrelerin ayarlanması**, öğrenme sürecini kontrol etmenizi ve probleme özel bileşenler seçmenizi sağlar.
* **AutoML**, ML modellerinin tasarlanması ve uygulanmasından elde edilen deneyimden yararlanarak ayarlama sürecini otomatikleştirir.
  Böylece veri bilimcilerin üzerindeki yükü azaltır ve kapsamlı deneyim gerektirmeyen, kullanıma hazır ML tekniklerini erişilebilir kılar.
* Bir AutoML algoritması üç temel bileşenden oluşur:
  **arama uzayı**, **arama stratejisi** ve **değerlendirme stratejisi**.
  Farklı AutoML sistemleri, bu bileşenleri sizin için yapılandıran veya özelleştirmenize izin veren farklı düzeylerde API’ler sağlar.
* AutoML hâlen çözülmemiş pek çok zorluk barındırmaktadır.
  Gerçek anlamda **tam otomatik makine öğrenimi** hedefine ulaşmak zordur.
  Bu nedenle **iyimser** olmalı, ancak AutoML’in mevcut yeteneklerini **abartmamaya** da özen göstermeliyiz.


## 2. The end-to-end pipeline of an ML project

* 2.1 An overview of the end-to-end pipeline – 18
* 2.2 Framing the problem and assembling the dataset – 19
* 2.3 Data preprocessing – 22
* 2.4 Feature engineering – 25
* 2.5 ML algorithm selection – 28

  * *Building the linear regression model* – 29
  * *Building the decision tree model* – 31
* 2.6 Fine-tuning the ML model: Introduction to grid search – 34

## 3. Deep learning in a nutshell

* 3.1 What is deep learning? – 42
* 3.2 TensorFlow and Keras – 43
* 3.3 California housing price prediction with a multilayer perceptron – 43

  * *Assembling and preparing the data* – 44
  * *Building up the multilayer perceptron* – 45
  * *Training and testing the neural network* – 49
  * *Tuning the number of epochs* – 52
* 3.4 Classifying handwritten digits with convolutional neural networks – 55

  * *Assembling and preparing the dataset* – 55
  * *Addressing the problem with an MLP* – 57
  * *Addressing the problem with a CNN* – 59
* 3.5 IMDB review classification with recurrent neural networks – 64

  * *Preparing the data* – 65
  * *Building up the RNN* – 67
  * *Training and validating the RNN* – 69

---

# Part 2: AutoML in Practice

## 4. Automated generation of end-to-end ML solutions

* 4.1 Preparing the AutoML toolkit: AutoKeras – 73
* 4.2 Automated image classification – 76

  * *Attacking the problem with five lines of code* – 76
  * *Dealing with different data formats* – 80
  * *Configuring the tuning process* – 81
* 4.3 End-to-end AutoML solutions for four supervised learning problems – 83

  * *Text classification with the 20 newsgroups dataset* – 83
  * *Structured data classification with the Titanic dataset* – 85
  * *Structured data regression with the California housing dataset* – 88
  * *Multilabel image classification* – 89
* 4.4 Addressing tasks with multiple inputs or outputs – 91

  * *Automated image classification with the AutoKeras IO API* – 91
  * *Automated multi-input learning* – 93
  * *Automated multi-output learning* – 94

## 5. Customizing the search space by creating AutoML pipelines

* 5.1 Working with sequential AutoML pipelines – 100
* 5.2 Creating a sequential AutoML pipeline for automated hyperparameter tuning – 102

  * *Tuning MLPs for structured data regression* – 103
  * *Tuning CNNs for image classification* – 109
* 5.3 Automated pipeline search with hyperblocks – 111

  * *Automated model selection for image classification* – 112
  * *Automated selection of image preprocessing methods* – 117
* 5.4 Designing a graph-structured AutoML pipeline – 121
* 5.5 Designing custom AutoML blocks – 125

  * *Tuning MLPs with a custom MLP block* – 125
  * *Designing a hyperblock for model selection* – 132

## 6. AutoML with a fully customized search space

* 6.1 Customizing the search space in a layerwise fashion – 139

  * *Tuning an MLP for regression with KerasTuner* – 139
  * *Tuning an autoencoder model for unsupervised learning* – 147
* 6.2 Tuning the autoencoder model – 151
* 6.3 Tuning shallow models with different search methods – 154

  * *Selecting and tuning shallow models* – 154
  * *Tuning a shallow model pipeline* – 157
  * *Trying out different search methods* – 158
  * *Automated feature engineering* – 159
* 6.4 Controlling the AutoML process by customizing tuners – 169

  * *Creating a tuner for tuning scikit-learn models* – 170
  * *Creating a tuner for tuning Keras models* – 174
  * *Jointly tuning and selection among deep learning and shallow models* – 176
  * *Hyperparameter tuning beyond Keras and scikit-learn models* – 179

# Part 3 – Advanced Topics in AutoML

## 7. Customizing the Search Method of AutoML
### 7.1 Sequential Search Methods
### 7.2 Getting Started with a Random Search Method
### 7.3 Customizing a Bayesian Optimization Search Method
- Vectorizing the hyperparameters
- Updating the surrogate function based on historical model evaluations
- Designing the acquisition function
- Sampling the new hyperparameters via the acquisition function
- Tuning the GBDT model with the Bayesian optimization method
- Resuming the search process and recovering the search method
### 7.4 Customizing an Evolutionary Search Method
- Selection strategies in the evolutionary search method
- The aging evolutionary search method
- Implementing a simple mutation operation
- Evaluating the aging evolutionary search method

## 8. Scaling up AutoML
### 8.1 Handling Large-Scale Datasets
- Loading an image-classification dataset
- Splitting the loaded dataset
- Loading a text-classification dataset
- Handling large datasets in general
### 8.2 Parallelization on Multiple GPUs
- Data parallelism
- Model parallelism
- Parallel tuning
### 8.3 Search Speedup Strategies
- Model scheduling with Hyperband
- Faster convergence with pretrained weights in the search space
- Warm-starting the search space

## 9. Wrapping Up
### 9.1 Key Concepts in Review
- The AutoML process and its key components
- The machine learning pipeline
- The taxonomy of AutoML
- Applications of AutoML
- Automated deep learning with AutoKeras
- Fully personalized AutoML with KerasTuner
- Implementing search techniques
- Scaling up the AutoML process
