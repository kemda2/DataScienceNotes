## 🐍 Python for Data Science

### 1. Data Structures: Lists, Tuples, Dicts, Sets

* **Lists** (`[]`): sıralıdır, elemanları değiştirilebilir. Örneğin:

  ```python
  fruits = ['apple', 'banana', 'cherry']
  fruits.append('date')  # meyve listesine 'date' eklendi
  ```

  Açıklama: `append` metodu liste sonuna yeni değer ekler.

* **Tuples** (`()`): sıralı ama değiştirilemez. Sabit veriler için idealdir:

  ```python
  point = (3.5, -4.2)
  # point[0] = 10  -> hata verir çünkü tuple immutable.
  ```

* **Dictionaries**: Anahtar-değer çiftleri. Bilgiyi tanımlı alanlar halinde tutmak için kullanılır:

  ```python
  user = {'name': 'Alice', 'age': 30}
  email = user.get('email', 'not provided')  # None yerine varsayılan değer
  ```

* **Sets**: Benzersiz öğeler içerir, membership testleri hızlıdır:

  ```python
  s = {1, 2, 2, 3}  # sonuç {1,2,3}
  ```

---

### 2. Control Flow, Enumerate & Zip

* **If / Elif / Else** ile koşullu akış kontrolü:

  ```python
  x = 5
  if x > 0:
      print('Positive')
  elif x < 0:
      print('Negative')
  else:
      print('Zero')
  ```

* **For loop & enumerate**: indeks ve değeri aynı anda almak için:

  ```python
  animals = ['cat', 'dog', 'rabbit']
  for i, animal in enumerate(animals):
      print(i, animal)  # "0 cat", "1 dog"...
  ```

* **Zip**: paralel listeleri aynı anda dolaş:

  ```python
  names = ['Alice', 'Bob']
  ages = [30, 25]
  for name, age in zip(names, ages):
      print(f'{name} is {age} years old.')
  ```

---

### 3. Comprehensions (List, Dict, Set)

* **List comprehension** örneği:

  ```python
  squares = [x**2 for x in range(10)]
  evens = [x for x in squares if x % 2 == 0]
  ```

  Açıklama: `for` ve `if` ifadelerini tek satırda birleştirerek kısa ve verimli kod yazılmış oldu.

* **Dict comprehension**:

  ```python
  mapping = {x: x**2 for x in range(5)}
  ```

* **Set comprehension**:

  ```python
  unique_evens = {x for x in range(10) if x % 2 == 0}
  ```

---

### 4. Functions & Lambdas

* **Fonksiyon tanımı**:

  ```python
  def greet(name, greeting="Hello"):
      """Returns a greeting string."""
      return f"{greeting}, {name}!"
  ```

  Açıklama: `greet("Alice")` çağrısı "Hello, Alice!" döndürür. Default parametre kullanımını gösterir.

* **Lambda fonksiyonu**:

  ```python
  multiply = lambda a, b: a * b
  print(multiply(2, 3))  # 6
  ```

* **Args/kwargs kullanımı**:

  ```python
  def func(*args, **kwargs):
      print("args:", args)
      print("kwargs:", kwargs)
  func(1, 2, key='value')
  ```

* **Type hints**:

  ```python
  def add(a: int, b: int) -> int:
      return a + b
  ```

---

### 5. File I/O & Context Managers

* **Metin dosyası okuma**:

  ```python
  with open('file.txt', 'r', encoding='utf-8') as f:
      text = f.read()
  ```

  Açıklama: `with` bloğu dosyayı güvenli şekilde açıp işlem sonrasında otomatik olarak kapatır.

* **JSON dosyası**:

  ```python
  import json
  with open('data.json') as f:
      data = json.load(f)
  ```

* **CSV okuma (pandas ile)**:

  ```python
  import pandas as pd
  df = pd.read_csv('data.csv')
  ```

---

### 6. Exception Handling & Debugging

* **Hata yakalama örneği**:

  ```python
  try:
      val = float(user_input)
  except ValueError as e:
      print('Invalid input:', e)
  else:
      print('Value is valid:', val)
  finally:
      print('End of try/except block')
  ```

* **Assert kullanımı**:

  ```python
  assert len(data) > 0, "Data list cannot be empty"
  ```

* **Basit debug**:

  * `print()` ile geçici veri kontrolü
  * `import pdb; pdb.set_trace()` ile adım adım izleme

---

### 7. Environment & Dependency Management

* **Virtual environment oluşturma**:

  ```bash
  python -m venv env
  source env/bin/activate  # Windows: env\Scripts\activate
  ```

* **requirements.txt örneği**:

  ```
  numpy==1.24.0
  pandas>=1.5,<2.0
  scikit-learn
  ```

---

## 🔢 NumPy – Fast Numeric Computing

### 1. Dizi Oluşturma

```python
import numpy as np
a = np.array([1, 2, 3])
zeros = np.zeros((2, 3))
ones = np.ones((3, 3))
seq = np.arange(0, 10, 2)
lin = np.linspace(0, 1, 5)
```

### 2. Vectorization & Broadcasting

```python
a = np.arange(1, 6)
b = np.linspace(1, 5, 5)
print(a * b)  # element-wise çarpma
```

### 3. Index & Boolean Mask

```python
arr = np.arange(10)
mask = arr % 3 == 0
filtered = arr[mask]  # 0,3,6,9
```

### 4. Çok Boyutlu Operasyonlar

```python
m = np.arange(9).reshape((3, 3))
row_mean = m.mean(axis=1)
```

### 5. Lineer Cebir

```python
A = np.array([[1, 2], [3, 4]])
invA = np.linalg.inv(A)
product = A @ invA  # yaklaşık birim matrisi verir
```

### 6. Rastgele Sayılar

```python
np.random.seed(42)
norm = np.random.normal(loc=0, scale=1, size=1000)
```

---

## 🧮 Pandas – Data Manipulation Power

### 1. Veri Yükleme & İnceleme

```python
import pandas as pd
df = pd.read_csv('sales.csv')
print(df.head())
print(df.info())
print(df.describe())
```

### 2. Temizlik & Dönüşüm

* Örnek: Para sütununu float’a çevirme

  ```python
  df['price'] = df['price'].str.replace('$', '').astype(float)
  ```

* Eksik veriden kurtulma:

  ```python
  df.dropna(subset=['price'], inplace=True)
  ```

### 3. Yeni Değişkenler

* İndirimli fiyat sütunu:

  ```python
  df['discounted_price'] = df['price'] * (1 - df['discount_rate'])
  ```

* Yaş gruplarına ayırma:

  ```python
  df['age_group'] = pd.cut(df['age'], bins=[0,18,65,120],
                            labels=['child','adult','senior'])
  ```

### 4. Gruplama & Özet

```python
summary = df.groupby('category').agg(
    total_sales=('sales', 'sum'),
    avg_price=('price', 'mean')
)
```

### 5. Birleştirme

```python
merged = df.merge(customers_df, on='customer_id', how='left')
```

### 6. Pivot & Çoklu İndeks

```python
pivot = df.pivot_table(values='sales', index='region', columns='product', aggfunc='sum')
```

### 7. Zaman Serisi

```python
df['date'] = pd.to_datetime(df['date'])
monthly = df.set_index('date').resample('M').sum()
rolling_avg = monthly['sales'].rolling(window=3).mean()
```

---

## 📊 Data Visualization

### 1. Matplotlib ile Temel Grafikler

```python
import matplotlib.pyplot as plt
plt.figure(figsize=(8, 5))
plt.plot(df['date'], df['sales'], marker='o', linestyle='-')
plt.title('Monthly Sales over Time')
plt.xlabel('Date')
plt.ylabel('Sales')
plt.grid(True)
plt.tight_layout()
plt.show()
```

### 2. Seaborn – İstatistiksel Görselleştirmeler

```python
import seaborn as sns
sns.histplot(df['sales'], kde=True)
sns.boxplot(x='region', y='sales', data=df)
sns.pairplot(df[['sales', 'profit', 'cost']])
```

### 3. Plotly – Etkileşimli Grafikler

```python
import plotly.express as px
fig = px.scatter(df, x='cost', y='sales', color='category', hover_data=['profit'])
fig.show()
```

### 4. Grafik En İyi Uygulamaları

* Başlık, eksen etiketi, açıklama (legend) eklemek okunabilirliği artırır.
* Renk paletleri ve görsel netlik önemlidir.

---

## 📈 Statistics & Probability

### 1. Tanımlayıcı İstatistikler

```python
mean = df['sales'].mean()
median = df['sales'].median()
std = df['sales'].std()
q1, q3 = df['sales'].quantile(0.25), df['sales'].quantile(0.75)
```

### 2. Olasılık Temelleri

* Olay ve örnek uzay tanımları.
* Koşullu olasılık ve Bayes teoremi formülü açıklanır.

### 3. Dağılımlar

```python
import numpy as np, seaborn as sns
data = np.random.binomial(n=10, p=0.5, size=1000)
sns.histplot(data, kde=False)
```

### 4. CLT ve Simülasyon

* Farklı dağılımlardan örnekler çekip ortalamaların dağılımını grafikle göstererek CLT açıklanır.

### 5. Hipotez Testi

```python
from scipy.stats import ttest_ind
stat, pval = ttest_ind(df[df.group=='A']['sales'], df[df.group=='B']['sales'])
print(f"t={stat:.3f}, p={pval:.3f}")
```

---

## 🤖 Machine Learning – scikit‑learn

### 1. Model Eğitim Örneği

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
X = df[['feature1', 'feature2']]
y = df['target']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
model = LogisticRegression().fit(X_train, y_train)
preds = model.predict(X_test)
```

### 2. Model Değerlendirme

```python
from sklearn.metrics import classification_report, confusion_matrix, roc_auc_score
print(classification_report(y_test, preds))
print(confusion_matrix(y_test, preds))
print('ROC AUC:', roc_auc_score(y_test, model.predict_proba(X_test)[:,1]))
```

### 3. Cross‑Validation

```python
from sklearn.model_selection import cross_val_score
scores = cross_val_score(model, X, y, cv=5, scoring='roc_auc')
print("CV AUC scores:", scores)
```

### 4. Grid Search

```python
from sklearn.model_selection import GridSearchCV
from sklearn.ensemble import RandomForestClassifier
param_grid = {'n_estimators': [50, 100], 'max_depth': [None, 10]}
grid = GridSearchCV(RandomForestClassifier(random_state=42), param_grid, cv=3, scoring='accuracy').fit(X, y)
print("Best params:", grid.best_params_)
```

### 5. Pipeline Yapısı

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
pipe = Pipeline([('scaler', StandardScaler()), ('clf', RandomForestClassifier(random_state=42))])
pipe.fit(X_train, y_train)
```

### 6. Unsupervised Learning

```python
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=3, random_state=42).fit(X)
labels = kmeans.labels_
```

```python
from sklearn.decomposition import PCA
pca = PCA(n_components=2).fit_transform(X)
```

---

## 🧠 Deep Learning (Optional)

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

model = Sequential([
    Dense(64, activation='relu', input_shape=(X.shape[1],)),
    Dense(1, activation='sigmoid')
])
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
model.fit(X_train, y_train, epochs=10, batch_size=32, validation_split=0.2)
```

---

## 📜 Natural Language Processing (NLP)

### 1. Text Preprocessing

```python
import nltk
nltk.download('punkt')
from nltk.tokenize import word_tokenize
tokens = word_tokenize("This is a sample sentence.")
```

* Stop words ve lemmatization örnekleri:

```python
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer

stop = stopwords.words('english')
lemmatizer = WordNetLemmatizer()
processed = [lemmatizer.lemmatize(token.lower()) for token in tokens if token.isalpha() and token.lower() not in stop]
```

### 2. Vectorization

```python
from sklearn.feature_extraction.text import TfidfVectorizer
corpus = ["I love data science", "Machine learning is fun"]
tfidf = TfidfVectorizer().fit_transform(corpus)
```

### 3. Word Embeddings

```python
from gensim.models import Word2Vec
sentences = [doc.split() for doc in corpus]
w2v = Word2Vec(sentences, vector_size=50, window=2, min_count=1)
print(w2v.wv['science'])
```

### 4. Sentiment Analysis

```python
from sklearn.linear_model import LogisticRegression
model = LogisticRegression()
model.fit(tfidf, [1, 0])  # basit pozitif/negatif örneği
```

### 5. Topic Modeling

```python
import gensim.corpora as corpora
from gensim.models import LdaModel

id2word = corpora.Dictionary(sentences)
corpus_gensim = [id2word.doc2bow(text) for text in sentences]
lda = LdaModel(corpus=corpus_gensim, id2word=id2word, num_topics=2)
print(lda.print_topics())
```

---

## 🧪 End-to-End Projects

### Titanic Walkthrough (notebook şeklinde)

1. EDA: eksik değer analizi
2. Feature Engineering: kabin, aile büyüklüğü, yaş grupları
3. Modelleme: RandomForestClassifier
4. Değerlendirme: ROC AUC, accuracy, confusion matrix

### Deployment Demo

* **Streamlit** uygulaması örneği:

  ```bash
  pip install streamlit
  streamlit run app.py
  ```

  Uygulama, kullanıcı girişini alır, model predict, sonucu gösterir.

---

## 🚀 Repo Kullanımı

1. `notebooks/` altında her konunun ayrıntılı notebook'una ulaşabilirsiniz.
2. `projects/` dizininde tam projeler yer alır.
3. `requirements.txt` ile ortamı hazırlayıp reproduktif şekilde çalışabilirsiniz.
4. Pull request ve önerilerle katkı sağlamanızdan memnuniyet duyarım 🙌
