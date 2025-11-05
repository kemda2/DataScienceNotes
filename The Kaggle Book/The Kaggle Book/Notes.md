# Part I: Introduction to Competitions *(Bölüm I: Yarışmalara Giriş)*

## Chapter 1: Introducing Kaggle and Other Data Science Competitions *(Bölüm 1: Kaggle ve Diğer Veri Bilimi Yarışmalarına Giriş)*

Veri bilimi yarışmaları uzun zamandır var ve zaman içinde giderek artan bir başarı elde ettiler. Tutkulu bir yarışmacı topluluğundan doğan bu yarışmalar, giderek daha fazla ilgi çekmeye ve milyonlarca veri bilimciden oluşan çok daha geniş bir kitleye ulaşmaya başladı. En popüler veri bilimi yarışma platformu olan **Kaggle**’da uzun yıllardır yarışmacı olarak yer aldığımız için, bu değişimlerin tümüne yıllar boyunca doğrudan tanıklık ettik ve bizzat deneyimledik.

Bugün, Kaggle ve diğer yarışma platformları hakkında bilgi ararsanız, çok sayıda **buluşma (meetup)**, **tartışma paneli**, **podcast**, **röportaj** ve hatta bu tür yarışmalarda nasıl kazanılacağını anlatan **çevrimiçi kurslar** bulabilirsiniz. (Genellikle bu kurslar size azim, hesaplama kaynakları ve harcanan zamanın doğru karışımını kullanmanızı tavsiye eder.) Ancak, şu anda okumakta olduğunuz kitap dışında, bu kadar çok veri bilimi yarışmasını nasıl yöneteceğinizi ve onlardan nasıl en iyi şekilde yararlanabileceğinizi — yalnızca puan veya sıralama açısından değil, **profesyonel deneyim** bakımından da — sistematik bir şekilde anlatan bir rehber bulmanız oldukça zordur.

Bu kitapta amacımız, Kaggle veya diğer veri bilimi yarışmalarında nasıl yüksek puan alacağınızı anlatan birkaç ipucu vermek değil. Bunun yerine, **Kaggle’da daha etkili yarışmanız** ve yarışma deneyimlerinizden — özellikle de profesyonel hayatınız açısından — **en fazla faydayı elde etmeniz** için kapsamlı bir rehber sunmak istiyoruz. Kitap içeriğine, **Kaggle Master** ve **Grandmaster**’larla yapılan röportajlar da eşlik ediyor. Bu röportajların size Kaggle’da yarışmanın belirli yönleri hakkında farklı bakış açıları ve içgörüler sunacağını ve rekabetçi veri bilimi yaparken kendinizi sınama ve öğrenme biçiminize ilham vereceğini umuyoruz.

Bu kitabın sonunda, **kendi deneyimlerimizden**, **yarışmalardan edindiğimiz bilgilerden** ve **kaynaklardan** doğrudan derlediğimiz bilgileri içselleştirmiş olacaksınız. Böylece yarışmadan yarışmaya öğrenmenizi ve gelişmenizi sağlayacak bir yol haritasına sahip olacaksınız.

Başlangıç noktası olarak, bu bölümde şunları inceleyeceğiz:

* Rekabetçi programlamanın nasıl veri bilimi yarışmalarına evrildiğini,
* Neden Kaggle platformunun bu tür yarışmalar için en popüler site olduğunu,
* Ve bu platformun nasıl çalıştığını.

Bu bölümde aşağıdaki konuları ele alacağız:

* Veri bilimi yarışma platformlarının yükselişi
* **Common Task Framework** (Ortak Görev Çerçevesi) paradigması
* Kaggle platformu ve bazı alternatifleri
* Bir Kaggle yarışmasının nasıl işlediği: aşamaları, yarışma türleri, gönderim ve liderlik tablosu dinamikleri, hesaplama kaynakları, ağ oluşturma ve daha fazlası

### The rise of data science competition platforms *(Veri bilimi yarışma platformlarının yükselişi)*

Rekabetçi programlamanın köklü bir geçmişi vardır; 1970’lerde düzenlenen ilk **ICPC (International Collegiate Programming Contest – Uluslararası Üniversitelerarası Programlama Yarışması)** ile başlamıştır. İlk ICPC’de, üniversitelerden ve şirketlerden gelen küçük takımlar, bir dizi problemi bilgisayar programı kullanarak çözmeleri gereken bir yarışmaya katılıyordu (başlangıçta katılımcılar **FORTRAN** dilinde kodlama yapıyordu). İyi bir final sıralaması elde etmek için takımların güçlü **takım çalışması**, **problem çözme** ve **programlama** becerileri sergilemeleri gerekiyordu.

Bu tür bir yarışmanın yoğun atmosferinde yer almak ve işe alım yapan şirketlerin dikkatini çekme fırsatı, öğrencilere büyük bir motivasyon sağladı ve yarışmanın yıllar boyunca popüler kalmasına neden oldu. ICPC finalistleri arasında, günümüzde oldukça tanınmış isimler vardır: **Adam D’Angelo** (Facebook’un eski CTO’su ve Quora’nın kurucusu), **Nikolai Durov** (Telegram Messenger’ın kurucu ortağı) ve **Matei Zaharia** (Apache Spark’ın yaratıcısı). Bu isimlerin yanı sıra birçok profesyonel aynı ortak deneyimi paylaşır: bir ICPC yarışmasına katılmışlardır.

ICPC’nin ardından, özellikle 2000 yılından sonra uzaktan katılımın kolaylaşmasıyla programlama yarışmaları büyük bir gelişme gösterdi. Bu sayede uluslararası yarışmaların düzenlenmesi hem daha kolay hem de daha düşük maliyetli hale geldi. Bu yarışmaların çoğunun formatı benzerdir: bir dizi problem verilir ve katılımcıların bunları çözmek için kod yazması gerekir. Kazananlar sadece ödül kazanmakla kalmaz, aynı zamanda işe alım yapan şirketlerin dikkatini çeker veya kendi alanlarında tanınır hale gelirler.

Rekabetçi programlamadaki problemler genellikle **kombinatorik**, **sayı teorisi**, **graf teorisi**, **algoritmik oyun teorisi**, **hesaplamalı geometri**, **dizgi analizi** ve **veri yapıları** gibi konulardan oluşur. Son yıllarda ise **yapay zekâ** ile ilgili problemler de bu yarışmalarda yer almaya başlamıştır. Özellikle **KDD Cup**’ın (Knowledge Discovery and Data Mining Cup – Bilgi Keşfi ve Veri Madenciliği Yarışması) başlatılmasından sonra bu tür problemler oldukça popüler hale gelmiştir. Bu yarışma, her yıl **Association for Computing Machinery (ACM)** tarafından düzenlenen konferans kapsamında **Özel İlgi Grubu (SIG)** tarafından yürütülmektedir. (Kaynak: [https://kdd.org/conferences](https://kdd.org/conferences))

İlk **KDD Cup**, 1997 yılında düzenlenmiş ve **doğrudan pazarlamada lift eğrisi optimizasyonu** konusundaki bir problemi içermiştir. Bu yarışma, günümüzde hâlâ devam eden uzun bir yarışma serisinin başlangıcını oluşturmuştur. Veri kümeleri, yönergeler ve kazananlar dâhil olmak üzere tüm arşivlere şu adresten ulaşabilirsiniz: [https://www.kdd.org/kdd-cup](https://www.kdd.org/kdd-cup). Yazım sırasında en son mevcut olan yarışma ise [https://ogb.stanford.edu/kddcup2021/](https://ogb.stanford.edu/kddcup2021/).
KDD Cup yarışmaları, en iyi uygulamaları belirlemede oldukça etkili olmuştur. Birçok makalede yarışma çözümleri, teknikler ve veri kümeleri paylaşılmış, bu da araştırmacılar ve uygulayıcılar için **deney**, **eğitim** ve **karşılaştırma (benchmarking)** açısından büyük fayda sağlamıştır.

Hem rekabetçi programlama etkinliklerinin hem de KDD Cup’ın başarısı, şirketleri (örneğin **Netflix**) ve girişimcileri (örneğin Kaggle’ın kurucusu **Anthony Goldbloom**) **veri bilimi yarışma platformları** kurmaya teşvik etti. Bu platformlar, şirketlerin çözülmesi zor veri bilimi problemlerini kitle kaynaklı çözümlerle çözebilmesine olanak tanıdı. Gerçekten de veri bilimi alanında her problem için işe yarayan tek bir “altın” yöntem yoktur; çoğu zaman, **“deneyebileceğin her şeyi dene”** yaklaşımı gerekir.

Aslında, uzun vadede hiçbir algoritma tüm problemler için diğerlerini alt edemez. Bu durum, **David Wolpert** ve **William Macready** tarafından ortaya konan **No Free Lunch Teoremi (Bedava Öğle Yemeği Yok Teoremi)** ile açıklanır. Bu teoreme göre, her makine öğrenimi algoritması yalnızca çözümü içeren bir hipotez uzayına sahipse başarılı olur. Dolayısıyla, bir algoritmanın belirli bir problemi en iyi şekilde çözebileceğini önceden bilemezsiniz; bunu öğrenmenin tek yolu, algoritmayı doğrudan o problem üzerinde test etmektir.
Makine öğreniminde herhangi bir “kutsal kâse” veya teorik kestirme yoktur — yalnızca **ampirik deneyler** size neyin işe yaradığını gösterebilir.

Bu konuda daha fazla bilgi edinmek için **No Free Lunch Teoremi** üzerine kuramsal açıklamaları inceleyebilirsiniz. Aşağıda bu konuyu detaylı anlatan bir makaleye bağlantı verilmiştir:
👉 [Analytics India Magazine – What are the No Free Lunch Theorems in Data Science?](https://analyticsindiamag.com/what-are-the-no-free-lunch-theorems-in-data-science/)

Bu tür durumlarda **crowdsourcing (kitle kaynak kullanımı)** mükemmel bir yöntemdir; çünkü algoritmaları ve veri dönüşümlerini kapsamlı bir şekilde test etmeniz gerekir, ancak bunu yapacak insan gücü ve işlem gücünüz yoktur. Bu nedenle, hükümetler ve şirketler belirli alanlarda ilerleme kaydetmek için yarışmalara başvurur:

* **Kamu tarafında:** ABD’nin **DARPA** kuruluşu tarafından düzenlenen yarışmalarda; **otonom araçlar**, **robotik operasyonlar**, **makine çevirisi**, **konuşmacı tanıma**, **parmak izi tanıma**, **bilgi erişimi**, **OCR (Optik Karakter Tanıma)**, **otomatik hedef tanıma** gibi birçok alanda yarışmalar düzenlenmiştir.
* **Şirket tarafında:** Örneğin **Netflix**, kullanıcıların film tercihlerinin tahmin edilmesini iyileştirmek amacıyla düzenlenen bir yarışmanın sonucuna göre algoritmasını geliştirmiştir.

**Netflix Yarışması (Netflix Prize)**, mevcut **öneri sistemini** (collaborative filtering) geliştirmeyi amaçlıyordu. Yarışmanın hedefi, bir kullanıcının bir filme vereceği puanı, yalnızca daha önce puanladığı filmlerden yola çıkarak tahmin etmekti — yani kullanıcı kimliği veya film açıklamaları hakkında hiçbir bilgi yoktu (bunların tümü kimlik kodlarıyla değiştirilmişti). Katılımcılardan, mevcut puan geçmişini akıllıca kullanarak tahmin yapan modeller geliştirmeleri istendi.
**1.000.000 ABD Doları** tutarındaki büyük ödül, yalnızca geliştirilen modelin Netflix’in mevcut algoritması **Cinematch**’i belirli bir eşiğin üzerinde iyileştirmesi durumunda verilecekti.

Yarışma 2006’dan 2009’a kadar sürdü ve kazanan takım, önceki yarışmalardan birçok takımın birleşmesiyle oluştu: **Commendo Research & Consulting GmbH**’den **Andreas Töscher** ve **Michael Jahrer** (aynı zamanda Kaggle’da da tanınan yarışmacılar), **AT&T Labs**’tan iki araştırmacı ve **Yahoo!**’dan iki araştırmacı.
Yarışmayı kazanmak, o kadar büyük bir hesaplama gücü ve farklı çözümlerin birleştirilmesini (ensemble) gerektirdi ki, takımlar rekabeti sürdürebilmek için birleşmek zorunda kaldılar. Sonuçta, **Netflix** bu çözümü doğrudan uygulamak yerine, yarışmadan elde edilen en değerli içgörüleri alıp mevcut **Cinematch algoritmasını** geliştirmede kullandı.
Bu konuda daha fazla bilgi için şu **Wired** makalesini okuyabilirsiniz:
👉 [https://www.wired.com/2012/04/netflix-prize-costs/](https://www.wired.com/2012/04/netflix-prize-costs/)

Netflix yarışmasının sonunda önemli olan şey, çözümün kendisi değil, **Netflix’in iş modelinin DVD kiralamadan çevrimiçi yayın platformuna geçmesiyle** birlikte elde edilen **bilgi ve deneyimdi**. Yarışmadan hem katılımcılar (öneri sistemleri alanında büyük bir ün kazandılar) hem de Netflix (geliştirilmiş öneri sistemi bilgisini yeni iş modeline aktardı) büyük fayda sağladı.

### The Kaggle competition platform *(Kaggle yarışma platformu)*

**Netflix dışındaki birçok şirket de veri bilimi yarışmalarından fayda sağlamıştır.** Liste oldukça uzundur, ancak yarışmayı düzenleyen şirketlerin açık bir şekilde fayda elde ettiğini bildirdiği birkaç örneği verebiliriz. Örneğin:

* **Allstate** adlı sigorta şirketi, yüzlerce veri bilimcinin katıldığı bir yarışma sayesinde ([https://www.kaggle.com/c/ClaimPredictionChallenge](https://www.kaggle.com/c/ClaimPredictionChallenge)), kendi uzmanları tarafından geliştirilen aktüeryal modellerini önemli ölçüde iyileştirebilmiştir.
* Başka iyi belgelenmiş bir örnek olarak, **General Electric**, havayolu uçuşlarının varış zamanlarını tahmin etmede kullanılan sektör standardı performans ölçütüne göre (kök ortalama kare hatası – *root mean squared error* metriğiyle ölçülür) %40’lık bir gelişme sağlamıştır. Bu başarı, benzer bir yarışma sayesinde elde edilmiştir ([https://www.kaggle.com/c/flight](https://www.kaggle.com/c/flight)).

**Kaggle yarışma platformu** bugüne kadar yüzlerce yarışma düzenlemiştir ve bu iki örnek, platformu başarıyla kullanan şirketlerden yalnızca birkaçıdır.
Şimdi, belirli yarışmaların ötesine geçip bu kitabın da merkezinde yer alan **Kaggle şirketi** hakkında konuşalım.

### A history of Kaggle *(Kaggle’ın tarihçesi)*

**Kaggle**, ilk adımlarını **Şubat 2010’da**, ekonomist ve ekonometrikçi olarak eğitim almış Avustralyalı **Anthony Goldbloom** sayesinde attı. Goldbloom, Avustralya Hazine Bakanlığı’nda (*Department of the Treasury*) ve Avustralya Merkez Bankası’nın (*Reserve Bank of Australia*) Araştırma Departmanı’nda çalıştıktan sonra, Londra’da haftalık uluslararası dergi **The Economist**’te staj yaptı.

The Economist’te çalıştığı dönemde “**büyük veri (big data)**” üzerine bir makale yazma fırsatı buldu. Bu makale, onun aklına **ilginç makine öğrenimi problemlerini çözmek için en iyi analitik uzmanları kitlesel katılımla (crowdsourcing) bir araya getirecek bir yarışma platformu kurma fikrini** getirdi ([kaynak](https://www.smh.com.au/technology/from-bondi-to-the-big-bucks-the-28yearold-whos-making-datascience-a-sport-20111104-1myq1.html)).

Bu platformun iş fikrinde “crowdsourcing” dinamiklerinin önemli bir rol oynamasından dolayı, Goldbloom platformun adını **Kaggle** koydu. Bu isim, İngilizce “**gaggle**” (kaz sürüsü) kelimesine bir gönderme yapıyor; kaz figürü de zaten Kaggle platformunun sembolüdür.

Goldbloom, daha sonra **ABD’nin Silikon Vadisi’ne taşındı** ve Kaggle girişimi, iki tanınmış risk sermayesi şirketi olan **Khosla Ventures** ve **Index Ventures** tarafından yönetilen bir yatırım turunda **11,25 milyon dolar** tutarında **A Serisi yatırım** aldı. İlk yarışmalar başlatıldı, topluluk hızla büyüdü ve bazı erken dönem yarışmacılar dikkat çekici başarılara ulaştı. Bunlardan biri olan **Jeremy Howard**, Avustralyalı bir veri bilimci ve girişimciydi. Kaggle’da birkaç yarışma kazandıktan sonra şirketin **Başkanı (President)** ve **Baş Bilimcisi (Chief Scientist)** oldu.

Jeremy Howard, **Aralık 2013’te** görevinden ayrıldı ve daha sonra **fast.ai** ([www.fast.ai](http://www.fast.ai)) adlı yeni bir girişim kurdu. Bu girişim, **makine öğrenimi kursları** ve **geliştiriciler için derin öğrenme (deep learning) kütüphanesi** sunmaktadır.

O dönemde öne çıkan diğer bazı **Kaggle yarışmacıları (Kagglers)** arasında **Jeremy Achin** ve **Thomas de Godoy** da bulunuyordu. Platformda **ilk 20 küresel sıralama** arasına girdikten sonra emekli olmaya karar verdiler ve **DataRobot** adlı kendi şirketlerini kurdular. Kısa süre sonra, geliştirdikleri yazılıma en iyi makine öğrenimi bilgilerini ve uygulamalarını kazandırmak amacıyla **Kaggle yarışmalarında başarılı olmuş katılımcıları işe almaya** başladılar. Bugün **DataRobot**, **AutoML (otomatik makine öğrenimi)** çözümleri geliştiren önde gelen şirketlerden biridir.

Kaggle yarışmaları, giderek artan bir ilgiyle büyümeye devam etti. **Derin öğrenmenin “babası” Geoffrey Hinton**, 2012’de **Merck** tarafından düzenlenen bir Kaggle yarışmasına katıldı ve kazandı ([kaynak](https://www.kaggle.com/c/MerckActivity/overview/winners)).

Ayrıca Kaggle, **François Chollet**’nin derin öğrenme kütüphanesi **Keras**’ı tanıttığı **Otto Group Product Classification Challenge** ([bağlantı](https://www.kaggle.com/c/otto-group-product-classification-challenge/discussion/13632)) yarışmasının ve **Tianqi Chen**’in **XGBoost** adlı daha hızlı ve daha doğru bir **gradient boosting** algoritmasını tanıttığı **Higgs Boson Machine Learning Challenge** ([bağlantı](https://www.kaggle.com/c/higgs-boson/discussion/10335)) yarışmasının da düzenlendiği platformdur.

François Chollet ayrıca **Quora** sitesinde “Kaggle yarışmalarında neden Keras bu kadar başarılı oldu?” sorusuna verdiği cevapta, Kaggle yarışmalarında başarılı olmanın özünü mükemmel bir şekilde açıklamıştır ([kaynak](https://www.quora.com/Why-has-Keras-been-so-successful-lately-at-Kaggle-competitions)).
Ona göre, **çok sayıda denemeyi hızlı şekilde yapmak ve teoriden ziyade ampirik kanıtlarla yönlenmek**, Kaggle’da başarılı olmanın temelidir. Biz de onun belirttiği noktaların dışında başka bir “gizli sır” olduğuna inanmıyoruz.

François Chollet ayrıca Kaggle’da kendi yarışmasını da düzenlemiştir ([Abstraction and Reasoning Challenge](https://www.kaggle.com/c/abstraction-and-reasoning-challenge/)) — bu yarışma, **dünyanın ilk genel yapay zekâ (general AI) yarışması** olarak kabul edilir.

Yarışma üstüne yarışma geldikçe, Kaggle etrafındaki topluluk büyümeye devam etti ve **2017 yılında 1 milyon kullanıcıya** ulaştı. Aynı yıl, **Google Baş Bilimcisi Fei-Fei Li**, **Google Next** etkinliğinde yaptığı açılış konuşmasında **Google’ın Kaggle’ı satın alacağını** duyurdu.
O tarihten bu yana **Kaggle, Google çatısı altında** faaliyet göstermektedir.

Bugün, **Kaggle topluluğu hâlâ aktif ve büyümeye devam ediyor.**
Anthony Goldbloom’un bir tweet’inde ([kaynak](https://twitter.com/antgoldbloom/status/1400119591246852096)) belirttiği üzere, kullanıcıların büyük bir kısmı sadece yarışmalara katılmakla kalmıyor; aynı zamanda **Kaggle’ın herkese açık veri setlerini indiriyor** (Kaggle artık önemli bir **veri merkezi** haline gelmiştir), **Python veya R ile herkese açık Notebooks oluşturuyor** ya da **platformun sunduğu kurslardan yeni bir şeyler öğreniyor.**

![](./im/1001.png)

Yıllar boyunca Kaggle, katılımcılarına aşağıdaki gibi **daha pek çok fırsat** sunmuştur:

* **Kendi şirketlerini kurmak**
* **Makine öğrenimi yazılımları ve paketleri başlatmak**
* **Dergilerde röportajlar yapmak** ([kaynak](https://www.wired.com/story/solve-these-tough-data-problems-and-watch-job-offers-roll-in/))
* **Makine öğrenimi kitapları yazmak** ([kaynak](https://twitter.com/antgoldbloom/status/745662719588589568))
* **Hayallerindeki işi bulmak**

Ve en önemlisi, **veri bilimi ile ilgili beceriler ve teknik detaylar hakkında daha fazla bilgi edinmek**.

### Other competition platforms *(Diğer yarışma platformları)*

### Introducing Kaggle *(Kaggle’a giriş)*

### Stages of a competition *(Bir yarışmanın aşamaları)*

### Types of competitions and examples *(Yarışma türleri ve örnekleri)*

### Submission and leaderboard dynamics *(Gönderim ve liderlik tablosu dinamikleri)*

### Explaining the Common Task Framework paradigm *(Ortak Görev Çerçevesi paradigmasının açıklanması)*

### Understanding what can go wrong in a competition *(Bir yarışmada nelerin ters gidebileceğini anlamak)*

### Computational resources *(Hesaplama kaynakları)*

### Kaggle Notebooks *(Kaggle Defterleri)*

### Teaming and networking *(Takım kurma ve ağ oluşturma)*

### Performance tiers and rankings *(Performans seviyeleri ve sıralamalar)*

### Criticism and opportunities *(Eleştiriler ve fırsatlar)*

### Summary *(Özet)*

---

## Chapter 2: Organizing Data with Datasets *(Bölüm 2: Veri Setleriyle Veriyi Düzenleme)*

### Setting up a dataset *(Bir veri seti oluşturma)*

### Gathering the data *(Veri toplama)*

### Working with datasets *(Veri setleriyle çalışma)*

### Using Kaggle Datasets in Google Colab *(Google Colab’da Kaggle veri setlerini kullanma)*

### Legal caveats *(Yasal uyarılar)*

### Summary *(Özet)*

---

## Chapter 3: Working and Learning with Kaggle Notebooks *(Bölüm 3: Kaggle Notebooks ile Çalışmak ve Öğrenmek)*

### Setting up a Notebook *(Bir defter oluşturma)*

### Running your Notebook *(Defterinizi çalıştırma)*

### Saving Notebooks to GitHub *(Defterleri GitHub’a kaydetme)*

### Getting the most out of Notebooks *(Defterlerden en iyi şekilde yararlanma)*

### Upgrading to Google Cloud Platform (GCP) *(Google Cloud Platform’a (GCP) yükseltme)*

### One step beyond *(Bir adım öteye geçmek)*

### Kaggle Learn courses *(Kaggle Learn kursları)*

### Summary *(Özet)*

---

## Chapter 4: Leveraging Discussion Forums *(Bölüm 4: Tartışma Forumlarını Etkin Kullanma)*

### How forums work *(Forumlar nasıl çalışır)*

### Example discussion approaches *(Tartışma örnekleri ve yaklaşımlar)*

### Netiquette *(İnternet görgü kuralları)*

### Summary *(Özet)*

---

# Part II: Sharpening Your Skills for Competitions *(Bölüm II: Yarışmalar İçin Becerilerini Geliştirme)*

## Chapter 5: Competition Tasks and Metrics *(Bölüm 5: Yarışma Görevleri ve Ölçütleri)*

### Evaluation metrics and objective functions *(Değerlendirme metrikleri ve hedef fonksiyonlar)*

### Basic types of tasks *(Temel görev türleri)*

#### Regression *(Regresyon)*

#### Classification *(Sınıflandırma)*

#### Ordinal *(Sıralı veriler)*

### The Meta Kaggle dataset *(Meta Kaggle veri seti)*

### Handling never-before-seen metrics *(Daha önce görülmemiş metriklerle başa çıkma)*

### Metrics for regression (standard and ordinal) *(Regresyon için metrikler - standart ve sıralı)*

#### Mean squared error (MSE) and R² *(Ortalama kare hata (MSE) ve R²)*

#### Root mean squared error (RMSE) *(Kök ortalama kare hata (RMSE))*

#### Root mean squared log error (RMSLE) *(Kök ortalama log kare hata (RMSLE))*

#### Mean absolute error (MAE) *(Ortalama mutlak hata (MAE))*

### Metrics for classification (label prediction and probability) *(Sınıflandırma metrikleri - etiket tahmini ve olasılık)*

#### Accuracy *(Doğruluk)*

#### Precision and recall *(Kesinlik ve duyarlılık)*

#### The F1 score *(F1 skoru)*

#### Log loss and ROC-AUC *(Log kaybı ve ROC-AUC)*

#### Matthews correlation coefficient (MCC) *(Matthews korelasyon katsayısı)*

### Metrics for multi-class classification *(Çok sınıflı sınıflandırma metrikleri)*

### Metrics for object detection problems *(Nesne tespiti problemleri için metrikler)*

#### Intersection over union (IoU) *(Kesişim/Birleşim oranı)*

#### Dice *(Dice katsayısı)*

### Metrics for multi-label classification and recommendation problems *(Çok etiketli sınıflandırma ve öneri problemleri için metrikler)*

#### MAP@K *(MAP@K metriği)*

### Optimizing evaluation metrics *(Değerlendirme metriklerini optimize etme)*

### Custom metrics and custom objective functions *(Özel metrikler ve özel hedef fonksiyonları)*

### Post-processing your predictions *(Tahminleri sonradan işleme)*

### Predicted probability and its adjustment *(Tahmin edilen olasılığın ayarlanması)*

### Summary *(Özet)*

---

## Chapter 6: Designing Good Validation *(Bölüm 6: İyi Bir Doğrulama Sistemi Tasarlama)*

### Snooping on the leaderboard *(Liderlik tablosunu gözetlemek)*

### The importance of validation in competitions *(Yarışmalarda doğrulamanın önemi)*

### Bias and variance *(Önyargı ve varyans)*

### Trying different splitting strategies *(Farklı veri bölme stratejilerini denemek)*

#### The basic train-test split *(Temel eğitim-test bölünmesi)*

#### Probabilistic evaluation methods *(Olasılıksal değerlendirme yöntemleri)*

#### k-fold cross-validation *(k-katlı çapraz doğrulama)*

#### Subsampling *(Alt örnekleme)*

#### The bootstrap *(Bootstrap yöntemi)*

### Tuning your model validation system *(Model doğrulama sistemini ayarlamak)*

### Using adversarial validation *(Zıt doğrulama yöntemini kullanmak)*

#### Example implementation *(Uygulama örneği)*

### Handling different distributions of training and test data *(Eğitim ve test verilerindeki farklı dağılımlarla başa çıkma)*

### Handling leakage *(Veri sızıntısını önleme)*

### Summary *(Özet)*

---

## Chapter 7: Modeling for Tabular Competitions *(Bölüm 7: Tablo Verisi Yarışmaları İçin Modellemede Yaklaşımlar)*

### The Tabular Playground Series *(Tabular Playground Serisi)*

### Setting a random state for reproducibility *(Tekrarlanabilirlik için rastgele durum belirleme)*

### The importance of EDA *(Keşifsel veri analizinin önemi)*

### Dimensionality reduction with t-SNE and UMAP *(t-SNE ve UMAP ile boyut indirgeme)*

### Reducing the size of your data *(Veri boyutunu küçültme)*

### Applying feature engineering *(Özellik mühendisliği uygulama)*

#### Easily derived features *(Kolay türetilen özellikler)*

#### Meta-features based on rows and columns *(Satır ve sütunlara dayalı meta-özellikler)*

#### Target encoding *(Hedef kodlama)*

### Using feature importance to evaluate your work *(Özellik önemini kullanarak çalışmanı değerlendirme)*

### Pseudo-labeling *(Sahte etiketleme)*

### Denoising with autoencoders *(Otoenkoderlerle gürültü giderme)*

### Neural networks for tabular competitions *(Tablo verisi yarışmaları için sinir ağları)*

### Summary *(Özet)*

---

## Chapter 8: Hyperparameter Optimization *(Bölüm 8: Hiperparametre Optimizasyonu)*

### Basic optimization techniques *(Temel optimizasyon teknikleri)*

#### Grid search *(Izgara araması)*

#### Random search *(Rastgele arama)*

#### Halving search *(Yarıya indirme araması)*

### Key parameters and how to use them *(Temel parametreler ve nasıl kullanılacakları)*

#### Linear models *(Doğrusal modeller)*

#### Support-vector machines *(Destek vektör makineleri)*

#### Random forests and extremely randomized trees *(Rastgele ormanlar ve aşırı rastgele ağaçlar)*

#### Gradient tree boosting *(Gradyan ağaç güçlendirmesi)*

#### LightGBM *(LightGBM algoritması)*

#### XGBoost *(XGBoost algoritması)*

#### CatBoost *(CatBoost algoritması)*

#### HistGradientBoosting *(Histogram tabanlı gradyan güçlendirme)*

### Bayesian optimization *(Bayesyen optimizasyon)*

#### Using Scikit-optimize *(Scikit-optimize kullanımı)*

#### Customizing a Bayesian optimization search *(Bayesyen aramayı özelleştirme)*

#### Extending Bayesian optimization to neural architecture search *(Bayesyen optimizasyonu sinir ağı mimarisi aramasına genişletme)*

#### Creating lighter and faster models with KerasTuner *(KerasTuner ile daha hafif ve hızlı modeller oluşturma)*

#### The TPE approach in Optuna *(Optuna’daki TPE yaklaşımı)*

### Summary *(Özet)*

---

## Chapter 9: Ensembling with Blending and Stacking Solutions *(Bölüm 9: Karıştırma ve Yığınlama (Ensemble) Çözümleri)*

### A brief introduction to ensemble algorithms *(Topluluk (ensemble) algoritmalarına kısa bir giriş)*

### Averaging models into an ensemble *(Modelleri ortalama alarak birleştirme)*

#### Majority voting *(Çoğunluk oylaması)*

#### Averaging of model predictions *(Model tahminlerinin ortalaması)*

#### Weighted averages *(Ağırlıklı ortalamalar)*

#### Averaging in your cross-validation strategy *(Çapraz doğrulama stratejinde ortalama alma)*

#### Correcting averaging for ROC-AUC evaluations *(ROC-AUC değerlendirmeleri için ortalamayı düzeltme)*

### Blending models using a meta-model *(Meta-model kullanarak modelleri karıştırma)*

#### Best practices for blending *(Karıştırma için en iyi uygulamalar)*

### Stacking models together *(Modelleri yığınlama)*

#### Stacking variations *(Yığınlama varyasyonları)*

### Creating complex stacking and blending solutions *(Karmaşık karıştırma ve yığınlama çözümleri oluşturma)*

### Summary *(Özet)*

---

## Chapter 10: Modeling for Computer Vision *(Bölüm 10: Bilgisayarlı Görü (Computer Vision) için Modellemede Yaklaşımlar)*

### Augmentation strategies *(Veri artırma stratejileri)*

#### Keras built-in augmentations *(Keras’ın yerleşik artırmaları)*

#### ImageDataGenerator approach *(ImageDataGenerator yaklaşımı)*

#### Preprocessing layers *(Ön işleme katmanları)*

#### albumentations *(Albumentations kütüphanesi)*

### Classification *(Sınıflandırma)*

### Object detection *(Nesne tespiti)*

### Semantic segmentation *(Anlamsal segmentasyon)*

### Summary *(Özet)*

---

## Chapter 11: Modeling for NLP *(Bölüm 11: Doğal Dil İşleme (NLP) için Modellemede Yaklaşımlar)*

### Sentiment analysis *(Duygu analizi)*

### Open domain Q&A *(Açık alan soru-cevap)*

### Text augmentation strategies *(Metin artırma stratejileri)*

#### Basic techniques *(Temel teknikler)*

#### nlpaug *(nlpaug kütüphanesi)*

### Summary *(Özet)*

---

## Chapter 12: Simulation and Optimization Competitions *(Bölüm 12: Simülasyon ve Optimizasyon Yarışmaları)*

### Connect X *(Connect X oyunu)*

### Rock-paper-scissors *(Taş-kağıt-makas)*

### Santa competition 2020 *(Santa yarışması 2020)*

### The name of the game *(Oyunun özü)*

### Summary *(Özet)*

---

# Part III: Leveraging Competitions for Your Career *(Bölüm III: Yarışmaları Kariyerinde Avantaja Dönüştürme)*

## Chapter 13: Creating Your Portfolio of Projects and Ideas *(Bölüm 13: Proje ve Fikir Portföyü Oluşturma)*

### Building your portfolio with Kaggle *(Kaggle ile portföy oluşturma)*

### Leveraging Notebooks and discussions *(Defterler ve tartışmalardan yararlanma)*

### Leveraging Datasets *(Veri setlerinden yararlanma)*

### Arranging your online presence beyond Kaggle *(Kaggle dışında çevrimiçi varlığını düzenleme)*

#### Blogs and publications *(Bloglar ve yayınlar)*

#### GitHub *(GitHub)*

### Monitoring competition updates and newsletters *(Yarışma güncellemelerini ve bültenleri takip etme)*

### Summary *(Özet)*

---

## Chapter 14: Finding New Professional Opportunities *(Bölüm 14: Yeni Profesyonel Fırsatlar Bulmak)*

### Building connections with other competition data scientists *(Diğer yarışmacı veri bilimcilerle bağlantı kurma)*

### Participating in Kaggle Days and other Kaggle meetups *(Kaggle Days ve diğer Kaggle buluşmalarına katılma)*

### Getting spotted and other job opportunities *(Fark edilmek ve diğer iş fırsatları)*

#### The STAR approach *(STAR yaklaşımı)*

### Summary (and some parting words) *(Özet ve kapanış notları)*

---

## Other Books You May Enjoy *(Hoşunuza Gidebilecek Diğer Kitaplar)*

## Index *(Dizin)*
