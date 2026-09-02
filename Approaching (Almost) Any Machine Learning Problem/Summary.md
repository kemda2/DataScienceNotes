
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