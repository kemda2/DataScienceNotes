# Bölüm 1: En Ünlü Tablosal Yarışma – Porto Seguro'nun Güvenli Sürücü Tahmini *(Chapter 1: The Most Renowned Tabular Competition – Porto Seguro’s Safe Driver Prediction)*

Herhangi bir Kaggle yarışmasında liderlik tablosunun zirvesine nasıl ulaşılacağını öğrenmek sabır, **azim** ve en iyi sonuçları elde etmek için rekabet etmenin en iyi yolunu öğrenmek için **birçok deneme** gerektirir. Bu nedenle, geçmişteki bazı Kaggle yarışmalarını deneyerek ve tartışmaları okuyarak, not defterlerini (notebooks) yeniden kullanarak, **özellik mühendisliği** (feature engineering) yaparak ve çeşitli modelleri eğiterek liderlik tablosunun zirvesine nasıl ulaşılacağını öğrenerek bu becerileri daha hızlı geliştirmenize yardımcı olabilecek bir çalışma kitabı düşündük.

En ünlü tablo (tabular) yarışmalarından biri olan **Porto Seguro’s Safe Driver Prediction** ile başlıyoruz. Bu yarışmada, sigortacılıkta yaygın bir sorunu çözmeniz ve önümüzdeki yıl kimin araç sigortası talebinde bulunacağını bulmanız isteniyor. Böyle bir bilgi, talepte bulunma olasılığı daha yüksek olan sürücüler için sigorta ücretini artırmak ve olasılığı daha düşük olanlar için düşürmek açısından faydalıdır.

Bu yarışmayı çözmek için gerekli olan temel içgörüleri ve teknik detayları açıklarken, size gerekli kodu gösterecek ve **The Kaggle Book**’ta bulunan konuları incelemenizi ve soruları yanıtlamanızı isteyeceğiz. Öyleyse, daha fazla gecikmeden, bu yeni öğrenme yolunuza başlayalım.

Bu bölümde şunları öğreneceksiniz:

  * Bir **LightGBM** modelini nasıl ayarlayacağınız (tune) ve eğiteceğiniz
  * Bir **gürültü giderici otomatik kodlayıcıyı (denoising autoencoder)** nasıl oluşturacağınız ve bunu bir sinir ağını beslemek için nasıl kullanacağınız
  * Birbirinden oldukça farklı olan modelleri **etkili bir şekilde nasıl harmanlayacağınız (blend)**

> Bu bölümdeki tüm kod dosyaları **[https://packt.link/kwbchp1](https://www.google.com/search?q=https://packt.link/kwbchp1)** adresinde bulunabilir.

## Yarışmayı ve veriyi anlama *(Understanding the competition and the data)*

Porto Seguro, Brezilya'nın (Brezilya ve Uruguay'da faaliyet gösteren) üçüncü büyük sigorta şirketidir; otomobil sigortası teminatının yanı sıra pek çok başka sigorta ürünü de sunmaktadır ve son 20 yıldır fiyatlarını belirlemek ve otomatik sigorta teminatını daha fazla sürücü için daha erişilebilir kılmak amacıyla analitik yöntemler ve makine öğrenimi kullanmaktadır. Görevlerini başarmanın yeni yollarını keşfetmek amacıyla, Kaggle kullanıcılarının (Kagglers) bazı temel analitik problemlerine yeni ve daha iyi çözümler bulmalarını bekleyerek bir yarışmaya ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction)) sponsor olmuşlardır.

Yarışma, Kaggle kullanıcılarının, bir sürücünün önümüzdeki yıl bir otomobil sigortası talebi başlatma olasılığını tahmin eden bir model oluşturmasını amaçlamaktadır ki bu oldukça yaygın bir görev türüdür (sponsor bunu "sigortacılık için klasik bir meydan okuma" olarak bahsetmektedir). Talepte bulunma olasılığı hakkındaki bu tür bilgiler, bir sigorta şirketi için oldukça değerli olabilir. Böyle bir model olmadan, sigorta şirketleri müşterilerine risklerine bakılmaksızın yalnızca sabit bir prim uygulayabilir veya kötü performans gösteren bir modele sahiplerse, onlara **uygunsuz bir prim** uygulayabilirler. Müşterilerin riskini profilendirmedeki yanlışlıklar, iyi sürücülere daha yüksek sigorta maliyeti yüklenmesine ve kötü sürücüler için fiyatın düşürülmesine yol açabilir. Bunun şirket üzerindeki etkisi iki yönlü olacaktır: iyi sürücüler sigortalarını başka yerlerde arayacak ve şirketin portföyü kötü sürücülerle aşırı yüklenecektir (teknik olarak, şirketin **kötü bir hasar oranı** olacaktır: [https://www.investopedia.com/terms/l/loss-ratio.asp](https://www.investopedia.com/terms/l/loss-ratio.asp)). Bunun yerine, şirket talep olasılığını doğru bir şekilde tahmin edebilirse, müşterilerinden **adil bir fiyat** isteyebilir, böylece pazar paylarını artırabilir, daha memnun müşterilere ve daha dengeli bir müşteri portföyüne (daha iyi hasar oranı) sahip olabilir ve rezervlerini (şirketin talepleri ödemek için ayırdığı para) daha iyi yönetebilir.

Bunu yapmak için, sponsor eğitim ve test veri kümeleri sağlamıştır ve veri kümesi çok büyük olmadığı ve çok iyi hazırlanmış göründüğü için yarışma herkes için idealdi.

Verilerin sunulmasına ayrılmış olan yarışma sayfasında ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/data](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/data)) belirtildiği gibi:

> Benzer gruplara ait özellikler, özellik adlarında bu şekilde etiketlenmiştir (örneğin, ind, reg, car, calc).
>
> Ek olarak, özellik adları ikili (binary) özellikleri belirtmek için **bin** ve kategorik özellikleri belirtmek için **cat** sonekini içerir. Bu tanımlamalara sahip olmayan özellikler ya sürekli (continuous) ya da sıralıdır (ordinal). **-1** değerleri, özelliğin gözlemde **eksik** olduğunu gösterir. **Hedef (target)** sütunu, poliçe sahibi için bir talep açılıp açılmadığını gösterir.

Yarışma için veri hazırlığı, herhangi bir bilgi sızıntısını (leak) önlemek için dikkatlice yapılmıştır ve özelliklerin anlamı hakkında gizlilik korunmuş olsa da, kullanılan farklı etiketlerin motorlu taşıt sigortası modellemesinde yaygın olarak kullanılan belirli özellik türlerine atıfta bulunduğu oldukça açıktır:

  * **ind**: "Bireysel özellikler" (individual characteristics)
  * **car**: "Araba özellikleri" (car characteristics)
  * **calc**: "Hesaplanmış özellikler" (calculated features)
  * **reg**: "Bölgesel/coğrafi özellikler" (regional/geographic features)

Bireysel özelliklere gelince, yarışma sırasında anlamları hakkında çok fazla spekülasyon yapılmıştır. Örneğin şuraya bakınız:

  * [https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41489](https://www.google.com/search?q=https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41489), burada Raddar, **ps\_car\_13** özelliğinin iki yılda bir zorunlu araç muayeneleri arasında kat edilen mesafeyi temsil edebileceğini öne sürmektedir.
  * [https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41488](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41488), burada Raddar, **ps\_car\_12** özelliğinin bunun yerine motor silindir hacmini temsil edebileceğini öne sürmektedir.
  * [https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41057](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41057), burada bazı özelliklerin Porto Seguro'nun çevrimiçi teklif formundan türetildiği yönündeki öneriyi okuyabilirsiniz.

Tüm bu ve daha fazla çabaya rağmen, sonunda özelliklerin çoğunun anlamı şimdiye kadar bir **gizem olarak kalmıştır**.

Bu yarışmanın ilginç gerçekleri şunlardır:

1.  Veriler, özellikler anonim olsa da, **gerçek dünyadan** alınmıştır.
2.  Veriler, herhangi bir tür sızıntı olmaksızın **çok iyi hazırlanmıştır** (burada sihirli özellikler yoktur – sihirli özellik, becerikli bir işlemle Kaggle yarışmasında modellerinize yüksek tahmin gücü sağlayabilen bir özelliktir).
3.  Test veri seti sadece eğitim veri setiyle aynı kategorik seviyeleri tutmakla kalmaz; aynı zamanda aynı dağılımdan geliyormuş gibi görünmektedir, ancak Yuya Yamamoto, verilerin t-SNE ile ön işlenmesinin düşmanca doğrulama (adversarial validation) testinin başarısız olmasına yol açtığını iddia etmektedir ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/44784](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/44784)).

> 📝 Alıştırma 1
> 
> 
> 
> İlk alıştırma olarak, **The Kaggle Book**'taki **düşmanca doğrulama (adversarial validation)** ile ilgili içeriklere ve koda (sayfa 179'dan başlayarak) atıfta bulunarak, eğitim ve test verilerinin **büyük olasılıkla aynı veri dağılımından** kaynaklandığını kanıtlayınız.
> 
> 
> 
> **Alıştırma Notları** (Size yardımcı olacak tüm notları veya çalışmaları buraya yazınız):

Tilii (Mensur Dlakic, Montana Eyalet Üniversitesi'nde Doçent: [https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/42197](https://www.google.com/search?q=https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/42197)) tarafından yapılan ilginç bir paylaşım, t-SNE kullanarak şunu göstermektedir: "**Sigorta parametreleri açısından çok benzer olan birçok insan vardır, ancak bunlardan bazıları talepte bulunacak, bazıları ise bulunmayacaktır**." Tilii'nin bahsettiği şey, sigortacılıkta olanlara oldukça tipiktir; belirli öncüller (sigorta parametreleri) için bir şeyin olma olasılığı aynıdır, ancak o olay, olaylar dizisini ne kadar süre gözlemlediğimize bağlı olarak gerçekleşir veya gerçekleşmez.

Örneğin, sigortacılıkta IoT ve telematik verilerini ele alalım. Bir sürücünün gelecekte talepte bulunup bulunmayacağını tahmin etmek için sürüş davranışını analiz etmek oldukça yaygındır. Gözlem süreniz **çok kısaysa** (örneğin, bu yarışmada olduğu gibi bir yıl), çok kötü sürücülerin bile bir talepte bulunmaması söz konusu olabilir, çünkü kötü bir sürücü için bile böyle bir olayın kısa bir zaman diliminde meydana gelme olasılığı düşüktür.

Benzer fikirler, Andy Harless ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/42735](https://www.google.com/search?q=https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/42735)) tarafından tartışılmaktadır; Harless, bunun yerine yarışmanın gerçek görevinin "kazaya daha yatkın olan sürücüleri belirleyen **gizli bir sürekli değişkenin değerini tahmin etmek**" olduğunu savunur, çünkü aslında "**talepte bulunmak bir sürücünün özelliği değil; şansın bir sonucudur**."

## Değerlendirme metriğini anlama
*(Understanding the evaluation metric)*

Elbette, metninizi Türkçeye çevirdim:

-----

Yarışmada kullanılan metrik, **normalleştirilmiş Gini katsayısıdır** (ekonomide kullanılan benzer Gini katsayısı/endeksinden almıştır) ve daha önce başka bir yarışmada, Allstate Claim Prediction Challenge'da ([https://www.kaggle.com/competitions/ClaimPredictionChallenge](https://www.google.com/search?q=https://www.kaggle.com/competitions/ClaimPredictionChallenge)) kullanılmıştır. Bu yarışmadan, metriğin ne hakkında olduğuna dair çok net bir açıklama alabiliriz:

> Bir giriş gönderdiğinizde, gözlemler "en büyük tahminden" "en küçük tahmine" doğru sıralanır. Tahminlerinizin devreye girdiği tek adım budur, bu nedenle yalnızca tahminlerinizin belirlediği sıra önemlidir. Gözlemleri soldan sağa, en büyük tahminler solda olacak şekilde düzenlenmiş olarak görselleştirin. Ardından soldan sağa hareket ederek şunu sorarız: "**Verinin en soldaki %x'lik kısmında, gerçekten gözlemlenen kaybın ne kadarını biriktirdiniz?**" Bir model olmadan, tahminlerin %10'unda kaybın %10'unu biriktirmeyi beklersiniz, bu nedenle model olmaması (veya bir "sıfır" modeli) düz bir çizgiye ulaşır. **Sizin eğriniz ile bu düz çizgi arasındaki alana Gini katsayısı diyoruz.**
>
> "Mükemmel" bir model için ulaşılabilecek maksimum bir alan vardır. Biz, modelinizin Gini katsayısını mükemmel modelin Gini katsayısına bölerek **normalleştirilmiş Gini katsayısını** kullanacağız.

Daha iyi bir açıklama da Kilian Batzner'ın not defterinde (notebook) sağlanmıştır: [https://www.kaggle.com/code/batzner/gini-coefficient-an-intuitive-explanation](https://www.kaggle.com/code/batzner/gini-coefficient-an-intuitive-explanation). Kilian, net çizimler ve bazı basit örnekler kullanarak, sigorta şirketlerinin aktüerya departmanları tarafından rutin olarak kullanılan, ancak çok yaygın olmayan bu metriği anlamlandırmaya çalışmaktadır.

Bu metrik, yaklaşık olarak $2 \cdot \text{ROC-AUC} - 1$ formülüne karşılık geldiği için **ROC-AUC skoru** veya **Mann–Whitney U non-parametrik istatistiksel testi** (U istatistiği, alıcı işletim karakteristiği eğrisi altındaki alana – AUC'ye eşdeğer olduğundan) ile yaklaştırılabilir. Dolayısıyla, **ROC-AUC'yi maksimize etmek, normalleştirilmiş Gini katsayısını maksimize etmekle aynıdır** (bir referans için Wikipedia girişindeki *Diğer istatistiksel ölçümlerle ilişki* bölümüne bakınız: [https://en.wikipedia.org/wiki/Gini\_coefficient](https://en.wikipedia.org/wiki/Gini_coefficient)).

Metrik, ayrıca ölçeklenmiş tahmin sırası (rank) ile ölçeklenmiş hedef değerinin kovaryansı olarak da yaklaşık olarak ifade edilebilir, bu da daha anlaşılır bir sıra ilişkisi ölçütü sağlar (bkz. Dmitriy Guller: [https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/40576](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/40576)).

**Amaç fonksiyonu** (objective function) açısından bakıldığında, bir sınıflandırma probleminde yapacağınız gibi **ikili log-kaybı** (binary log-loss) için optimizasyon yapabilirsiniz. Ne ROC-AUC ne de normalleştirilmiş Gini katsayısı türevlenebilir değildir ve bunlar yalnızca doğrulama kümesi üzerindeki metrik değerlendirmesi için kullanılabilir (örneğin, erken durdurma veya bir sinir ağında öğrenme hızını azaltma için). Ancak, log-kaybı için optimizasyon yapmak her zaman ROC-AUC'yi ve normalleştirilmiş Gini katsayılarını iyileştirmez ve ikisi de doğrudan türevlenebilir değildir.

> Aslında türevlenebilir bir ROC-AUC yaklaştırması mevcuttur. Bunun nasıl çalıştığını Toon Calders ve Szymon Jaroszewicz'in *Efficient AUC Optimization for Classification* adlı çalışmasında okuyabilirsiniz. European Conference on Principles of Data Mining and Knowledge Discovery. Springer, Berlin, Heidelberg, 2007: [https://link.springer.com/content/pdf/10.1007/978-3-540-74976-9\_8.pdf](https://link.springer.com/content/pdf/10.1007/978-3-540-74976-9_8.pdf).

Ancak, yarışmada bir amaç fonksiyonu olarak log-kaybından farklı bir şey kullanmaya ve değerlendirme metriği olarak ROC-AUC veya normalleştirilmiş Gini katsayısından başka bir şey kullanmaya gerek olmadığı anlaşılmaktadır.

Kaggle Not Defterleri arasında normalleştirilmiş Gini katsayısını hesaplamak için aslında birkaç Python uygulaması bulunmaktadır. Burada, CPMP'nin ([https://www.kaggle.com/code/cpmpml/extremely-fast-gini-computation/notebook](https://www.google.com/search?q=https://www.kaggle.com/code/cpmpml/extremely-fast-gini-computation/notebook)) **Numba** kullanarak hesaplamaları hızlandıran çalışmasını kullandık ve öneriyoruz: bu hem kesin hem de hızlıdır.

> 📝 Alıştırma 2
> 
> 
> 
> **The Kaggle Book**'un 5. bölümünde (sayfa 95 ve sonrası), özellikle yeni ve genellikle bilinmeyen yarışma metrikleriyle nasıl başa çıkılacağını açıklamıştık.
> 
> 
> 
> Bir alıştırma olarak, Kaggle'da **normalleştirilmiş Gini katsayısını** bir değerlendirme metriği olarak kullanan **kaç yarışma** olduğunu bulabilir misiniz?
> 
> 
> 
> **Alıştırma Notları** (Size yardımcı olacak tüm notları veya çalışmaları buraya yazınız):

## Michael Jahrer'ın en iyi çözüm fikirlerini inceleme *(Examining the top solution ideas from Michael Jahrer)*

Michael Jahrer ([https://www.kaggle.com/mjahrer](https://www.kaggle.com/mjahrer), yarışma Büyük Ustası ve Netflix Ödülü'nün "BellKor's Pragmatic Chaos" ekibindeki kazananlarından biri), yarışma boyunca halka açık liderlik tablosunu uzun süre ve belirgin bir farkla önde götürdü ve özel çözümler nihayet açıklandığında kazanan ilan edildi.

Kısa süre sonra, tartışma forumunda, **gürültü giderici otomatik kodlayıcıları (denoising autoencoders)** ve sinir ağlarını akıllıca kullanması nedeniyle birçok Kaggler için bir referans haline gelen çözümünün kısa bir özetini yayınladı ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/44629](https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/44629)). Michael, çözümüne ilişkin herhangi bir Python kodu eklememiş olsa da (kodlama çalışmasını doğrudan C++/CUDA ile Python kullanmadan yazılmış "eski usul" ve "düşük seviyeli" olarak tanımlamıştır), yazıları kullandığı modellere, hiperparametrelerine ve mimarilerine dair referanslar açısından oldukça zengindir.

Öncelikle Michael, çözümünün altı modelin bir karışımından (bir LightGBM modeli ve beş sinir ağı) oluştuğunu açıklamaktadır. Ayrıca, muhtemelen **aşırı öğrenme (overfitting)** nedeniyle, karışıma her bir modelin katkısını ağırlıklandırmanın (ayrıca doğrusal ve doğrusal olmayan yığınlama (stacking) yapmanın) bir avantaj sağlamadığı için, yalnızca farklı tohumlardan (seed) oluşturulmuş, **eşit ağırlığa sahip** modellerin bir karışımına başvurduğunu belirtmektedir.

Bu içgörü, Michael'ın yaklaşımını çoğaltma görevimizi çok daha kolay hale getiriyor, çünkü kendisi ayrıca, **yalnızca LightGBM sonuçlarını oluşturduğu sinir ağlarından biriyle karıştırmanın (blending)** bile yarışmada birinciliği garantilemek için yeterli olacağını da belirtmiştir.

Bu içgörü, alıştırma çalışmamızı bir sürü model yerine, iki iyi tekil modelle sınırlayacaktır. Buna ek olarak, Michael, bazı sütunları çıkarmak ve kategorik özellikleri **tek-sıcak kodlama (one-hot encoding)** dışında çok az veri işleme yaptığını da belirtmiştir.

## Bir LightGBM gönderimi oluşturma *(Building a LightGBM submission)*

Alıştırmamız LightGBM tabanlı bir çözüm üzerinde çalışmakla başlıyor. Kodu, Kaggle Notebooks'u kullanarak hemen yürütülmeye hazır şekilde şu adreste bulabilirsiniz: [https://www.kaggle.com/code/lucamassaron/workbook-lgb](https://www.kaggle.com/code/lucamassaron/workbook-lgb). Kodu kolayca erişilebilir hale getirmiş olsak da, bunun yerine kodu doğrudan kitaptan yazmanızı veya kopyalamanızı ve hücre hücre çalıştırmanızı öneriyoruz; her bir kod satırının ne yaptığını anlamak ve çözümü kişiselleştirmek, performansını daha da artırabilir.

> LightGBM kullanırken, **GPU veya TPU hızlandırıcılarından hiçbirini açmanıza gerek yoktur ve açmamalısınız**. GPU hızlandırması yalnızca LightGBM'nin GPU sürümünü yüklediyseniz yardımcı olabilir. Bu tür GPU hızlandırmalı bir sürümü Kaggle Notebooks'a nasıl kuracağınıza dair çalışma ipuçlarını şu örnekte bulabilirsiniz: [https://www.kaggle.com/code/lucamassaron/gpu-accelerated-lightgbm](https://www.kaggle.com/code/lucamassaron/gpu-accelerated-lightgbm).

Anahtar paketleri (hiperparametre optimizasyonu için NumPy, pandas ve Optuna, LightGBM ve bazı yardımcı fonksiyonlar) içe aktararak başlıyoruz. Ayrıca bir konfigürasyon sınıfı tanımlıyor ve onu örneklendiriyoruz. Konfigürasyon sınıfında tanımlanan parametreleri, kodun ilerleyişi sırasında keşfederken tartışacağız. Burada önemli olan nokta, tüm parametrelerinizi içeren bir sınıf kullanarak, onları kod boyunca tutarlı bir şekilde değiştirmenizin daha kolay olacağıdır. Yarışmanın hararetinde, kodun birden fazla yerinde atıfta bulunulan bir parametreyi güncellemeyi unutmak kolaydır ve parametreler hücrelere ve fonksiyonlara dağılmışsa bunları ayarlamak her zaman zordur. Bir konfigürasyon sınıfı, size çok çaba kazandırabilir ve yol boyunca hatalardan koruyabilir:

```python
import numpy as np
import pandas as pd
import optuna
import lightgbm as lgb
from path import Path
from sklearn.model_selection import StratifiedKFold

class Config:
    input_path = Path('../input/porto-seguro-safe-driver-prediction')
    optuna_lgb = False
    n_estimators = 1500
    early_stopping_round = 150
    cv_folds = 5
    random_state = 0
    params = {'objective': 'binary',
              'boosting_type': 'gbdt',
              'learning_rate': 0.01,
              'max_bin': 25,
              'num_leaves': 31,
              'min_child_samples': 1500,
              'colsample_bytree': 0.7,
              'subsample_freq': 1,
              'subsample': 0.7,
              'reg_alpha': 1.0,
              'reg_lambda': 1.0,
              'verbosity': 0,
              'random_state': 0}

config = Config()
```

Bir sonraki adım, eğitim, test ve örnek gönderim veri setlerini içe aktarmayı gerektirir. Bunu pandas'ın `read_csv` fonksiyonunu kullanarak yapıyoruz. Ayrıca, yüklenen `DataFrame`'lerin indeksini her bir veri örneğinin tanımlayıcısına (`id` sütununa) ayarlıyoruz.

Benzer gruplara ait özellikler etiketlendiği (`ind`, `reg`, `car` ve `calc` etiketlerini kullanarak) ve ayrıca ikili (binary) ve kategorik özellikler kolayca bulunabildiği (etiketlerinde sırasıyla `bin` ve `cat` etiketlerini kullanırlar), bunları numaralandırabilir ve listelere kaydedebiliriz:

```python
train = pd.read_csv(config.input_path / 'train.csv', index_col='id')
test = pd.read_csv(config.input_path / 'test.csv', index_col='id')
submission = pd.read_csv(config.input_path / 'sample_submission.csv', 
index_col='id')
calc_features = [feat for feat in train.columns if "_calc" in feat]
cat_features = [feat for feat in train.columns if "_cat" in feat]
```

Daha sonra, sadece hedefi (0'lar ve 1'lerden oluşan ikili bir hedef) çıkarır ve eğitim veri setinden kaldırırız:

```python
target = train["target"]
train = train.drop("target", axis="columns")
```

Bu noktada, Michael Jahrer'in de işaret ettiği gibi, `calc` özelliklerini silebiliriz. Bu fikir, özellikle not defterlerinde yarışma sırasında çokça yinelenmiştir ([https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41970](https://www.google.com/search?q=https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/discussion/41970)), çünkü bunları silmenin hem yerel çapraz doğrulama skorunu hem de halka açık liderlik tablosu skorunu iyileştirdiği deneysel olarak doğrulanabilmiştir (genel bir kural olarak, özellik seçimi sırasında her ikisini de takip etmek önemlidir). Ayrıca, bu özellikler gradyan artırma (gradient boosting) modellerinde de kötü performans göstermiştir (önemleri her zaman ortalamanın altındadır).

Tartışabiliriz ki, bu özellikler mühendislik ürünü özellikler oldukları için, orijinal özelliklerine göre yeni bir bilgi içermezler, ancak onları içeren eğitilmiş herhangi bir modele sadece gürültü katarlar:

```python
train = train.drop(calc_features, axis="columns")
test = test.drop(calc_features, axis="columns")
```

> 📝 Alıştırma 3
> 
> 
> 
> **The Kaggle Book**'un 220. sayfasında (Çalışmanızı değerlendirmek için özellik önemini kullanma başlığı altında) sağlanan önerilere dayanarak, bir alıştırma olarak:
> 
> 
> 
> 1.  Bu yarışma için **kendi özellik seçimi** not defterinizi (notebook) kodlayın.
> 
> 2.  **Hangi özelliklerin tutulması** ve **hangilerinin atılması** gerektiğini kontrol edin.
> 
> 
> 
> **Alıştırma Notları** (Size yardımcı olacak tüm notları veya çalışmaları buraya yazınız):

## Bir gürültü giderici otomatik kodlayıcı (denoising autoencoder) ve bir DNN kurma *(Setting up a denoising autoencoder and a DNN)*

## Sonuçları birleştirme *(Ensembling the results)*

## Özet *(Summary)*

---

# Bölüm 2: Makridakis Yarışmaları – Doğruluk ve Belirsizlik İçin Kaggle'daki M5 *(Chapter 2: The Makridakis Competitions – M5 on Kaggle for Accuracy and Uncertainty)*

## Yarışmayı ve veriyi anlama *(Understanding the competition and the data)*

## Değerlendirme Metriğini Anlama *(Understanding the Evaluation Metric)*

## Monsaraida'nın 4. sıradaki çözüm fikirlerini inceleme *(Examining the 4th place solution’s ideas from Monsaraida)*

## Belirli tarihler ve zaman ufukları (time horizons) için tahminleri hesaplama *(Computing predictions for specific dates and time horizons)*

## Herkese açık (public) ve özel (private) tahminleri bir araya getirme *(Assembling public and private predictions)*

## Özet *(Summary)*

---

# Bölüm 3: Görsel Yarışma: Manyok Yaprağı Hastalığı Yarışması *(Chapter 3: Vision Competition: Cassava Leaf Disease Competition)*

## Veriyi ve metrikleri anlama *(Understanding the data and metrics)*

## Bir temel model (baseline model) oluşturma *(Building a baseline model)*

## En iyi çözümlerden ders alma *(Learning from top solutions)*

### Ön eğitim *(Pretraining)*

### Test zamanı artırımı *(Test time augmentation)*

### Dönüştürücüler *(Transformers)*

### Birleştirme *(Ensembling)*

## Kapsamlı bir çözüm *(A complete solution)*

## Özet *(Summary)*

---

# Bölüm 4: NLP Yarışması – Google Quest Soru-Cevap Etiketleme *(Chapter 4: NLP Competition – Google Quest Q&A Labeling)*

## Temel çözüm *(The baseline solution)*

## En iyi çözümlerden ders alma *(Learning from top solutions)*

## Özet *(Summary)*