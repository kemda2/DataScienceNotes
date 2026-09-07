
## MNIST El Yazısı Rakamları Üzerine İnceleme

```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns
from sklearn import datasets
from sklearn import manifold
%matplotlib inline

data = datasets.fetch_openml(
 'mnist_784',
 version=1,
 return_X_y=True
)
pixel_values, targets = data
targets = targets.astype(int)

single_image = pixel_values[1, :].reshape(28, 28)
plt.imshow(single_image, cmap='gray')

tsne = manifold.TSNE(n_components=2, random_state=42)
transformed_data = tsne.fit_transform(pixel_values[:3000, :])

tsne_df = pd.DataFrame(
np.column_stack((transformed_data, targets[:3000])),
columns=["x", "y", "targets"]
)
tsne_df.loc[:, "targets"] = tsne_df.targets.astype(int)

grid = sns.FacetGrid(tsne_df, hue="targets", size=8)
grid.map(plt.scatter, "x", "y").add_legend()
```

## Çapraz Doğrulama

```python
import pandas as pd
df = pd.read_csv("winequality-red.csv")

# a mapping dictionary that maps the quality values from 0 to 5
quality_mapping = {
 3: 0,
 4: 1,
 5: 2,
 6: 3,
 7: 4,
 8: 5
}
# you can use the map function of pandas with
# any dictionary to convert the values in a given
# column to values in the dictionary
df.loc[:, "quality"] = df.quality.map(quality_mapping)

# use sample with frac=1 to shuffle the dataframe
# we reset the indices since they change after
# shuffling the dataframe
df = df.sample(frac=1).reset_index(drop=True)
# top 1000 rows are selected
# for training
df_train = df.head(1000)
# bottom 599 values are selected
# for testing/validation
df_test = df.tail(599)

# import from scikit-learn
from sklearn import tree
from sklearn import metrics
# initialize decision tree classifier class
# with a max_depth of 3
clf = tree.DecisionTreeClassifier(max_depth=3)
# choose the columns you want to train on
# these are the features for the model
cols = ['fixed acidity',
 'volatile acidity',
 'citric acid',
 'residual sugar',
 'chlorides',
 'free sulfur dioxide',
 'total sulfur dioxide',
 'density',
 'pH',
 'sulphates',
 'alcohol']
# train the model on the provided features
# and mapped quality from before
clf.fit(df_train[cols], df_test.quality)

# generate predictions on the training set
train_predictions = clf.predict(df_train[cols])
# generate predictions on the test set
test_predictions = clf.predict(df_test[cols])
# calculate the accuracy of predictions on
# training data set
train_accuracy = metrics.accuracy_score(
 df_train.quality, train_predictions
)
# calculate the accuracy of predictions on
# test data set
test_accuracy = metrics.accuracy_score(
 df_test.quality, test_predictions
)
```
Sonuçlar;

max_depth = 3
Training accuracy: %58,9
Test accuracy: %54,25


max_depth = 7
Training accuracy: %76,6
Test accuracy: %57,3

Ağaç daha karmaşık hale geldikçe training performansı ciddi şekilde artıyor, fakat test performansı yalnızca biraz artıyor.

Bu nedenle aşırı öğrenmiş olduğu görülmektedir.



Aşağıda tüm derinlik değerlerini bulan kod verilmiştir;

```python
# NOTE: this code is written in a jupyter notebook
# import scikit-learn tree and metrics
from sklearn import tree
from sklearn import metrics
# import matplotlib and seaborn
# for plotting
import matplotlib
import matplotlib.pyplot as plt
import seaborn as sns
# this is our global size of label text
# on the plots
matplotlib.rc('xtick', labelsize=20)
matplotlib.rc('ytick', labelsize=20)
# This line ensures that the plot is displayed
# inside the notebook
%matplotlib inline
# initialize lists to store accuracies
# for training and test data
# we start with 50% accuracy
train_accuracies = [0.5]
test_accuracies = [0.5]

# iterate over a few depth values
for depth in range(1, 25):

    # init the model
    clf = tree.DecisionTreeClassifier(max_depth=depth)

    # columns/features for training
    # note that, this can be done outside
    # the loop
    cols = [
    'fixed acidity',
    'volatile acidity',
    'citric acid',
    'residual sugar',
    'chlorides',
    'free sulfur dioxide',
    'total sulfur dioxide',
    'density',
    'pH',
    'sulphates',
    'alcohol'
    ]

    # fit the model on given features
    clf.fit(df_train[cols], df_train.quality)
    # create training & test predictions
    train_predictions = clf.predict(df_train[cols])
    test_predictions = clf.predict(df_test[cols])
    # calculate training & test accuracies
    train_accuracy = metrics.accuracy_score(
    df_train.quality, train_predictions
    )
    test_accuracy = metrics.accuracy_score(
    df_test.quality, test_predictions
    )

    # append accuracies
    train_accuracies.append(train_accuracy)
    test_accuracies.append(test_accuracy)
# create two plots using matplotlib
# and seaborn
plt.figure(figsize=(10, 5))
sns.set_style("whitegrid")
plt.plot(train_accuracies, label="train accuracy")
plt.plot(test_accuracies, label="test accuracy")
plt.legend(loc="upper left", prop={'size': 15})
plt.xticks(range(0, 26, 5))
plt.xlabel("max_depth", size=20)
plt.ylabel("accuracy", size=20)
plt.show()
```

![](i/001.png)

En popüler ve yaygın olarak kullanılan birkaç çapraz doğrulama tekniği:

k-fold cross-validation (k-katlı çapraz doğrulama)
stratified k-fold cross-validation (tabakalı k-katlı çapraz doğrulama)
hold-out based validation (hold-out tabanlı doğrulama)
leave-one-out cross-validation (birini dışarıda bırakma çapraz doğrulaması)
group k-fold cross-validation (grup k-katlı çapraz doğrulama)

Scikit-learn'deki KFold kullanılarak herhangi bir veri, k eşit parçaya bölünebilir.
k-fold çapraz doğrulama kullanıldığında, her örneğe 0'dan k-1'e kadar bir değer atanır.

```python
# import pandas and model_selection module of scikit-learn
import pandas as pd
from sklearn import model_selection
if __name__ == "__main__":
    # Training data is in a CSV file called train.csv
    df = pd.read_csv("train.csv")

    # we create a new column called kfold and fill it with -1
    df["kfold"] = -1

    # the next step is to randomize the rows of the data
    df = df.sample(frac=1).reset_index(drop=True)

    # initiate the kfold class from model_selection module
    kf = model_selection.KFold(n_splits=5)

    # fill the new kfold column
    for fold, (trn_, val_) in enumerate(kf.split(X=df)):
        df.loc[val_, 'kfold'] = fold
        # save the new csv with kfold column
        df.to_csv("train_folds.csv", index=False)
```

Normal K-Fold, verileri katmanlara rastgele böler; Stratified K-Fold ise her katmanda sınıfların oranını mümkün olduğunca aynı tutar. Özellikle dengesiz veri kümelerinde Stratified K-Fold çok daha güvenlidir.

```python
# import pandas and model_selection module of scikit-learn
import pandas as pd
from sklearn import model_selection
if __name__ == "__main__":
    # Training data is in a csv file called train.csv
    df = pd.read_csv("train.csv")
    # we create a new column called kfold and fill it with -1
    df["kfold"] = -1
    # the next step is to randomize the rows of the data
    df = df.sample(frac=1).reset_index(drop=True)
    # fetch targets
    y = df.target.values
    # initiate the kfold class from model_selection module
    kf = model_selection.StratifiedKFold(n_splits=5)
    # fill the new kfold column
    for f, (t_, v_) in enumerate(kf.split(X=df, y=y)):
    df.loc[v_, 'kfold'] = f
    # save the new csv with kfold column
    df.to_csv("train_folds.csv", index=False)
```

```python
b = sns.countplot(x='quality', data=df)
b.set_xlabel("quality", fontsize=20)
b.set_ylabel("count", fontsize=20)
```

![](i/002.png)

Az örnekli regresyon problemlerinde aşağıdaki tabloya göre kaç bins'e bölünerek hedef binlere ayrılır bakılır ve oluşturulan bins değişkenine göre stratified k-fold yapılır. Direk k-fold da kullanılabilir.

![10000'den az veride kaç bin'e böleceğimizin seçimi](i/003.png)

```python
# stratified-kfold for regression

import numpy as np
import pandas as pd
from sklearn import datasets
from sklearn import model_selection


def create_folds(data):
    # kfold adında yeni bir sütun oluşturuyoruz ve başlangıçta -1 veriyoruz
    data["kfold"] = -1

    # Verinin satırlarını rastgele karıştırıyoruz
    data = data.sample(frac=1).reset_index(drop=True)

    # Sturges kuralına göre bin sayısını hesaplıyoruz
    num_bins = np.floor(1 + np.log2(len(data)))

    # Target değerlerini bin'lere ayırıyoruz
    data.loc[:, "bins"] = pd.cut(
        data["target"],
        bins=num_bins,
        labels=False
    )

    # 5 katlı Stratified K-Fold oluşturuyoruz
    kf = model_selection.StratifiedKFold(n_splits=5)

    # Fold'ları oluşturuyoruz
    # Burada target yerine bins kullanıyoruz
    for f, (t_, v_) in enumerate(
        kf.split(X=data, y=data.bins.values)
    ):
        data.loc[v_, "kfold"] = f

    # Geçici olarak oluşturduğumuz bins sütununu siliyoruz
    data = data.drop("bins", axis=1)

    # Fold bilgileri eklenmiş DataFrame'i döndürüyoruz
    return data


if __name__ == "__main__":
    # 15000 örnek, 100 feature ve 1 target içeren
    # yapay bir regresyon veri seti oluşturuyoruz
    X, y = datasets.make_regression(
        n_samples=15000,
        n_features=100,
        n_targets=1
    )

    # NumPy dizisini DataFrame'e dönüştürüyoruz
    df = pd.DataFrame(
        X,
        columns=[f"f_{i}" for i in range(X.shape[1])]
    )

    # Target sütununu ekliyoruz
    df.loc[:, "target"] = y

    # Stratified K-Fold'ları oluşturuyoruz
    df = create_folds(df)

    # Sonucu görmek için ilk 10 satırı yazdırıyoruz
    print(df.head(10))

```

## Değerlendirme Metrikleri

Sınıflandırma problemlerinde en yaygın kullanılan metrikler şunlardır:

* Accuracy (Doğruluk)
* Precision (Kesinlik) — P
* Recall (Duyarlılık) — R
* F1 Score — F1
* ROC (Receiver Operating Characteristic) eğrisinin altında kalan alan — AUC
* Log Loss
* Precision at k — P@k
* Average Precision at k — AP@k
* Mean Average Precision at k — MAP@k
* Regresyon problemlerinde kullanılan metrikler

Regresyon problemlerinde en yaygın kullanılan değerlendirme metrikleri ise şunlardır:

* Mean Absolute Error (MAE) — Ortalama Mutlak Hata
* Mean Squared Error (MSE) — Ortalama Kare Hata
* Root Mean Squared Error (RMSE) — Kök Ortalama Kare Hata
* Root Mean Squared Logarithmic Error (RMSLE) — Kök Ortalama Kare Logaritmik Hata
* Mean Percentage Error (MPE) — Ortalama Yüzde Hata
* Mean Absolute Percentage Error (MAPE) — Ortalama Mutlak * Yüzde Hata
* R² — R-kare

```python
def accuracy(y_true, y_pred):
    """
    Function to calculate accuracy
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: accuracy score
    """
    # initialize a simple counter for correct predictions
    correct_counter = 0
    # loop over all elements of y_true
    # and y_pred "together"
    for yt, yp in zip(y_true, y_pred):
        if yt == yp:
        # if prediction is equal to truth, increase the counter
        correct_counter += 1
        # return accuracy
        # which is correct predictions over the number of samples
    return correct_counter / len(y_true)

from sklearn import metrics
l1 = [0,1,1,1,0,0,0,1]
l2 = [0,1,0,1,0,1,0,0]
metrics.accuracy_score(l1, l2)
# 0.625
accuracy(l1, l2)
# 0.625
```

```python
def true_positive(y_true, y_pred):
    """
    True Positive sayısını hesaplar.
    y_true: Gerçek değerler
    y_pred: Modelin tahminleri
    """
    tp = 0

    for yt, yp in zip(y_true, y_pred):
        if yt == 1 and yp == 1:
            tp += 1

    return tp


def true_negative(y_true, y_pred):
    """
    True Negative sayısını hesaplar.
    """
    tn = 0

    for yt, yp in zip(y_true, y_pred):
        if yt == 0 and yp == 0:
            tn += 1

    return tn


def false_positive(y_true, y_pred):
    """
    False Positive sayısını hesaplar.
    """
    fp = 0

    for yt, yp in zip(y_true, y_pred):
        if yt == 0 and yp == 1:
            fp += 1

    return fp


def false_negative(y_true, y_pred):
    """
    False Negative sayısını hesaplar.
    """
    fn = 0

    for yt, yp in zip(y_true, y_pred):
        if yt == 1 and yp == 0:
            fn += 1

    return fn

l1 = [0,1,1,1,0,0,0,1]
l2 = [0,1,0,1,0,1,0,0]

true_positive(l1, l2)
# 2
false_positive(l1, l2)
# 1
false_negative(l1, l2)
# 2
true_negative(l1, l2)
# 3

def accuracy_v2(y_true, y_pred):
    """
    TP/TN/FP/FN kullanarak accuracy hesaplayan fonksiyon.
    
    y_true: Gerçek değerler
    y_pred: Modelin tahminleri
    """
    
    tp = true_positive(y_true, y_pred)
    fp = false_positive(y_true, y_pred)
    fn = false_negative(y_true, y_pred)
    tn = true_negative(y_true, y_pred)

    accuracy_score = (tp + tn) / (tp + tn + fp + fn)

    return accuracy_score

l1 = [0, 1, 1, 1, 0, 0, 0, 1]
l2 = [0, 1, 0, 1, 0, 1, 0, 0]

accuracy(l1, l2)
# 0.625

accuracy_v2(l1, l2)
# 0.625

metrics.accuracy_score(l1, l2)
# 0.625
```

```python
def precision(y_true, y_pred):
    """
    Precision hesaplayan fonksiyon.
    y_true: Gerçek değerler
    y_pred: Modelin tahminleri
    return: precision skoru
    """

    tp = true_positive(y_true, y_pred)
    fp = false_positive(y_true, y_pred)

    precision = tp / (tp + fp)

    return precision

l1 = [0, 1, 1, 1, 0, 0, 0, 1]  # gerçek değerler
l2 = [0, 1, 0, 1, 0, 1, 0, 0]  # tahminler

precision(l1, l2)
# 0.6666666666666666

def recall(y_true, y_pred):
    """
    Recall hesaplayan fonksiyon.
    y_true: Gerçek değerler
    y_pred: Modelin tahminleri
    return: recall skoru
    """

    tp = true_positive(y_true, y_pred)
    fn = false_negative(y_true, y_pred)

    recall = tp / (tp + fn)

    return recall

l1 = [0, 1, 1, 1, 0, 0, 0, 1]
l2 = [0, 1, 0, 1, 0, 1, 0, 0]

recall(l1, l2)
# 0.5



import matplotlib.pyplot as plt

# Gerçek değerler
y_true = [
    0, 0, 0, 1, 0, 0, 0, 0, 0, 0,
    1, 0, 0, 0, 0, 0, 0, 0, 1, 0
]

# Modelin tahmin ettiği olasılıklar
y_pred = [
    0.02638412, 0.11114267, 0.31620708,
    0.0490937, 0.0191491, 0.17554844,
    0.15952202, 0.03819563, 0.11639273,
    0.079377, 0.08584789, 0.39095342,
    0.27259048, 0.03447096, 0.04644807,
    0.03543574, 0.18521942, 0.05934905,
    0.61977213, 0.33056815
]

# Listeler
precisions = []
recalls = []

# Threshold değerleri
thresholds = [
    0.0490937,
    0.05934905,
    0.079377,
    0.08584789,
    0.11114267,
    0.11639273,
    0.15952202,
    0.17554844,
    0.18521942,
    0.27259048,
    0.31620708,
    0.33056815,
    0.39095342,
    0.61977213
]

# Her threshold için precision ve recall hesapla
for i in thresholds:

    # Olasılıkları 0 veya 1'e dönüştür
    temp_prediction = [
        1 if x >= i else 0
        for x in y_pred
    ]

    # Precision hesapla
    p = precision(y_true, temp_prediction)

    # Recall hesapla
    r = recall(y_true, temp_prediction)

    # Sonuçları listelere ekle
    precisions.append(p)
    recalls.append(r)


# Precision-Recall grafiği
plt.figure(figsize=(7, 7))
plt.plot(recalls, precisions, marker="o")
plt.xlabel("Recall", fontsize=15)
plt.ylabel("Precision", fontsize=15)
plt.title("Precision-Recall Curve")
plt.grid()
plt.show()
```

![](i/004.png)

```python
def f1(y_true, y_pred):
    """
    Function to calculate f1 score
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: f1 score
    """
    p = precision(y_true, y_pred)
    r = recall(y_true, y_pred)
    score = 2 * p * r / (p + r)
    return score
```

```python
y_true = [0, 0, 0, 1, 0, 0, 0, 0, 0, 0,
          1, 0, 0, 0, 0, 0, 0, 0, 1, 0]

y_pred = [0, 0, 1, 0, 0, 0, 1, 0, 0, 0,
          1, 0, 0, 0, 0, 0, 0, 0, 1, 0]

f1(y_true, y_pred)
0.5714285714285715
```

```python
from sklearn import metrics

metrics.f1_score(y_true, y_pred)

0.5714285714285715
```

```python
def tpr(y_true, y_pred):
    """
    Function to calculate tpr
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: tpr/recall
    """
    return recall(y_true, y_pred)
```

```python
def fpr(y_true, y_pred):
    """
    Function to calculate fpr
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: fpr
    """
    fp = false_positive(y_true, y_pred)
    tn = true_negative(y_true, y_pred)
    return fp / (tn + fp)
```

```python
# TPR ve FPR değerlerini saklamak için boş listeler
tpr_list = []
fpr_list = []

# Gerçek hedefler
y_true = [0, 0, 0, 0, 1, 0, 1,
          0, 0, 1, 0, 1, 0, 0, 1]

# Bir örneğin 1 olma olasılığına dair tahminler
y_pred = [0.1, 0.3, 0.2, 0.6, 0.8, 0.05,
          0.9, 0.5, 0.3, 0.66, 0.3, 0.2,
          0.85, 0.15, 0.99]

# Elle belirlenmiş threshold (eşik) değerleri
thresholds = [0, 0.1, 0.2, 0.3, 0.4, 0.5,
              0.6, 0.7, 0.8, 0.85, 0.9, 0.99, 1.0]

# Tüm threshold değerleri üzerinde döngü
for thresh in thresholds:

    # Belirli bir threshold için tahminleri hesapla
    temp_pred = [1 if x >= thresh else 0 for x in y_pred]

    # TPR hesapla
    temp_tpr = tpr(y_true, temp_pred)

    # FPR hesapla
    temp_fpr = fpr(y_true, temp_pred)

    # TPR ve FPR değerlerini listelere ekle
    tpr_list.append(temp_tpr)
    fpr_list.append(temp_fpr)
```

![alt text](i/005.png)

```python
plt.figure(figsize=(7, 7))

plt.fill_between(fpr_list, tpr_list, alpha=0.4)
plt.plot(fpr_list, tpr_list, lw=3)

plt.xlim(0, 1.0)
plt.ylim(0, 1.0)

plt.xlabel('FPR', fontsize=15)
plt.ylabel('TPR', fontsize=15)

plt.show()
```

![](i/006.png)

```python
from sklearn import metrics

y_true = [0, 0, 0, 0, 1, 0, 1,
          0, 0, 1, 0, 1, 0, 0, 1]

y_pred = [0.1, 0.3, 0.2, 0.6, 0.8, 0.05,
          0.9, 0.5, 0.3, 0.66, 0.3, 0.2,
          0.85, 0.15, 0.99]

metrics.roc_auc_score(y_true, y_pred)
0.8300000000000001
```

```python
# True Positive ve False Positive değerlerini saklamak için boş listeler
tp_list = []
fp_list = []

# Gerçek hedefler
y_true = [0, 0, 0, 0, 1, 0, 1,
          0, 0, 1, 0, 1, 0, 0, 1]

# Bir örneğin 1 olma olasılığına dair tahminler
y_pred = [0.1, 0.3, 0.2, 0.6, 0.8, 0.05,
          0.9, 0.5, 0.3, 0.66, 0.3, 0.2,
          0.85, 0.15, 0.99]

# Elle belirlenmiş threshold (eşik) değerleri
thresholds = [0, 0.1, 0.2, 0.3, 0.4, 0.5,
              0.6, 0.7, 0.8, 0.85, 0.9, 0.99, 1.0]

# Tüm threshold değerleri üzerinde döngü
for thresh in thresholds:

    # Belirli bir threshold için tahminleri hesapla
    temp_pred = [1 if x >= thresh else 0 for x in y_pred]

    # True Positive hesapla
    temp_tp = true_positive(y_true, temp_pred)

    # False Positive hesapla
    temp_fp = false_positive(y_true, temp_pred)

    # TP ve FP değerlerini listelere ekle
    tp_list.append(temp_tp)
    fp_list.append(temp_fp)
```

![alt text](i/007.png)

![alt text](i/008.png)

```python
import numpy as np

def log_loss(y_true, y_proba):
    """
    Function to calculate log loss
    :param y_true: list of true values
    :param y_proba: list of probabilities for 1
    :return: overall log loss
    """

    # epsilon değerini tanımla
    epsilon = 1e-15

    # Bireysel loss değerlerini saklamak için boş liste
    loss = []

    # Gerçek değerler ve tahmin olasılıkları üzerinde dolaş
    for yt, yp in zip(y_true, y_proba):

        # Olasılığı sınırla
        yp = np.clip(yp, epsilon, 1 - epsilon)

        # Tek bir örnek için loss hesapla
        temp_loss = -1.0 * (
            yt * np.log(yp)
            + (1 - yt) * np.log(1 - yp)
        )

        # Loss değerini listeye ekle
        loss.append(temp_loss)

    # Tüm örneklerin ortalama loss değerini döndür
    return np.mean(loss)
```

```python
y_true = [0, 0, 0, 0, 1, 0, 1,
          0, 0, 1, 0, 1, 0, 0, 1]

y_proba = [0.1, 0.3, 0.2, 0.6, 0.8, 0.05,
           0.9, 0.5, 0.3, 0.66, 0.3, 0.2,
           0.85, 0.15, 0.99]

log_loss(y_true, y_proba)
0.49882711861432294

from sklearn import metrics
metrics.log_loss(y_true, y_proba)
0.49882711861432294

```

```python
import numpy as np

def macro_precision(y_true, y_pred):
    """
    Function to calculate macro averaged precision
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: macro precision score
    """

    # unique değerlerin uzunluğunu alarak
    # sınıf sayısını bul
    num_classes = len(np.unique(y_true))

    # precision değerini 0 olarak başlat
    precision = 0

    # tüm sınıflar üzerinde döngü
    for class_ in range(num_classes):

        # Mevcut sınıf dışındaki tüm sınıflar negatif kabul edilir
        temp_true = [1 if p == class_ else 0 for p in y_true]
        temp_pred = [1 if p == class_ else 0 for p in y_pred]

        # Mevcut sınıf için True Positive hesapla
        tp = true_positive(temp_true, temp_pred)

        # Mevcut sınıf için False Positive hesapla
        fp = false_positive(temp_true, temp_pred)

        # Mevcut sınıf için precision hesapla
        temp_precision = tp / (tp + fp)

        # Tüm sınıfların precision değerlerini topla
        precision += temp_precision

    # Sınıfların ortalama precision değerini hesapla ve döndür
    precision /= num_classes

    return precision
```

```python
import numpy as np

def micro_precision(y_true, y_pred):
    """
    Function to calculate micro averaged precision
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: micro precision score
    """

    # Unique değerlerin uzunluğunu alarak
    # sınıf sayısını bul
    num_classes = len(np.unique(y_true))

    # TP ve FP değerlerini 0 olarak başlat
    tp = 0
    fp = 0

    # Tüm sınıflar üzerinde döngü
    for class_ in range(num_classes):

        # Mevcut sınıf dışındaki tüm sınıflar negatif kabul edilir
        temp_true = [1 if p == class_ else 0 for p in y_true]
        temp_pred = [1 if p == class_ else 0 for p in y_pred]

        # Mevcut sınıf için True Positive hesapla
        # ve toplam TP'ye ekle
        tp += true_positive(temp_true, temp_pred)

        # Mevcut sınıf için False Positive hesapla
        # ve toplam FP'ye ekle
        fp += false_positive(temp_true, temp_pred)

    # Genel precision değerini hesapla ve döndür
    precision = tp / (tp + fp)

    return precision
```

```python
from collections import Counter
import numpy as np

def weighted_precision(y_true, y_pred):
    """
    Function to calculate weighted averaged precision
    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: weighted precision score
    """

    # Unique değerlerin uzunluğunu alarak
    # sınıf sayısını bul
    num_classes = len(np.unique(y_true))

    # Sınıf: örnek sayısı sözlüğü oluştur
    # Örneğin: {0: 20, 1: 15, 2: 21}
    class_counts = Counter(y_true)

    # Precision değerini 0 olarak başlat
    precision = 0

    # Tüm sınıflar üzerinde döngü
    for class_ in range(num_classes):

        # Mevcut sınıf dışındaki tüm sınıflar negatif kabul edilir
        temp_true = [1 if p == class_ else 0 for p in y_true]
        temp_pred = [1 if p == class_ else 0 for p in y_pred]

        # Sınıf için TP ve FP hesapla
        tp = true_positive(temp_true, temp_pred)
        fp = false_positive(temp_true, temp_pred)

        # Sınıfın precision değerini hesapla
        temp_precision = tp / (tp + fp)

        # Precision değerini sınıftaki örnek sayısıyla çarp
        weighted_precision = class_counts[class_] * temp_precision

        # Genel precision değerine ekle
        precision += weighted_precision

    # Toplam örnek sayısına bölerek
    # genel weighted precision değerini hesapla
    overall_precision = precision / len(y_true)

    return overall_precision
```

```python
from sklearn import metrics

y_true = [0, 1, 2, 0, 1, 2, 0, 2, 2]
y_pred = [0, 2, 1, 0, 2, 1, 0, 0, 2]

# Macro Precision
print("Macro Precision:")
print("Bizim:", macro_precision(y_true, y_pred))
print("sklearn:", metrics.precision_score(y_true, y_pred, average="macro"))

# Micro Precision
print("\nMicro Precision:")
print("Bizim:", micro_precision(y_true, y_pred))
print("sklearn:", metrics.precision_score(y_true, y_pred, average="micro"))

# Weighted Precision
print("\nWeighted Precision:")
print("Bizim:", weighted_precision(y_true, y_pred))
print("sklearn:", metrics.precision_score(y_true, y_pred, average="weighted"))
```



```python
from collections import Counter
import numpy as np


def weighted_f1(y_true, y_pred):
    """
    Function to calculate weighted F1 score

    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: weighted F1 score
    """

    # Find the number of classes by taking
    # length of unique values in true list
    num_classes = len(np.unique(y_true))

    # Create class:sample count dictionary
    # Example: {0: 20, 1: 15, 2: 21}
    class_counts = Counter(y_true)

    # Initialize F1 to 0
    f1 = 0

    # Loop over all classes
    for class_ in range(num_classes):

        # All classes except current are considered negative
        temp_true = [1 if p == class_ else 0 for p in y_true]
        temp_pred = [1 if p == class_ else 0 for p in y_pred]

        # Calculate precision and recall for class
        p = precision(temp_true, temp_pred)
        r = recall(temp_true, temp_pred)

        # Calculate F1 of class
        if p + r != 0:
            temp_f1 = 2 * p * r / (p + r)
        else:
            temp_f1 = 0

        # Multiply F1 with count of samples in class
        weighted_f1 = class_counts[class_] * temp_f1

        # Add to F1
        f1 += weighted_f1

    # Calculate overall F1 by dividing by
    # total number of samples
    overall_f1 = f1 / len(y_true)

    return overall_f1
```

```python
from sklearn import metrics

y_true = [0, 1, 2, 0, 1, 2, 0, 2, 2]
y_pred = [0, 2, 1, 0, 2, 1, 0, 0, 2]

print(weighted_f1(y_true, y_pred))

0.41269841269841273

print(metrics.f1_score(
    y_true,
    y_pred,
    average="weighted"
))

0.41269841269841273
```

```python
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn import metrics

# Gerçek sınıflar
y_true = [0, 1, 2, 0, 1, 2, 0, 2, 2]

# Tahminler
y_pred = [0, 2, 1, 0, 2, 1, 0, 0, 2]

# Confusion matrix
cm = metrics.confusion_matrix(y_true, y_pred)

# Grafik
plt.figure(figsize=(10, 10))

cmap = sns.cubehelix_palette(
    50,
    hue=0.05,
    rot=0,
    light=0.9,
    dark=0,
    as_cmap=True
)

sns.set(font_scale=2.5)

sns.heatmap(
    cm,
    annot=True,
    cmap=cmap,
    cbar=False
)

plt.ylabel("Actual Labels", fontsize=20)
plt.xlabel("Predicted Labels", fontsize=20)

plt.show()
```

![](i/009.png)

```python
def pk(y_true, y_pred, k):
    """
    This function calculates precision at k
    for a single sample

    :param y_true: list of values, actual classes
    :param y_pred: list of values, predicted classes
    :return: precision at a given value k
    """

    # If k is 0, return 0.
    # We should never have this as k is always >= 1
    if k == 0:
        return 0

    # We are interested only in top-k predictions
    y_pred = y_pred[:k]

    # Convert predictions to set
    pred_set = set(y_pred)

    # Convert actual values to set
    true_set = set(y_true)

    # Find common values
    common_values = pred_set.intersection(true_set)

    # Return length of common values over k
    return len(common_values) / len(y_pred[:k])
```

```python
def apk(y_true, y_pred, k):
    """
    This function calculates average precision at k
    for a single sample

    :param y_true: list of values, actual classes
    :param y_pred: list of values, predicted classes
    :return: average precision at a given value k
    """

    # Initialize P@k list of values
    pk_values = []

    # Loop over all k, from 1 to k + 1
    for i in range(1, k + 1):

        # Calculate P@i and append to list
        pk_values.append(pk(y_true, y_pred, i))

    # If we have no values in the list, return 0
    if len(pk_values) == 0:
        return 0

    # Return the sum of the list divided by its length
    return sum(pk_values) / len(pk_values)
```

```python
def mapk(y_true, y_pred, k):
    """
    This function calculates mean average precision at k
    for multiple samples.

    :param y_true: list of values, actual classes
    :param y_pred: list of values, predicted classes
    :param k: number of predictions to consider
    :return: mean average precision at k
    """

    # initialize empty list for apk values
    apk_values = []

    # loop over all samples
    for i in range(len(y_true)):

        # store AP@k value for every sample
        apk_values.append(
            apk(y_true[i], y_pred[i], k=k)
        )

    # return mean of AP@k values
    return sum(apk_values) / len(apk_values)
```

### Regresyon

```python
import numpy as np
def mean_absolute_error(y_true, y_pred):
 """
 This function calculates mae
 :param y_true: list of real numbers, true values
 :param y_pred: list of real numbers, predicted values
 :return: mean absolute error
 """
 # initialize error at 0
 error = 0
 # loop over all samples in the true and predicted list
 for yt, yp in zip(y_true, y_pred):
 # calculate absolute error
 # and add to error
 error += np.abs(yt - yp)
 # return mean error
 return error / len(y_true)
```

```python
def mean_squared_error(y_true, y_pred):
    """
    Bu fonksiyon MSE hesaplar.

    :param y_true: gerçek değerlerin listesi
    :param y_pred: tahmin edilen değerlerin listesi
    :return: ortalama karesel hata
    """

    # Hatayı 0 olarak başlat
    error = 0

    # Gerçek ve tahmin edilen değerler üzerinde dolaş
    for yt, yp in zip(y_true, y_pred):

        # Karesel hatayı hesapla
        # ve toplam hataya ekle
        error += (yt - yp) ** 2

    # Ortalama hatayı döndür
    return error / len(y_true)
```

```python
import numpy as np

def mean_squared_log_error(y_true, y_pred):
    """
    Bu fonksiyon MSLE hesaplar.

    :param y_true: gerçek değerlerin listesi
    :param y_pred: tahmin edilen değerlerin listesi
    :return: ortalama karesel logaritmik hata
    """

    # Hatayı 0 olarak başlat
    error = 0

    # Gerçek ve tahmin edilen değerler üzerinde dolaş
    for yt, yp in zip(y_true, y_pred):

        # Karesel logaritmik hatayı hesapla
        # ve toplam hataya ekle
        error += (np.log(1 + yt) - np.log(1 + yp)) ** 2

    # Ortalama hatayı döndür
    return error / len(y_true)
```

```python
def mean_percentage_error(y_true, y_pred):
    """
    This function calculates MPE.

    :param y_true: list of real numbers, true values
    :param y_pred: list of real numbers, predicted values
    :return: mean percentage error
    """

    # initialize error at 0
    error = 0

    # loop over all samples in true and predicted list
    for yt, yp in zip(y_true, y_pred):

        # calculate percentage error
        # and add to error
        error += (yt - yp) / yt

    # return mean percentage error
    return error / len(y_true)
```

```python
import numpy as np

def mean_abs_percentage_error(y_true, y_pred):
    """
    This function calculates MAPE.

    :param y_true: list of real numbers, true values
    :param y_pred: list of real numbers, predicted values
    :return: mean absolute percentage error
    """

    # initialize error at 0
    error = 0

    # loop over all samples in true and predicted list
    for yt, yp in zip(y_true, y_pred):

        # calculate absolute percentage error
        # and add to error
        error += np.abs(yt - yp) / yt

    # return mean absolute percentage error
    return error / len(y_true)
```

```python
import numpy as np

def r2(y_true, y_pred):
    """
    This function calculates R-squared score.

    :param y_true: list of real numbers, true values
    :param y_pred: list of real numbers, predicted values
    :return: R2 score
    """

    # calculate the mean value of true values
    mean_true_value = np.mean(y_true)

    # initialize numerator with 0
    numerator = 0

    # initialize denominator with 0
    denominator = 0

    # loop over all true and predicted values
    for yt, yp in zip(y_true, y_pred):

        # update numerator
        numerator += (yt - yp) ** 2

        # update denominator
        denominator += (yt - mean_true_value) ** 2

    # calculate the ratio
    ratio = numerator / denominator

    # return 1 - ratio
    return 1 - ratio
```

```python
import numpy as np

def mae_np(y_true, y_pred):
    return np.mean(np.abs(y_true - y_pred))
```

```python
from sklearn import metrics

y_true = [1, 2, 3, 1, 2, 3, 1, 2, 3]

y_pred = [2, 1, 3, 1, 2, 3, 3, 1, 2]

metrics.cohen_kappa_score(
    y_true,
    y_pred,
    weights="quadratic"
)

0.33333333333333337

metrics.accuracy_score(y_true, y_pred)

0.4444444444444444
```

```python
def mcc(y_true, y_pred):
    """
    This function calculates Matthew's Correlation Coefficient
    for binary classification.

    :param y_true: list of true values
    :param y_pred: list of predicted values
    :return: MCC score
    """

    tp = true_positive(y_true, y_pred)
    tn = true_negative(y_true, y_pred)
    fp = false_positive(y_true, y_pred)
    fn = false_negative(y_true, y_pred)

    numerator = (tp * tn) - (fp * fn)

    denominator = (
        (tp + fp) *
        (fn + tn) *
        (fp + tn) *
        (tp + fn)
    )

    denominator = denominator ** 0.5

    return numerator / denominator
```

## Makine öğrenmesi projelerini organize etmek

# src/train.py

import joblib
import pandas as pd
from sklearn import metrics
from sklearn import tree


```python
# src/train.py

import joblib
import pandas as pd
from sklearn import metrics
from sklearn import tree


def run(fold):
    # read the training data with folds
    df = pd.read_csv("../input/mnist_train_folds.csv")

    # training data is where kfold is not equal to provided fold
    # also, note that we reset the index
    df_train = df[df.kfold != fold].reset_index(drop=True)

    # validation data is where kfold is equal to provided fold
    df_valid = df[df.kfold == fold].reset_index(drop=True)

    # drop the label column from dataframe and convert it to
    # a numpy array by using .values.
    # target is label column in the dataframe
    x_train = df_train.drop("label", axis=1).values
    y_train = df_train.label.values

    # similarly, for validation, we have
    x_valid = df_valid.drop("label", axis=1).values
    y_valid = df_valid.label.values

    # initialize simple decision tree classifier from sklearn
    clf = tree.DecisionTreeClassifier()

    # fit the model on training data
    clf.fit(x_train, y_train)

    # create predictions for validation samples
    preds = clf.predict(x_valid)

    # calculate & print accuracy
    accuracy = metrics.accuracy_score(y_valid, preds)
    print(f"Fold={fold}, Accuracy={accuracy}")

    # save the model
    joblib.dump(clf, f"../models/dt_{fold}.bin")


if __name__ == "__main__":
    run(fold=0)
    run(fold=1)
    run(fold=2)
    run(fold=3)
    run(fold=4)

❯ python train.py
Fold=0, Accuracy=0.8680833333333333
Fold=1, Accuracy=0.8685
Fold=2, Accuracy=0.8674166666666666
Fold=3, Accuracy=0.8703333333333333
Fold=4, Accuracy=0.8699166666666667
```

Elle yazılan kısımları koddan ayırmak için;

```python
# config.py
TRAINING_FILE = "../input/mnist_train_folds.csv"
MODEL_OUTPUT = "../models/"
```
Düzeltilmiş train.py;
```python
# train.py

import os
import config
import joblib
import pandas as pd
from sklearn import metrics
from sklearn import tree


def run(fold):
    # read the training data with folds
    df = pd.read_csv(config.TRAINING_FILE)

    # training data is where kfold is not equal to provided fold
    # also, note that we reset the index
    df_train = df[df.kfold != fold].reset_index(drop=True)

    # validation data is where kfold is equal to provided fold
    df_valid = df[df.kfold == fold].reset_index(drop=True)

    # drop the label column from dataframe and convert it to
    # a numpy array by using .values.
    # target is label column in the dataframe
    x_train = df_train.drop("label", axis=1).values
    y_train = df_train.label.values

    # similarly, for validation, we have
    x_valid = df_valid.drop("label", axis=1).values
    y_valid = df_valid.label.values

    # initialize simple decision tree classifier from sklearn
    clf = tree.DecisionTreeClassifier()

    # fit the model on training data
    clf.fit(x_train, y_train)

    # create predictions for validation samples
    preds = clf.predict(x_valid)

    # calculate & print accuracy
    accuracy = metrics.accuracy_score(y_valid, preds)

    print(f"Fold={fold}, Accuracy={accuracy}")

    # save the model
    joblib.dump(
        clf,
        os.path.join(config.MODEL_OUTPUT, f"dt_{fold}.bin")
    )


if __name__ == "__main__":
    run(fold=0)
    run(fold=1)
    run(fold=2)
    run(fold=3)
    run(fold=4)
```


76