# Makine Öğrenmesi

Computer Vision: Bilgisayarların fotoğraf veya videolardan nesneleri tanıma ve takip etmelerini sağlayan bir kavram.

Denetimli Öğrenme;

- Regresyon
- Sınıflama

Denetimsiz Öğrenme;

- Kümeleme
- Boyut İndirgeme

Aşağıdaki kavramlar da ihtiyaç halinde öğrenilebilir. 

- Yarı-Denetimli Öğrenme
- Takviyeli Öğrenme

# Eğitim ve Test Verisi Ayırma

Genel olarak %80 veya %70 eğitim seti olacak şekilde ayırılır.

```Python
import pandas as pd
from sklearn.model_selection import train_test_split

veri=pd.read_excel("C:/Users/90506/Desktop/Ornek.xlsx")

y=veri["Y"]
X=veri[["X1", "X2"]]

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

# Regresyon Model Başarısı

$R^2$ Bağımsız değişkenin bağımlı değişkeni ne kadar açıklayadığını gösterir. Düzeltilmiş $R^2$ ise birden çok bağımsız değişken olduğunda kullanılır. 

Başarı değerlendirmesinde kullanılan yapılar;

## Mean Square Error (MSE)

$$
MSE = \frac{1}{n} \sum_{i=1}^{n} e_i^2
$$

Hataların karesi alındığı için aykırı değerlerin tespiti kolaylaşır.

## Root Mean Square Error (RMSE)

$$
RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n} e_i^2}
$$

Hataların nasıl bir dağılım gösterdiğine dair bilgi verir.

## Mean Absolute Error (MAE)

$$
MAE = \frac{1}{n} \sum_{i=1}^{n} |e_i|
$$

Kare olmadığı için uçdeğerler hakkında sapmalı bir sonuç çıkarmayacaktır. Sadece mesafe bazında ölçüm yapacaktır.

## Örnekler

```Python
import pandas as pd
import statsmodels.api as sm

veri=pd.read_excel("C:/Users/90506/Desktop/Ornek.xlsx")

y=veri ["Y"]
X=veri [["X1", "X2"]]

sabit=sm.add_constant(X)
model=sm.OLS(y, sabit).fit()

print(model.summary())

# OLS Regression Results
# ==============================================================================
# Dep. Variable:                      Y   R-squared:                       0.002
# Model:                            OLS   Adj. R-squared:                 -0.000
# Method:                 Least Squares   F-statistic:                    0.8301
# Date:                Wed, 17 Aug 2022   Prob (F-statistic):              0.436
# Time:                        13:48:23   Log-Likelihood:               -6182.7
# No. Observations:                 1000   AIC:                        1.237e+04
# Df Residuals:                      997   BIC:                        1.239e+04
# Df Model:                           2   Covariance Type:             nonrobust
# ==============================================================================
#                    coef    std err          t      P>|t|      [0.025   0.975]
# ---------------------------------------------------------------------
# const          294.6826     13.597     21.672      0.000     268.000    321.365
# X1               0.0326      0.032      1.018      0.309      -0.030     0.096
# X2              -0.0272      0.032     -0.843      0.399      -0.091     0.036
# ==============================================================================
# Omnibus:          996.319                Durbin-Watson:                  2.071
# Prob(Omnibus):      0.000                Jarque-Bera (JB):              63.904
# Skew:               0.045                Prob(JB):                    1.33e-14
# Kurtosis:           1.765                Cond. No.                    1.59e+03
# ==============================================================================

from sklearn.metrics import mean_squared_error

mse=mean_squared_error(y, tahmin)
rmse=mean_squared_error (y, tahmin, squared=False)
mae=mean_absolute_error(y, tahmin)
```

# Hata Matrisi (Confusion matrix)

|               | Tahmin Doğru   | Tahmin Yanlış   |
| ------------- | :------------: | :-------------: |
| Gerçek Doğru  |  TP            | FN              |
| Gerçek Yanlış |  FP            | TN              |

Bu yapı üzerinden bazı performans değerleri hesaplanır;

1. Doğruluk (Accuracy): Yüzde kaç doğru tahmin ettiğini gösterir (TP + TN) / Toplam
1. Hata Oranı: Yüzde kaç hatalı tahmin ettiğini gösterir (FP + FN) / Toplam
1. Kesinlik (Precision): Yüzde kaç hatalı tahmin ettiğini gösterir TP / (TP + FP)
1. Duyarlılık (Recall): Modelin yüzde kaç oranla yanlış tahmin ettiğini verir TP / (TP + FN)

> Burada yanlış olan bir değeri doğru olarak tahmin etmemiz riskliyse kesinlik, doğru bir değere yanlış denilmesi riskliyse duyarlılık a bakılır.

F1 score bize hme kesinlik hem de duyarlılık üzerinden işlem gerçekleştiriyor ve bize kesinlik ve duyarlılık değerlerinin harmonik ortalamasını verir.

$$
\text{Harmonik ortalama} = \frac{n}{\frac{1}{x_1}+\frac{1}{x_2}+ \cdot\cdot\cdot\frac{1}{x_k}}
$$

$$
\text{F1 score} = \frac{2}{\frac{1}{\text{Kesinlik}}+\frac{1}{\text{Duyarlılık}}}
$$

Ek olarak ROC eğrisi bize True pozitif ve false pozitif oranlarını kıyaslamamıza izin veren bir eğri grafiğidir.

# Basit Doğrusal Regresyon

$$
\hat{y} = \beta_0 + \beta_1 \cdot x + \epsilon
$$

```Python
import pandas as pd 

data = pd.DataFrame(
    {

        "YearsExperience":[1.1, 1.3, 1.5, 2.0, 2.2, 2.9, 3.0, 3.2, 3.2, 3.7, 3.9, 4.0, 4.0, 4.1, 4.5, 4.9, 5.1, 5.3, 5.9, 6.0, 6.8, 7.1, 7.9, 8.2, 8.7, 9.0, 9.5, 9.6, 10.3, 10.5],
        "Salary":[39343.00, 46205.00, 37731.00, 43525.00, 39891.00, 56642.00, 60150.00, 54445.00, 64445.00, 57189.00, 63218.00, 55794.00, 56957.00, 57081.00, 61111.00, 67938.00, 66029.00, 83088.00, 81363.00, 93940.00, 91738.00, 98273.00, 101302.00, 113812.00, 109431.00, 105582.00, 116969.00, 112635.00, 122391.00, 121872.00]

    }
)

veri = data.copy()

print(veri.isnull().sum())

# YearsExperience    0
# Salary             0
# dtype: int64

import matplotlib.pyplot as plt

y=veri ["Salary"]
X=veri ["YearsExperience"]

plt.scatter(X,y)
plt.show()

```

![image](./images/basitreg1.png)

```Python
import statsmodels.api as sm

sabit=sm.add_constant(X)
model=sm.OLS(y, sabit).fit()

print(model.summary())

#                             OLS Regression Results                            
# ==============================================================================
# Dep. Variable:                 Salary   R-squared:                       0.957
# Model:                            OLS   Adj. R-squared:                  0.955
# Method:                 Least Squares   F-statistic:                     622.5
# Date:                Wed, 27 Aug 2025   Prob (F-statistic):           1.14e-20
# Time:                        09:49:28   Log-Likelihood:                -301.44
# No. Observations:                  30   AIC:                             606.9
# Df Residuals:                      28   BIC:                             609.7
# Df Model:                           1                                         
# Covariance Type:            nonrobust                                         
# ===================================================================================
#                       coef    std err          t      P>|t|      [0.025      0.975]
# -----------------------------------------------------------------------------------
# const            2.579e+04   2273.053     11.347      0.000    2.11e+04    3.04e+04
# YearsExperience  9449.9623    378.755     24.950      0.000    8674.119    1.02e+04
# ==============================================================================
# Omnibus:                        2.140   Durbin-Watson:                   1.648
# Prob(Omnibus):                  0.343   Jarque-Bera (JB):                1.569
# Skew:                           0.363   Prob(JB):                        0.456
# Kurtosis:                       2.147   Cond. No.                         13.2
# ==============================================================================

# Notes:
# [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
```

P>|t| kısmına bakıldığında sabit ve değişken katsayısı anlamlı çıkmıştır. Prob (F-statistic) kısmı incelendiğinde model anlamlı çıkmıştır. R-squared 0,957 ile gayet uyumlu gözükmektedir.

Sklearn ile model oluşturalım;

```Python
from sklearn.linear_model import LinearRegression

lr=LinearRegression()
lr.fit(X.values.reshape(-1,1), y.values.reshape(-1,1)) # .values.reshape(-1,1) array olması gerektiği için yapıldı.
print(lr.coef_, lr.intercept_) # [[9449.96232146]] [25792.20019867]

print(lr.predict(X.values.reshape(-1,1)))

# [[ 36187.15875227]
#  [ 38077.15121656]
#  [ 39967.14368085]
#  [ 44692.12484158]
#  [ 46582.11730587]
#  [ 53197.09093089]
#  [ 54142.08716303]
#  [ 56032.07962732]
#  [ 56032.07962732]
#  [ 60757.06078805]
#  [ 62647.05325234]
#  [ 63592.04948449]
#  [ 63592.04948449]
#  [ 64537.04571663]
#  [ 68317.03064522]
#  [ 72097.0155738 ]
#  [ 73987.00803809]
#  [ 75877.00050238]
#  [ 81546.97789525]
#  [ 82491.9741274 ]
#  [ 90051.94398456]
#  [ 92886.932681  ]
#  [100446.90253816]
#  [103281.8912346 ]
#  [108006.87239533]
#  [110841.86109176]
#  [115566.84225249]
#  [116511.83848464]
#  [123126.81210966]
#  [125016.80457395]]
```

# Çoklu Doğrusal Regresyon

$$
\hat{y} = \beta_0 + \beta_1 \cdot x_1 + \beta_2 \cdot x_2 + \cdot\cdot\cdot + \beta_k \cdot x_k + \epsilon
$$

```Python
import pandas as pd 

data = pd.DataFrame(
    {

        "TV":[230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8.6, 199.8, 66.1, 214.7, 23.8, 97.5, 204.1, 195.4, 67.8, 281.4, 69.2, 147.3, 218.4, 237.4, 13.2, 228.3, 62.3, 262.9, 142.9, 240.1, 248.8, 70.6, 292.9, 112.9, 97.2, 265.6, 95.7, 290.7, 266.9, 74.7, 43.1, 228, 202.5, 177, 293.6, 206.9, 25.1, 175.1, 89.7, 239.9, 227.2, 66.9, 199.8, 100.4, 216.4, 182.6, 262.7, 198.9, 7.3, 136.2, 210.8, 210.7, 53.5, 261.3, 239.3, 102.7, 131.1, 69, 31.5, 139.3, 237.4, 216.8, 199.1, 109.8, 26.8, 129.4, 213.4, 16.9, 27.5, 120.5, 5.4, 116, 76.4, 239.8, 75.3, 68.4, 213.5, 193.2, 76.3, 110.7, 88.3, 109.8, 134.3, 28.6, 217.7, 250.9, 107.4, 163.3, 197.6, 184.9, 289.7, 135.2, 222.4, 296.4, 280.2, 187.9, 238.2, 137.9, 25, 90.4, 13.1, 255.4, 225.8, 241.7, 175.7, 209.6, 78.2, 75.1, 139.2, 76.4, 125.7, 19.4, 141.3, 18.8, 224, 123.1, 229.5, 87.2, 7.8, 80.2, 220.3, 59.6, 0.7, 265.2, 8.4, 219.8, 36.9, 48.3, 25.6, 273.7, 43, 184.9, 73.4, 193.7, 220.5, 104.6, 96.2, 140.3, 240.1, 243.2, 38, 44.7, 280.7, 121, 197.6, 171.3, 187.8, 4.1, 93.9, 149.8, 11.7, 131.7, 172.5, 85.7, 188.4, 163.5, 117.2, 234.5, 17.9, 206.8, 215.4, 284.3, 50, 164.5, 19.6, 168.4, 222.4, 276.9, 248.4, 170.2, 276.7, 165.6, 156.6, 218.5, 56.2, 287.6, 253.8, 205, 139.5, 191.1, 286, 18.7, 39.5, 75.5, 17.2, 166.8, 149.7, 38.2, 94.2, 177, 283.6, 232.1],
        "Radio":[37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1, 2.6, 5.8, 24, 35.1, 7.6, 32.9, 47.7, 36.6, 39.6, 20.5, 23.9, 27.7, 5.1, 15.9, 16.9, 12.6, 3.5, 29.3, 16.7, 27.1, 16, 28.3, 17.4, 1.5, 20, 1.4, 4.1, 43.8, 49.4, 26.7, 37.7, 22.3, 33.4, 27.7, 8.4, 25.7, 22.5, 9.9, 41.5, 15.8, 11.7, 3.1, 9.6, 41.7, 46.2, 28.8, 49.4, 28.1, 19.2, 49.6, 29.5, 2, 42.7, 15.5, 29.6, 42.8, 9.3, 24.6, 14.5, 27.5, 43.9, 30.6, 14.3, 33, 5.7, 24.6, 43.7, 1.6, 28.5, 29.9, 7.7, 26.7, 4.1, 20.3, 44.5, 43, 18.4, 27.5, 40.6, 25.5, 47.8, 4.9, 1.5, 33.5, 36.5, 14, 31.6, 3.5, 21, 42.3, 41.7, 4.3, 36.3, 10.1, 17.2, 34.3, 46.4, 11, 0.3, 0.4, 26.9, 8.2, 38, 15.4, 20.6, 46.8, 35, 14.3, 0.8, 36.9, 16, 26.8, 21.7, 2.4, 34.6, 32.3, 11.8, 38.9, 0, 49, 12, 39.6, 2.9, 27.2, 33.5, 38.6, 47, 39, 28.9, 25.9, 43.9, 17, 35.4, 33.2, 5.7, 14.8, 1.9, 7.3, 49, 40.3, 25.8, 13.9, 8.4, 23.3, 39.7, 21.1, 11.6, 43.5, 1.3, 36.9, 18.4, 18.1, 35.8, 18.1, 36.8, 14.7, 3.4, 37.6, 5.2, 23.6, 10.6, 11.6, 20.9, 20.1, 7.1, 3.4, 48.9, 30.2, 7.8, 2.3, 10, 2.6, 5.4, 5.7, 43, 21.3, 45.1, 2.1, 28.7, 13.9, 12.1, 41.1, 10.8, 4.1, 42, 35.6, 3.7, 4.9, 9.3, 42, 8.6],
        "Newspaper":[69.2, 45.1, 69.3, 58.5, 58.4, 75, 23.5, 11.6, 1, 21.2, 24.2, 4, 65.9, 7.2, 46, 52.9, 114, 55.8, 18.3, 19.1, 53.4, 23.5, 49.6, 26.2, 18.3, 19.5, 12.6, 22.9, 22.9, 40.8, 43.2, 38.6, 30, 0.3, 7.4, 8.5, 5, 45.7, 35.1, 32, 31.6, 38.7, 1.8, 26.4, 43.3, 31.5, 35.7, 18.5, 49.9, 36.8, 34.6, 3.6, 39.6, 58.7, 15.9, 60, 41.4, 16.6, 37.7, 9.3, 21.4, 54.7, 27.3, 8.4, 28.9, 0.9, 2.2, 10.2, 11, 27.2, 38.7, 31.7, 19.3, 31.3, 13.1, 89.4, 20.7, 14.2, 9.4, 23.1, 22.3, 36.9, 32.5, 35.6, 33.8, 65.7, 16, 63.2, 73.4, 51.4, 9.3, 33, 59, 72.3, 10.9, 52.9, 5.9, 22, 51.2, 45.9, 49.8, 100.9, 21.4, 17.9, 5.3, 59, 29.7, 23.2, 25.6, 5.5, 56.5, 23.2, 2.4, 10.7, 34.5, 52.7, 25.6, 14.8, 79.2, 22.3, 46.2, 50.4, 15.6, 12.4, 74.2, 25.9, 50.6, 9.2, 3.2, 43.1, 8.7, 43, 2.1, 45.1, 65.6, 8.5, 9.3, 59.7, 20.5, 1.7, 12.9, 75.6, 37.9, 34.4, 38.9, 9, 8.7, 44.3, 11.9, 20.6, 37, 48.7, 14.2, 37.7, 9.5, 5.7, 50.5, 24.3, 45.2, 34.6, 30.7, 49.3, 25.6, 7.4, 5.4, 84.8, 21.6, 19.4, 57.6, 6.4, 18.4, 47.4, 17, 12.8, 13.1, 41.8, 20.3, 35.2, 23.7, 17.6, 8.3, 27.4, 29.7, 71.8, 30, 19.6, 26.6, 18.2, 3.7, 23.4, 5.8, 6, 31.6, 3.6, 6, 13.8, 8.1, 6.4, 66.2, 8.7],
        "Sales":[22.1, 10.4, 12, 16.5, 17.9, 7.2, 11.8, 13.2, 4.8, 15.6, 12.6, 17.4, 9.2, 13.7, 19, 22.4, 12.5, 24.4, 11.3, 14.6, 18, 17.5, 5.6, 20.5, 9.7, 17, 15, 20.9, 18.9, 10.5, 21.4, 11.9, 13.2, 17.4, 11.9, 17.8, 25.4, 14.7, 10.1, 21.5, 16.6, 17.1, 20.7, 17.9, 8.5, 16.1, 10.6, 23.2, 19.8, 9.7, 16.4, 10.7, 22.6, 21.2, 20.2, 23.7, 5.5, 13.2, 23.8, 18.4, 8.1, 24.2, 20.7, 14, 16, 11.3, 11, 13.4, 18.9, 22.3, 18.3, 12.4, 8.8, 11, 17, 8.7, 6.9, 14.2, 5.3, 11, 11.8, 17.3, 11.3, 13.6, 21.7, 20.2, 12, 16, 12.9, 16.7, 14, 7.3, 19.4, 22.2, 11.5, 16.9, 16.7, 20.5, 25.4, 17.2, 16.7, 23.8, 19.8, 19.7, 20.7, 15, 7.2, 12, 5.3, 19.8, 18.4, 21.8, 17.1, 20.9, 14.6, 12.6, 12.2, 9.4, 15.9, 6.6, 15.5, 7, 16.6, 15.2, 19.7, 10.6, 6.6, 11.9, 24.7, 9.7, 1.6, 17.7, 5.7, 19.6, 10.8, 11.6, 9.5, 20.8, 9.6, 20.7, 10.9, 19.2, 20.1, 10.4, 12.3, 10.3, 18.2, 25.4, 10.9, 10.1, 16.1, 11.6, 16.6, 16, 20.6, 3.2, 15.3, 10.1, 7.3, 12.9, 16.4, 13.3, 19.9, 18, 11.9, 16.9, 8, 17.2, 17.1, 20, 8.4, 17.5, 7.6, 16.7, 16.5, 27, 20.2, 16.7, 16.8, 17.6, 15.5, 17.2, 8.7, 26.2, 17.6, 22.6, 10.3, 17.3, 20.9, 6.7, 10.8, 11.9, 5.9, 19.6, 17.3, 7.6, 14, 14.8, 25.5, 18.4]
  
    }
)

veri=data.copy()

print(veri.corr()["Sales"])

# TV           0.901208
# Radio        0.349631
# Newspaper    0.157960
# Sales        1.000000

import matplotlib.pyplot as plt
import seaborn as sns

sns.pairplot(veri, kind="reg")
plt.show()
```

![image](./images/coklureg1.png) 

- TV düşündüğümüz gibi çok iyi bir ilişki sergiliyor.
- Radio iyi bir ilişki sergiliyor ama TV kadar iyi değil.
- Newspaper iyi bir ilişki sergilemiyor.

```Python
sns.boxplot(veri["TV"])
plt.show()
```

![image](./images/coklureg2.png) 

```Python
sns.boxplot(veri["Radio"])
plt.show()
```

![image](./images/coklureg3.png)

```Python
sns.boxplot(veri["Newspaper"])
plt.show()
```

![image](./images/coklureg4.png) 

TV ve Radio'da uç değer yokken Newspaper değişkeninde bulunmaktadır. Baskılama işlemi yapalım;

```Python
Q1=veri ["Newspaper"].quantile(0.25)
Q3=veri["Newspaper"].quantile(0.75)
IQR=Q3-Q1
ustsınır=Q3+1.5*IQR
aykırı = veri["Newspaper"]>ustsınır

veri.loc[aykırı, "Newspaper"]=ustsınır
sns.boxplot(veri["Newspaper"])
plt.show()
```

![image](./images/coklureg5.png)

```Python
import statsmodels.api as sm

y=veri ["Sales"]
X=veri [["TV", "Radio", "Newspaper"]]

sabit=sm.add_constant(X)
model=sm.OLS(y, sabit).fit()
print(model.summary())

#                             OLS Regression Results                            
# ==============================================================================
# Dep. Variable:                  Sales   R-squared:                       0.903
# Model:                            OLS   Adj. R-squared:                  0.901
# Method:                 Least Squares   F-statistic:                     605.4
# Date:                Wed, 27 Aug 2025   Prob (F-statistic):           8.13e-99
# Time:                        11:59:52   Log-Likelihood:                -383.33
# No. Observations:                 200   AIC:                             774.7
# Df Residuals:                     196   BIC:                             787.9
# Df Model:                           3                                         
# Covariance Type:            nonrobust                                         
# ==============================================================================
#                  coef    std err          t      P>|t|      [0.025      0.975]
# ------------------------------------------------------------------------------
# const          4.6246      0.308     15.004      0.000       4.017       5.232
# TV             0.0544      0.001     39.587      0.000       0.052       0.057
# Radio          0.1070      0.008     12.594      0.000       0.090       0.124
# Newspaper      0.0004      0.006      0.062      0.951      -0.011       0.012
# ==============================================================================
# Omnibus:                       16.072   Durbin-Watson:                   2.250
# Prob(Omnibus):                  0.000   Jarque-Bera (JB):               27.634
# Skew:                          -0.431   Prob(JB):                     9.99e-07
# Kurtosis:                       4.604   Cond. No.                         455.
# ==============================================================================

# Notes:
# [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

import statsmodels.api as sm

y=veri ["Sales"]
X=veri [["TV", "Radio"]]

sabit=sm.add_constant(X)
model=sm.OLS(y, sabit).fit()
print(model.summary())

#                             OLS Regression Results                             
# ==============================================================================
# Dep. Variable:                  Sales   R-squared:                       0.903
# Model:                            OLS   Adj. R-squared:                  0.902
# Method:                 Least Squares   F-statistic:                     912.7
# Date:                Wed, 27 Aug 2025   Prob (F-statistic):          2.39e-100
# Time:                        12:01:06   Log-Likelihood:                -383.34
# No. Observations:                 200   AIC:                             772.7
# Df Residuals:                     197   BIC:                             782.6
# Df Model:                           2                                         
# Covariance Type:            nonrobust                                         
# ==============================================================================
#                  coef    std err          t      P>|t|      [0.025      0.975]
# ------------------------------------------------------------------------------
# const          4.6309      0.290     15.952      0.000       4.058       5.203
# TV             0.0544      0.001     39.726      0.000       0.052       0.057
# Radio          0.1072      0.008     13.522      0.000       0.092       0.123
# ==============================================================================
# Omnibus:                       16.227   Durbin-Watson:                   2.252
# Prob(Omnibus):                  0.000   Jarque-Bera (JB):               27.973
# Skew:                          -0.434   Prob(JB):                     8.43e-07
# Kurtosis:                       4.613   Cond. No.                         425.
# ==============================================================================

# Notes:
# [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

from sklearn.model_selection import train_test_split

X_train, X_test,y_train,y_test=train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.linear_model import LinearRegression

lr=LinearRegression()
lr.fit(X_train, y_train)
print(lr.coef_) # [0.05450736 0.10325764]

tahmin=lr.predict(X_test)
y_test=y_test.sort_index()

df=pd.DataFrame({"Gerçek":y_test, "Tahmin": tahmin})
df.plot(kind="line")
plt.show()
```

![image](./images/coklureg6.png)

```Python
tahmin=lr.predict(X_test)
y_test=y_test.sort_index() # düzenli gösterim için

df=pd.DataFrame({"Gerçek":y_test, "Tahmin": tahmin})
df.plot(kind="line")
plt.show()
```

![image](./images/coklureg7.png) 

Veride eksik değerler yok. Eğer nan değerleri olsaydı;

```Python
from sklearn.impute import SimpleImputer
import numpy as np

imputer=SimpleImputer (missing_values=np.nan, strategy="mean") # eksik değerler NaN olarak gözüktüğü için
imputer=imputer.fit(veri)
veri.iloc[:,:]=imputer.transform(veri)
```

```Python
import sklearn.metrics as mt

r2=mt.r2_score (y_test, tahmin)
mse=mt.mean_squared_error(y_test, tahmin)
rmse=np.sqrt(mse) # mt.mean_squared_error(y_test, tahmin, squared=False)
mae=mt.mean_absolute_error(y_test, tahmin)
print(r2, mse, rmse, mae) # -0.5917637530745343 49.18725091013148 7.013362311340509 5.448564072450313
```
## Kategorik değişkenler

```Python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

data=sns.load_dataset("tips")
veri=data.copy()
print(veri)

# total_bill   tip   sex    smoker   day    time   size
# 16.99        1.01  Female No      Sun    Dinner 2
# 10.34        1.66  Male   No      Sun    Dinner 3
# 21.01        3.50  Male   No      Sun    Dinner 3
# 23.68        3.31  Male   No      Sun    Dinner 2
# 24.59        3.61  Female No      Sun    Dinner 4
# ...
# 29.03        5.92  Male   No      Sat    Dinner 3
# 27.18        2.00  Female Yes     Sat    Dinner 2
# 22.67        2.00  Male   Yes     Sat    Dinner 2
# 17.82        1.75  Male   No      Sat    Dinner 2
# 18.78        3.00  Female No      Thur   Dinner 2

print(veri.isnull().sum())

# total_bill   0
# tip          0
# sex          0
# smoker       0
# day          0
# time         0
# size         0
# dtype: int64

print(veri.dtypes)

# total_bill   float64
# tip          float64
# sex          category
# smoker       category
# day          category
# time         category
# size         int64
# dtype: object

kategori=[]
kategorik=veri.select_dtypes (include=["category"])

for i in kategorik.columns:
    kategori.append(i)
print(kategori) # ['sex' 'smoker', 'day', 'time']

veri=pd.get_dummies(veri, columns=kategori, drop_first=True)

y=veri["tip"]
X=veri.drop(columns="tip", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.linear_model import LinearRegression

lr=LinearRegression()
lr.fit(X_train, y_train)

tahmin=lr.predict(X_test)
y_test=y_test.sort_index()

df=pd.DataFrame({"Gerçek":y_test, "Tahmin": tahmin})
df.plot(kind="line")
plt.show()
```

![image](./images/coklureg8.png)

>Model kötü bir sonuç verdi. Bazı şeyleri yapmayıo atlamış olabiliriz veya model bu veri için uygun olmayabilir. 

```Python
import sklearn.metrics as mt

print(mt.r2_score (y_test, tahmin)) # -1.0084298085720063
```

Buna göre doğrusal modele göre çok uyumsuz bir yapı olduğunu düşünebiliriz.

# Model Tuning

```Python
import pandas as pd
from sklearn.model_selection import train_test_split,KFold
from sklearn.linear_model import LinearRegression
import sklearn.metrics as mt

data=pd.read_csv("C:/Users/90506/Desktop/Reklam.csv")
veri=data.copy()

y=veri ["Sales"]
X=veri.drop(columns="Sales", axis=1)

X_train, X_test,y_train,y_test=train_test_split(X, y, test_size=0.2, random_state=42)

lr=LinearRegression()
model=lr.fit(X_train, y_train)

def skor(model, x_train, x_test,y_train,y_test):
    egitimtahmin=model.predict(x_train)
    testtahmin=model.predict(x_test)

    r2_egitim=mt.r2_score(y_train, egitimtahmin)
    r2_test=mt.r2_score(y_test, testtahmin)

    mse_egitim=mt.mean_squared_error(y_train, egitimtahmin)
    mse_test=mt.mean_squared_error(y_test, testtahmin)

    return [r2_egitim, r2_test, mse_egitim, mse_test]

print("Eğitim R2= {} Eğitim MSE= {}".format(sonuc1[0], sonuc1[2]))
print("Test R2= {} Test MSE= {}".format(sonuc1 [1], sonuc1[3]))

# Eğitim R2= 0.8957008271017818 Eğitim MSE= 2.705129423081414
# Test R2= 0.899438024100912 Test MSE= 3.1740973539761033

lr_cv=LinearRegression()
k=5
iterasyon=1
cv=KFold(n_splits=k)

for egitimindex, testindex in cv.split(x):
    X_train, X_test=X.loc[egitimindex], X.loc[testindex]
    y_train, y_test=y.loc[egitimindex], y.loc[testindex]
    lr_cv.fit(X_train, y_train)

    sonuc2=skor(model=lr_cv,x_train=X_train,x_test=X_test,y_train=y_train,y_test=y_test)

    print("İterasyon:{}".format(iterasyon))
    print("Eğitim R2= {} Eğitim MSE= {}".format(sonuc2 [0], sonuc2[2]))
    print("Test R2= {} Test MSE= {}".format(sonuc2[1], sonuc2[3]))
    iterasyon +=1   

# İterasyon: 1
# Eğitim R2 = 0.9010130247585829 Eğitim MSE= 2.7115931715887225 
# Test R2 = 6.8786519804831341 Test MSE= 3.1365399007617047
# İterasyon: 2
# Eğitim R2 = 0.8903959783952622 Eğitim MSE 2.889696157849928
# Test R2 = 0.9176321165614462 Test MSE= 2.4256677581593875
# İterasyon: 3
# Eğitim R2 = 0.8896931584883978
# Eğitim MSE= 3.104396076662706
# Test R2 = 0.9293303235799653 Test MSE= 1.5852250798740997
# İterasyon: 4
# Eğitim R2= 0.9145880146193406 Eğitim nMSE = 2.241641526638164
# Test R2 = 8.8144396391722338 Test MSE= 5.4261550604294575
# İterasyon: 5
# Eğitim R2= 0.8961523241120161 Eğitim MSE= 2.82179249487708
# Test R2= 0.8954782879224387 Test MSE= 2.7911451862763954
```

# Polinomal Regresyon

Basit polinomsal regresyon denklemi;

$$
\hat{y} = \beta_0 + \beta_1 \cdot x + \beta_2 \cdot x^2 + \beta_3 \cdot x^3 +  \cdot  \cdot + \beta_k \cdot x^k + \epsilon
$$

X1 ve X2 değişkenleri olan çoklu polinomsal regresyon denklemi;

$$
y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \dots + \beta_n x_n + \beta_{n+1} x_1^2 + \beta_{n+2} x_2^2 + \dots + \beta_{2n} x_n^2 + \epsilon
$$

FOrmüldeki üst yapısına polinomun derecesi denir. 

```Python
import pandas as pd
import matplotlib.pyplot as plt

data = pd.DataFrame(
    {
        "Sıcaklık":[50, 50, 50, 70, 70, 70, 80, 80, 80, 90, 90, 90, 100, 100, 100],
        "Verim":[3.3, 2.8, 2.9, 2.3, 2.6, 2.1, 2.5, 2.9, 2.4, 3.0, 3.1, 2.8, 3.3, 3.5, 3.0]
    }
)

veri=data.copy()
print(veri)

y=veri["Verim"]
X=veri["Sıcaklık"]

plt.scatter(X,y)
plt.show()
```

![image](./images/polreg1.png)

```Python
from sklearn.linear_model import LinearRegression

y=y.values.reshape(-1,1)
X=X.values.reshape(-1,1)

lr=LinearRegression()
lr.fit(X,y)

import sklearn.metrics as mt

tahmin=lr.predict(X)

r2dog=mt.r2_score(y,tahmin)
mse=mt.mean_squared_error(y,tahmin)

print("Doğrusal R2= {} Doğrusal MSE= {}".format(r2dog,mse))
# Doğrusal R2= 0.09241764560913446 Doğrusal MSE= 0.13270870870870877

from sklearn.preprocessing import Polynomial Features

pol=PolynomialFeatures(degree=2) # 2 ile başla arttır
X_pol=pol.fit_transform(X)

lr2=LinearRegression()
lr2.fit(X_pol,y)

tahmin2=lr2.predict(X_pol)
r2pol=mt.r2_score(y, tahmin2)
msepol=mt.mean_squared_error(y, tahmin2)
print("Doğrusal R2= {} Doğrusal MSE= {}".format(r2pol,msepol))

# Pol R2= 0.6732052768464262 Pol MSE= 0.04778465063001146

pol=PolynomialFeatures(degree=3) # 2 ile başla arttır
X_pol=pol.fit_transform(X)

lr2=LinearRegression()
lr2.fit(X_pol,y)

tahmin2=lr2.predict(X_pol)
r2pol=mt.r2_score(y, tahmin2)
msepol=mt.mean_squared_error(y, tahmin2)
print("Pol R2= {} Pol MSE= {}".format(r2pol,msepol))
# Pol R2= 0.7354619487297125 Pol MSE= 0.03868134171907758

plt.scatter(X, y, color="red")
plt.show()
```

![image](./images/polreg2.png)

```python
plt.scatter(x, y, color="red")
plt.plot(X, tahmin, color="blue")
plt.show()
```

![image](./images/polreg3.png) 

```python
plt.scatter(X, y, color="red")
plt.plot(X, tahmin2, color="blue")
plt.show()
```

![image](./images/polreg4.png) 

---

dataset: https://www.kaggle.com/datasets/quantbruce/real-estate-price-prediction?resource=download

```python
import pandas as pd

data=pd.read_csv("C:/Users/90506/Desktop/Ev.csv")
veri=data.copy()

veri.drop(columns=["No", "X1 transaction date", "X5 latitude", "X6 longitude"], axis=1,inplace=True)

veri=veri.rename(columns={"X2 house age": "Ev Yaşı",
"X3 distance to the nearest MRT station": "Metro Uzaklık",
"X4 number of convenience stores": "Market Sayısı",
"Y house price of unit area": "Ev Fiyatı"})

import matplotlib.pyplot as plt
import seaborn as sns

sns.pairplot(veri)
plt.show()
```

![image](./images/polreg5.png)

```python
y=veri["Ev Fiyatı"]
X=veri.drop(columns="Ev Fiyatı", axis=1)

xcol = X.columns.to_list()

for i in xcol:
    sns.boxplot(X[i])
    plt.show()
```

![image](./images/polreg6.png) 

```python
Q1=X["Metro Uzaklık"].quantile(0.25)
Q3=X["Metro Uzaklık"].quantile(0.75)
IQR=Q3-Q1
ustsınır=Q3+1.5*IQR
aykırı = X["Metro Uzaklık"]>ustsınır

X.loc[aykırı, "Metro Uzaklık"]=ustsınır
sns.boxplot(X["Metro Uzaklık"])
plt.show()
```

![image](./images/polreg7.png)

```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import Polynomial Features
import sklearn.metrics as mt

pol=Polynomial Features (degree=3)
X_pol=pol.fit_transform(X)

X_train,X_test,y_train,y_test=train_test_split(X_pol, y, test_size=0.2, random_state=42)

pol_reg=LinearRegression()
pol_reg.fit(X_train,y_train)
tahmin=pol_reg.predict(X_test)
r2=mt.r2_score (y_test, tahmin)
Mse=mt.mean_squared_error(y_test, tahmin)
print("R2: {} MSE: {}".format(r2, Mse))
# R2: 0.7084828465358102 MSE: 48.904843188422916 3 ten sonra düştü yine

plt.scatter(X_pol, y, color="red")
plt.plot(X, tahmin, color="blue")
plt.show()
```

# Ridge Regresyon

Ridge regresyon, bağımsız değişkenlerin yüksek oranda korelasyon gösterdiği
senaryolarda çoklu regresyon modellerinin katsayılarını tahmin etme yöntemidir.

$$
\hat{\beta} = (X^T X + \lambda I)^{-1} X^T y
$$

Burada:

* $\hat{\beta}$: Regresyon katsayıları (beta değerleri)
* $X$: Bağımsız değişkenlerin (girdi özellikleri) matrisi
* $y$: Bağımlı değişken (hedef) vektörü
* $\lambda$: Regülerizasyon parametresi (pozitif bir değer)
* $I$: Birim matris (özellikle $X^T X$'in tersini alırken düzenlemeyi sağlar)
* $X^T$: $X$ matrisinin transpozu

- Aşırı öğrenmeye dirençli bir yapıdır. 
- Çoklu doğrusal bağlantı veya aykırı gözlem değerlerine dirençli bir yapıdır.
- Anlamsız değerleri kaldırmaz, katsayılarını sıfıra yakın bir değer belirleyerek etkilerini azaltır.

## Örnekler

```Python
import pandas as pd 

data = pd.DataFrame(
    {

        "TV":[230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8.6, 199.8, 66.1, 214.7, 23.8, 97.5, 204.1, 195.4, 67.8, 281.4, 69.2, 147.3, 218.4, 237.4, 13.2, 228.3, 62.3, 262.9, 142.9, 240.1, 248.8, 70.6, 292.9, 112.9, 97.2, 265.6, 95.7, 290.7, 266.9, 74.7, 43.1, 228, 202.5, 177, 293.6, 206.9, 25.1, 175.1, 89.7, 239.9, 227.2, 66.9, 199.8, 100.4, 216.4, 182.6, 262.7, 198.9, 7.3, 136.2, 210.8, 210.7, 53.5, 261.3, 239.3, 102.7, 131.1, 69, 31.5, 139.3, 237.4, 216.8, 199.1, 109.8, 26.8, 129.4, 213.4, 16.9, 27.5, 120.5, 5.4, 116, 76.4, 239.8, 75.3, 68.4, 213.5, 193.2, 76.3, 110.7, 88.3, 109.8, 134.3, 28.6, 217.7, 250.9, 107.4, 163.3, 197.6, 184.9, 289.7, 135.2, 222.4, 296.4, 280.2, 187.9, 238.2, 137.9, 25, 90.4, 13.1, 255.4, 225.8, 241.7, 175.7, 209.6, 78.2, 75.1, 139.2, 76.4, 125.7, 19.4, 141.3, 18.8, 224, 123.1, 229.5, 87.2, 7.8, 80.2, 220.3, 59.6, 0.7, 265.2, 8.4, 219.8, 36.9, 48.3, 25.6, 273.7, 43, 184.9, 73.4, 193.7, 220.5, 104.6, 96.2, 140.3, 240.1, 243.2, 38, 44.7, 280.7, 121, 197.6, 171.3, 187.8, 4.1, 93.9, 149.8, 11.7, 131.7, 172.5, 85.7, 188.4, 163.5, 117.2, 234.5, 17.9, 206.8, 215.4, 284.3, 50, 164.5, 19.6, 168.4, 222.4, 276.9, 248.4, 170.2, 276.7, 165.6, 156.6, 218.5, 56.2, 287.6, 253.8, 205, 139.5, 191.1, 286, 18.7, 39.5, 75.5, 17.2, 166.8, 149.7, 38.2, 94.2, 177, 283.6, 232.1],
        "Radio":[37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1, 2.6, 5.8, 24, 35.1, 7.6, 32.9, 47.7, 36.6, 39.6, 20.5, 23.9, 27.7, 5.1, 15.9, 16.9, 12.6, 3.5, 29.3, 16.7, 27.1, 16, 28.3, 17.4, 1.5, 20, 1.4, 4.1, 43.8, 49.4, 26.7, 37.7, 22.3, 33.4, 27.7, 8.4, 25.7, 22.5, 9.9, 41.5, 15.8, 11.7, 3.1, 9.6, 41.7, 46.2, 28.8, 49.4, 28.1, 19.2, 49.6, 29.5, 2, 42.7, 15.5, 29.6, 42.8, 9.3, 24.6, 14.5, 27.5, 43.9, 30.6, 14.3, 33, 5.7, 24.6, 43.7, 1.6, 28.5, 29.9, 7.7, 26.7, 4.1, 20.3, 44.5, 43, 18.4, 27.5, 40.6, 25.5, 47.8, 4.9, 1.5, 33.5, 36.5, 14, 31.6, 3.5, 21, 42.3, 41.7, 4.3, 36.3, 10.1, 17.2, 34.3, 46.4, 11, 0.3, 0.4, 26.9, 8.2, 38, 15.4, 20.6, 46.8, 35, 14.3, 0.8, 36.9, 16, 26.8, 21.7, 2.4, 34.6, 32.3, 11.8, 38.9, 0, 49, 12, 39.6, 2.9, 27.2, 33.5, 38.6, 47, 39, 28.9, 25.9, 43.9, 17, 35.4, 33.2, 5.7, 14.8, 1.9, 7.3, 49, 40.3, 25.8, 13.9, 8.4, 23.3, 39.7, 21.1, 11.6, 43.5, 1.3, 36.9, 18.4, 18.1, 35.8, 18.1, 36.8, 14.7, 3.4, 37.6, 5.2, 23.6, 10.6, 11.6, 20.9, 20.1, 7.1, 3.4, 48.9, 30.2, 7.8, 2.3, 10, 2.6, 5.4, 5.7, 43, 21.3, 45.1, 2.1, 28.7, 13.9, 12.1, 41.1, 10.8, 4.1, 42, 35.6, 3.7, 4.9, 9.3, 42, 8.6],
        "Newspaper":[69.2, 45.1, 69.3, 58.5, 58.4, 75, 23.5, 11.6, 1, 21.2, 24.2, 4, 65.9, 7.2, 46, 52.9, 114, 55.8, 18.3, 19.1, 53.4, 23.5, 49.6, 26.2, 18.3, 19.5, 12.6, 22.9, 22.9, 40.8, 43.2, 38.6, 30, 0.3, 7.4, 8.5, 5, 45.7, 35.1, 32, 31.6, 38.7, 1.8, 26.4, 43.3, 31.5, 35.7, 18.5, 49.9, 36.8, 34.6, 3.6, 39.6, 58.7, 15.9, 60, 41.4, 16.6, 37.7, 9.3, 21.4, 54.7, 27.3, 8.4, 28.9, 0.9, 2.2, 10.2, 11, 27.2, 38.7, 31.7, 19.3, 31.3, 13.1, 89.4, 20.7, 14.2, 9.4, 23.1, 22.3, 36.9, 32.5, 35.6, 33.8, 65.7, 16, 63.2, 73.4, 51.4, 9.3, 33, 59, 72.3, 10.9, 52.9, 5.9, 22, 51.2, 45.9, 49.8, 100.9, 21.4, 17.9, 5.3, 59, 29.7, 23.2, 25.6, 5.5, 56.5, 23.2, 2.4, 10.7, 34.5, 52.7, 25.6, 14.8, 79.2, 22.3, 46.2, 50.4, 15.6, 12.4, 74.2, 25.9, 50.6, 9.2, 3.2, 43.1, 8.7, 43, 2.1, 45.1, 65.6, 8.5, 9.3, 59.7, 20.5, 1.7, 12.9, 75.6, 37.9, 34.4, 38.9, 9, 8.7, 44.3, 11.9, 20.6, 37, 48.7, 14.2, 37.7, 9.5, 5.7, 50.5, 24.3, 45.2, 34.6, 30.7, 49.3, 25.6, 7.4, 5.4, 84.8, 21.6, 19.4, 57.6, 6.4, 18.4, 47.4, 17, 12.8, 13.1, 41.8, 20.3, 35.2, 23.7, 17.6, 8.3, 27.4, 29.7, 71.8, 30, 19.6, 26.6, 18.2, 3.7, 23.4, 5.8, 6, 31.6, 3.6, 6, 13.8, 8.1, 6.4, 66.2, 8.7],
        "Sales":[22.1, 10.4, 12, 16.5, 17.9, 7.2, 11.8, 13.2, 4.8, 15.6, 12.6, 17.4, 9.2, 13.7, 19, 22.4, 12.5, 24.4, 11.3, 14.6, 18, 17.5, 5.6, 20.5, 9.7, 17, 15, 20.9, 18.9, 10.5, 21.4, 11.9, 13.2, 17.4, 11.9, 17.8, 25.4, 14.7, 10.1, 21.5, 16.6, 17.1, 20.7, 17.9, 8.5, 16.1, 10.6, 23.2, 19.8, 9.7, 16.4, 10.7, 22.6, 21.2, 20.2, 23.7, 5.5, 13.2, 23.8, 18.4, 8.1, 24.2, 20.7, 14, 16, 11.3, 11, 13.4, 18.9, 22.3, 18.3, 12.4, 8.8, 11, 17, 8.7, 6.9, 14.2, 5.3, 11, 11.8, 17.3, 11.3, 13.6, 21.7, 20.2, 12, 16, 12.9, 16.7, 14, 7.3, 19.4, 22.2, 11.5, 16.9, 16.7, 20.5, 25.4, 17.2, 16.7, 23.8, 19.8, 19.7, 20.7, 15, 7.2, 12, 5.3, 19.8, 18.4, 21.8, 17.1, 20.9, 14.6, 12.6, 12.2, 9.4, 15.9, 6.6, 15.5, 7, 16.6, 15.2, 19.7, 10.6, 6.6, 11.9, 24.7, 9.7, 1.6, 17.7, 5.7, 19.6, 10.8, 11.6, 9.5, 20.8, 9.6, 20.7, 10.9, 19.2, 20.1, 10.4, 12.3, 10.3, 18.2, 25.4, 10.9, 10.1, 16.1, 11.6, 16.6, 16, 20.6, 3.2, 15.3, 10.1, 7.3, 12.9, 16.4, 13.3, 19.9, 18, 11.9, 16.9, 8, 17.2, 17.1, 20, 8.4, 17.5, 7.6, 16.7, 16.5, 27, 20.2, 16.7, 16.8, 17.6, 15.5, 17.2, 8.7, 26.2, 17.6, 22.6, 10.3, 17.3, 20.9, 6.7, 10.8, 11.9, 5.9, 19.6, 17.3, 7.6, 14, 14.8, 25.5, 18.4]
  
    }
)

veri=data.copy()

y=veri["Sales"]
X=veri.drop(columns="Sales", axis = 1)

from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression, Ridge
import sklearn.metrics as mt

X_train, X_test, y_train, y_test=train_test_split(X,y, test_size=0.2, random_state=42)

lr=LinearRegression()
lr.fit(X_train, y_train)

tahmin=lr.predict(X_test)

r2=mt.r2_score(y_test, tahmin)
mse=mt.mean_squared_error(y_test, tahmin)
print("R2: {} MSE: {}".format(r2,mse)) # R2: 0.9059011844150826 MSE: 2.907756910271091

ridge_model=Ridge(alpha=0.1)
ridge_model.fit(X_train,y_train)

tahmin2=ridge_model.predict(X_test)

r2rid=mt.r2_score (y_test, tahmin2)
mserid=mt.mean_squared_error(y_test, tahmin2)

print("R2 {} MSE: {}".format(r2rid, mserid)) # R2 0.9059010575706533 MSE: 2.9077608299034856

import matplotlib.pyplot as plt
import numpy as np

katsayılar=[]
lambdalar=10*np.linspace(10,-2,-100)*0.5

for i in lambdalar:
    ridmodel=Ridge(alpha=i)
    ridmodel.fit(X_train, y_train)
    katsayılar.append(ridmodel.coef_)

ax=plt.gca()
ax.plot(katsayılar, lambdalar)
ax.set_xscale("log")

plt.xlabel("Lambda")
plt.ylabel("Katsayılar")
plt.show()
```

![image](./images/rdgreg1.png)

Lambda değeri arttıkça katsayılar 0 a yakınsar.

```Python
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sns

data = pd.DataFrame(
    {
        "x1": [1948, 2261, 1989, 1999, 2086, 1717, 1383, 1470, 1350, 1602, 1417, 1662, 1955, 1974, 2094, 3149, 1471, 1691, 2373], 
        "x2": [4177, 3670, 4353, 3307, 4230, 2714, 2357, 3004, 2446, 4188, 3631, 4683, 4553, 3836, 4183, 6128, 2952, 3711, 4836],
        "x3": [5496, 7797, 7392, 7061, 6564, 5919, 5053, 3951, 4280, 5910, 5145, 6384, 5679, 6021, 6733, 8151, 4151, 4200, 6704],
        "x4": [2922, 3327, 2837, 3439, 2987, 3394, 2958, 2691, 2397, 3619, 3282, 6376, 6141, 5646, 6720, 9048, 4975, 4962, 6563],
        "x5": [5713, 6934, 6275, 6641, 6675, 5605, 5144, 5116, 4722, 6869, 5226, 7313, 6068, 5876, 6044, 8384, 5149, 5359, 6197],
        "x6": [3640, 4424, 4827, 4815, 3959, 3648, 3106, 3557, 3556, 5142, 3793, 4679, 3651, 4026, 6573, 7467, 4733, 3782, 5001],
        "x7": [3203, 3692, 4476, 4256, 3900, 3085, 4052, 2775, 2818, 3190, 2663, 3037, 2601, 3037, 2465, 2888, 2603, 3185, 3902],
        "x8": [2739, 3451, 4403, 4129, 3559, 2440, 3006, 1909, 2945, 3660, 3017, 3666, 2791, 3920, 3406, 3522, 3493, 3099, 3685],
        "x9": [2167, 2866, 3568, 3447, 4078, 2631, 3049, 1952, 2931, 3964, 2579, 3142, 2148, 2583, 2410, 2496, 3396, 3381, 3636],
        "x10": [2299, 2653, 3241, 3046, 3583, 2587, 2890, 1723, 2733, 3107, 3367, 2621, 2448, 2742, 2539, 2895, 3554, 2938, 3365],
        "y": [3255.2, 3682.7, 3921.9, 3909.3, 3768.9, 3106.4, 3069.4, 2940.2, 3015.3, 3953.2, 3172.4, 3791.0, 3344.8, 3650.3, 3878.7, 4606.2, 3498.8, 3241.0, 4013.5]
    }
)

veri = data.copy()

sns.heatmap(veri.corr(), annot=True)
plt.show()
```

![image](./images/rdgreg2.png) 

```Python
y = veri["y"]
X = veri.drop(columns="y", axis=1)

from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression, Ridge, RidgeCV
import sklearn.metrics as mt

LinearRegression()
lr.fit(X_train, y_train)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

tahmin = lr.predict(X_test)

r2 = mt.r2_score(y_test, tahmin)
print("R2: {}".format(r2)) # R2: 0.8980834625627182

ridge_model = Ridge(alpha=0)
ridge_model.fit(X_train, y_train)
tahmin2 = ridge_model.predict(X_test)

r2rid = mt.r2_score(y_test, tahmin2)
print("R2 Rid: {}".format(r2rid)) # R2 Rid: 0.8980834625627178

import numpy as np

lambdalar = 10**np.linspace(10, -2, 100) * 0.5

ridge_cv = RidgeCV(alphas=lambdalar, scoring="r2")
ridge_cv.fit(X_train, y_train)
print(ridge_cv.alpha_) # 2018508.6292982749

ridge_model = Ridge(alpha=2018508.6292982749)
ridge_model.fit(X_train, y_train)
tahmin2 = ridge_model.predict(X_test)

r2rid = mt.r2_score(y_test, tahmin2)
print("R2 Rid: {}".format(r2rid)) # R2 Rid: 0.8584004563372055
```

Çoklu doğrusal bağlantının etkilerinin giderildiğinde  R2 değeri 0.89 değerinden 0.85 değerine düştü.

# Lasso Regresyon

Ridge regresyon anlamsız değişkenleri sıfıra yakınlaştırırken Lasso katsayıyı sıfır yaparak modelden dışlar. 

```Python
import pandas as pd
from sklearn.datasets import load_boston

df = load_boston()

data = pd.DataFrame(df.data, columns=df.feature_names)

veri=data.copy()

veri["PRICE"] = df.target

y = veri["PRICE"]
X = veri.drop(columns="PRICE", axis=1)

from sklearn.model_selection import train_test_split
from sklearn.linear_model import Ridge,Lasso,LassoCV
import sklearn.metrics as mt

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

ridge_model = Ridge(alpha=0.1)
ridge_model.fit(X_train, y_train)
tahmin = ridge_model.predict(X_test)

print(ridge_model.score(X_train, y_train))
print(ridge_model.score(X_test, y_test))
print(mt.r2_score(y_test, tahmin))
# 0.750827350977196
# 0.6686244122021412
# 0.6686244122021412

lasso_model=Lasso (alpha=0.1)
lasso_model.fit(X_train,y_train)

print(lasso_model.score (X_train, y_train))
print(lasso_model.score(X_test,y_test))

# 0.7382419735910875
# 0.6569712802223936

print(ridge_model.coef_)
print(lasso_model.coef_)

[-1.12399694e-01
3.04593914e-02
3.48958400e-02
2.75033318e+00
-1.59244585e+01
4.44577949e+00
-7.30474388e-03
-1.42960751e+00
2.60042840e-01-1.07802286e-02
-9.00771040e-01
1.24004789e-02
-5.10902332e-01]

[-0.10415691
0.03489335
-0.01678527
0.91995182
-0
4.31168655
-0.01512583
-1.15148729
0.23923695
-0.01296223
-0.73224678
0.01309057
-0.56467442]

lamb=LassoCV(cv = 10 max_iter=10000).fit(X_train,y_train).alpha_

lasso_model2=Lasso(alpha=lamb)
lasso_model2.fit(X_train,y_train)

print(lasso_model2.score (X_train, y_train))
print(lasso_model2.score (X_test,y_test))
# 0.7157406210167571
# 0.6706431115795963

```

Görüldüğü gibi cv ile arttırılmıştır.

# ElasticNet Regresyon

Ridge ve Lasso modellerinin karışımıdır.

```Python
import pandas as pd
from sklearn.datasets import load_boston
from sklearn.model_selection import train_test_split
from sklearn.linear_model import Ridge, Lasso, ElasticNet

df=load_boston()
data=pd.DataFrame(df.data, columns=df.feature_names)
veri=data.copy()

veri["PRICE"]=df.target

y=veri ["PRICE"]
X=veri.drop(columns="PRICE", axis=1)

X_train, X_test, y_train, y_test=train_test_split(x,y, test_size=0.2, random_state=42)

ridge_model=Ridge (alpha=0.1)
ridge_model.fit(X_train,y_train)

lasso_model=Lasso (alpha=0.1)
lasso_model.fit(X_train,y_train)

elas_model=ElasticNet (alpha=0.1)
elas_model.fit(X_train, y_train)

print(ridge_model.score(X_train,y_train)) 
print(lasso_model.score(X_train,y_train))
print(elas_model.score(X_train,y_train))

print(ridge_model.score(X_test,y_test)) 
print(lasso_model.score(X_test,y_test))
print(elas_model.score(X_test,y_test))

# 0.750827350977196
# 0.7382419735910875
# 0.7365251888690172

# 0.6686244122021412
# 0.6569712802223936
# 0.6667328308555568

import sklearn.metrics as mt

tahminrid=ridge_model.predict(X_test)
tahminlasso=lasso_model.predict(X_test)
tahminelas=elas_model.predict(X_test)

print(mt.mean_squared_error(y_test, tahminrid))
print(mt.mean_squared_error(y_test, tahminlasso))
print(mt.mean_squared_error(y_test, tahminelas))

# 24.301025500192736
# 25.155593753934173
# 24.43974231649327

lamb=ElasticNetCV(cv=10,max_iter=10000).fit(X_train,y_train).alpha_

elas_model2=ElasticNet(alpha=lamb)
elas_model2.fit(X_train,y_train)

print(elas_model2.score(X_train, y_train))
print(elas_model2.score(X_test,y_test))

tahminelas2=elas_model2.predict(X_test)
print(mt.mean_squared_error(y_test, tahminelas2))

# 0.6751044230261126
# 0.6651210319978071
# 24.55794162442554
```

# Doğrusal Regresyon Örnek

dataset: https://www.kaggle.com/datasets/vedavyasv/usa-housing

```Python
import pandas as pd

df = pd.read_csv("ev.csv")

veri=data.copy()

veri=veri.drop(columns="Address", axis=1)

import matplotlib.pyplot as plt
import seaborn as sns

sns.pairplot(veri)
plt.show()
```

![image](./images/dgrorn1.png) 
 
```Python
kor=veri.corr()
sns.heatmap(kor, annot=True)
plt.show()
```

![image](./images/dgrorn2.png) 

```Python
import statsmodels.api as sm
from statsmodels.stats.outliers_influence import variance_inflation_factor

y=veri["Price"]
X=veri.drop(columns="Price", axis=1)

sabit=sm.add_constant(X)

vif=pd.DataFrame()
vif["Değişkenler"]=X.columns
vif["VIF"]=[variance_inflation_factor(sabit, i+1) for i in range(X.shape[1])]
```

| Değişkenler                      |    VIF    |
|----------------------------------|-----------|
| Avg. Area Income                 | 1.001159  |
| Avg. Area House Age              | 1.000577  |
| Avg. Area Number of Rooms        | 1.273535  |
| Avg. Area Number of Bedrooms     | 1.274413  |
| Area Population                  | 1.001266  |

Bütün değerler 5'ten küçük. Çoklu bağlantı sorunu gözükmüyor.

```Python
from sklearn.model_selection import train_test_split, cross_val_score
import sklearn.metrics as mt

X_train, X_test,y_train, y_test=train_test_split(X,y,test_size=0.2,random_state=42)

def caprazdog(model):
    dogruluk=cross_val_score (model, X, y, cv=10)
    return dogruluk.mean()

def basarı (gercek, tahmin):
    rmse=mt.mean_squared_error(gercek, tahmin, squared=True)
    r2=mt.r2_score (gercek, tahmin)
    return [rmse,r2]

from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet

lin_model=LinearRegression()
lin_model.fit(X_train,y_train)
lin_tahmin=lin_model.predict(X_test)

ridge_model=Ridge (alpha=0.1)
ridge_model.fit(X_train,y_train)
ridge_tahmin=ridge_model.predict(X_test)

lasso_model=Lasso (alpha=0.1)
lasso_model.fit(X_train,y_train)
lasso_tahmin=lasso_model.predict(X_test)

elas_model=Lasso (alpha=0.1)
elas_model.fit(X_train,y_train)
elas_tahmin=elas_model.predict(X_test)

sonuclar=[
    ["Linear Model", basarı(y_test, lin_tahmin)[0], basarı(y_test, lin_tahmin)[1], caprazdog(lin_model)],
    ["Ridge Model", basarı(y_test, ridge_tahmin)[0], basarı(y_test, ridge_tahmin)[1], caprazdog(ridge_model)],
    ["Lasso Model", basarı(y_test, lasso_tahmin)[0], basarı(y_test, lasso_tahmin)[1], caprazdog(lasso_model)],
    ["Elastic Net Model", basarı(y_test, elas_tahmin)[0], basarı(y_test, elas_tahmin)[1], caprazdog(elas_model)]
]

sonuclar = pd.DataFrame(sonuclar, columns=["Model", "RMSE", "R2", "Doğrulama"])
```

| Model             | RMSE      | R²       | Doğrulama |
| ----------------- | --------- | -------- | --------- |
| Linear Model      | 91.156062 | 0.898083 | 0.917379  |
| Ridge Model       | 91.156072 | 0.898083 | 0.917379  |
| Lasso Model       | 91.155747 | 0.898084 | 0.917379  |
| Elastic Net Model | 91.155939 | 0.898084 | 0.916542  |

En düşük RMSE değerine lineer model sahiptir.

# Temel Bileşenler Analizi

Bu analiz için önemli olan kavramlar;

- Öznitelik (Bağımsız değişken yapıları)
- Özdeğer
- Özvektör


Yalnızca sayıysa skaler, sütun gibi sıralı değerler vektör, bir kaç sütunun oluşturduğu yapı matris. Bir matrisi bir özvektör ile çarptığımızda aynı özvektörün ve skaler bir yapıda özdeğeri çıkması gerekir.

## Örnekler

dataset: https://www.kaggle.com/datasets/uciml/red-wine-quality-cortez-et-al-2009

```Python
import pandas as pd

data=pd.read_csv("winequality-red.csv")

veri=data.copy()

print(veri.isnull().sum())
# fixed acidity           0
# volatile acidity        0
# citric acid             0
# residual sugar          0
# chlorides               0
# free sulfur dioxide     0
# total sulfur dioxide    0
# density                 0
# pH                      0
# sulphates               0
# alcohol                 0
# quality                 0
# dtype: int64

veri.info()
# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 1599 entries, 0 to 1598
# Data columns (total 12 columns):
#  #   Column                Non-Null Count  Dtype  
# ---  ------                --------------  -----  
#  0   fixed acidity         1599 non-null   float64
#  1   volatile acidity      1599 non-null   float64
#  2   citric acid           1599 non-null   float64
#  3   residual sugar        1599 non-null   float64
#  4   chlorides             1599 non-null   float64
#  5   free sulfur dioxide   1599 non-null   float64
#  6   total sulfur dioxide  1599 non-null   float64
#  7   density               1599 non-null   float64
#  8   pH                    1599 non-null   float64
#  9   sulphates             1599 non-null   float64
#  10  alcohol               1599 non-null   float64
#  11  quality               1599 non-null   int64  
# dtypes: float64(11), int64(1)
# memory usage: 150.0 KB

import matplotlib.pyplot as plt
import seaborn as sns

sns.pairplot(veri)
plt.show()
```

![image](./images/pcaorn1.png)

Çok karmaşık bir yapı görülüyor.

```Python
kor=veri.corr()
sns.heatmap(kor, annot=True, cbar=True)
plt.show()
```

![image](./images/pcaorn2.png)

```Python
y=veri["quality"]
X=veri.drop(columns="quality",axis=1)

from sklearn.model_selection import train_test_split

X_train, x_test, y_train, y_test=train_test_split(X,y,test_size=0.2,random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.decomposition import PCA

pca=PCA()
X_train2=pca.fit_transform(X_train)
X_test=pca.transform(X_test) 

X_train2.shape # (1279, 11)
X_train.shape # (1279, 11)

# bağımsız değişken sayısı azalmamış

pca=PCA(n_components=2)

X_train2=pca.fit_transform(X_train) 
X_test=pca.transform(X_test)
print(X_train.shape) 
print(X_train2.shape)
# (1279, 11)
# (1279, 2) n_components e 2 yazınca değişken sayısını ikiye indirdi.

import numpy as np

pca = PCA()
X_train2 = pca.fit_transform(X_train)
X_test2 = pca.transform(X_test)

print(np.cumsum(pca.explained_variance_ratio_) * 100)
# [ 28.01769042  45.58168574  59.53932155  70.62114399  79.6423922  85.55109031  90.81771725  94.70160536  97.83107305  99.43207028 100.        ]

# Eğer 1 taneye indirgersek %28 2 taneye indirgersek %45 gibi.
```

![image](./images/pcaorn3.png)

```Python
from sklearn.linear_model import LinearRegression
import sklearn.metrics as mt

pca = PCA()
X_train2 = pca.fit_transform(X_train)
X_test2 = pca.transform(X_test)

print(np.cumsum(pca.explained_variance_ratio_) * 100)

lm = LinearRegression()
lm.fit(X_train2, y_train)
tahmin = lm.predict(X_test2)

r2 = mt.r2_score(y_test, tahmin)
rmse = mt.mean_squared_error(y_test, tahmin, squared=True)
print("R2: {}  RMSE: {}".format(r2, rmse))
# R2: 0.403180341279622  RMSE: 0.390025143639549

from sklearn.model_selection import KFold, cross_val_score

cv = KFold(n_splits=10, shuffle=True, random_state=1)

lm2 = LinearRegression()
RMSE = []

for i in range(1, X_train2.shape[1] + 1):
    hata = np.sqrt(-1 * cross_val_score(lm2, X_train2[:, :i], y_train.ravel(), cv=cv, scoring="neg_mean_squared_error").mean())
    RMSE.append(hata)

plt.plot(RMSE, "-x")
plt.xlabel("Bileşen Sayısı")
plt.ylabel("RMSE")
plt.show()
```

![image](./images/pcaorn4.png)

3te hata azaldığı için 3 ü kullanıyoruz.

# PCA Geometrisi

Temel bileşen analizi bir verinin en güzel şekilde ele alınabileceği açıyı bulmaya yarar. Doğru açıyı maksimum varyans sağlar. 

```python
import pandas as pd

data=pd.DataFrame(
    {
        "Y":[1, 2, 3, 8, 10, 11],
        "X":[1, 2.8, 3, 5, 6, 4]
    }
)

data.mean()

# Y    5.833333
# X    3.633333
# dtype: float64
```

Ortalamalarının kesiştiği noktayı orjin olarak belirliyoruz. Orijinden geçen doğrulardan hangisinin noktaların orijine olan uzaklıkları en yüksekse bu doğruya **1. bileşen yapısı (özvektör)** diyoruz. Bu mesafelerin karelerinin toplamına da **özdeğer** diyoruz. Mesela bulduğumuz açıya göre x 4 birim gittiğinde y 1 birim gitmelidir. pisagordan $1^2 + 4^2 = a^2$ bağlantısıyla $a=\sqrt{17}=4,12$ bulunur. a yı standartlaştırarak 1 e eşitlemeye çalışırız. a 1 olduğunda b 4 / 4.12'den 0,97 olur c 1 / 4,12 den 0,24 olur.

# LDA

Veri sınıfları arasındaki varyansı maksimize eden yöntemdir. Sınıflama yapısıdır.

dataset: https://www.kaggle.com/datasets/uciml/red-wine-quality-cortez-et-al-2009

```python
import pandas as pd

from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

data=pd.read_csv("C:/Usens/90506/Desktop/sarap.csv")
veri=data.copy()

y=veri["quality"] 
X=veri.drop(columns="quality",axis=1)

X_train,X_test,y_train,y_test=train_test_split(X,y,test_size=0.2,random_state=42)

sc=StandardScaler()
X_train=sc.fit_transform(x_train)
X_test=sc.transform(x_test)

from sklearn.discriminant_analysis import LinearDiscriminantAnalysis

lda=LineanDiscriminantAnalysis()

X_train2=lda.fit_transform(x_train,y_train)
X_test2=lda.transform(x_test)

import numpy as np

print(np.cumsum(lda.explained_variance_ratio_)*100)

[85.03158458 94.47170698 98.20710389 99.56831945 100.]
```

# Support Vector Regression

Hataları en aza indirmek yerine marjin aralığını en aza indiren doğruyu bularak çalışır.

![image](./images/svr1.png)

![image](./images/svr2.png)

EKK'ya göre daha dirençli bir yapıdır. Hatalara göre daha esnektir.

## Örnekler

dataset: https://www.kaggle.com/datasets/mariospirito/position-salariescsv

```Python
import pandas as pd

data=pd.DataFrame({
    "Position":["Business Analyst", "Junior Consultant", "Senior Consultant", "Manager", "Country Manager", "Region Manager", "Partner", "Senior Partner", "C-level", "CEO"],
    "Level":[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    "Salary":[45000, 50000, 60000, 80000, 110000, 150000, 200000, 300000, 500000, 1000000]
})

veri = data.copy()

# Data küçük olduğu için eğitim ve test olarak ayırmayacağız.

y=veri["Salary"]
X=veri["Level"]

# Eğitim test olarak ayrılmadığı için veriler dataframe formatında. Ama array olmadan modele sokamayız. O yüzden aşağıda array a çevireceğiz.

import numpy as np

y = np.array(y).reshape(-1, 1)
X = np.array(X).reshape(-1, 1)

from sklearn.preprocessing import StandardScaler

scx = StandardScaler()
scy = StandardScaler()

y = scy.fit_transform(y)
X = scx.fit_transform(X)

from sklearn.svm import SVR

svrmodel = SVR()
svrmodel.fit(X, y)
tahmin = svrmodel.predict(X)

import matplotlib.pyplot as plt

plt.scatter(X, y, color="red")
plt.show()
```

![image](./images/svr3.png)

```Python
plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()
```

![image](./images/svr4.png) 

```Python
svrmodel = SVR(kernel="linear")
svrmodel.fit(X, y)
tahmin = svrmodel.predict(X)

plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()
```

![image](./images/svr5.png) 

```Python
svrmodel = SVR(kernel="poly")
svrmodel.fit(X, y)
tahmin = svrmodel.predict(X)

plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()
```

![image](./images/svr6.png) 

```Python
svrmodel = SVR(kernel="poly", degree=2)
svrmodel.fit(X, y)
tahmin = svrmodel.predict(X)

plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()
```

![image](./images/svr7.png) 

```Python
svrmodel = SVR(kernel="rbf")
svrmodel.fit(X, y)
tahmin = svrmodel.predict(X)

plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()
```

![image](./images/svr8.png)

---

```Python
import yfinance as yf

data = yf.download("THYAO.IS", start="2022-08-01", end="2022-09-01")
veri = data.copy()
veri = veri.reset_index()
print(veri)
```
| Date       | Open  | High  | Low   | Close | Adj Close | Volume      |
| ---------- | ----- | ----- | ----- | ----- | --------- | ----------- |
| 2022-08-01 | 50.75 | 51.60 | 50.65 | 51.50 | 50.83     | 71,659,508  |
| 2022-08-02 | 51.80 | 53.20 | 51.65 | 52.35 | 51.67     | 137,217,705 |
| 2022-08-03 | 52.95 | 55.00 | 52.80 | 55.00 | 54.28     | 133,031,485 |
| 2022-08-04 | 55.70 | 56.35 | 55.10 | 55.30 | 54.58     | 108,646,850 |
| 2022-08-05 | 55.65 | 58.40 | 55.20 | 58.20 | 57.44     | 99,647,394  |
| 2022-08-08 | 58.40 | 59.00 | 57.60 | 58.75 | 57.98     | 87,925,247  |
| 2022-08-09 | 58.75 | 59.65 | 57.90 | 58.60 | 57.84     | 102,076,610 |
| 2022-08-10 | 58.80 | 60.35 | 58.60 | 60.15 | 59.37     | 92,599,249  |
| 2022-08-11 | 61.90 | 62.45 | 60.10 | 61.40 | 60.60     | 136,866,004 |
| 2022-08-12 | 61.85 | 63.35 | 61.25 | 62.40 | 61.59     | 101,328,664 |
| 2022-08-15 | 62.60 | 63.65 | 62.10 | 63.60 | 62.77     | 84,075,886  |
| 2022-08-16 | 63.70 | 68.05 | 61.70 | 67.85 | 66.96     | 144,211,535 |
| 2022-08-17 | 68.00 | 69.10 | 66.00 | 68.70 | 67.80     | 140,910,417 |
| 2022-08-18 | 68.80 | 70.05 | 66.80 | 69.65 | 68.74     | 99,352,988  |
| 2022-08-19 | 69.55 | 70.20 | 68.15 | 68.50 | 67.61     | 78,852,077  |
| 2022-08-22 | 68.50 | 69.35 | 67.50 | 67.75 | 66.87     | 76,273,653  |
| 2022-08-23 | 67.80 | 69.05 | 67.00 | 67.80 | 66.92     | 77,832,215  |
| 2022-08-24 | 68.20 | 71.70 | 68.10 | 68.65 | 67.75     | 158,320,953 |
| 2022-08-25 | 69.30 | 71.00 | 68.80 | 70.30 | 69.38     | 100,888,883 |
| 2022-08-26 | 70.90 | 71.15 | 67.40 | 69.70 | 68.79     | 93,635,673  |
| 2022-08-29 | 69.40 | 70.85 | 68.50 | 70.45 | 69.53     | 86,908,068  |
| 2022-08-31 | 71.00 | 73.40 | 70.70 | 73.10 | 72.15     | 105,936,916 |

```Python
import pandas as pd

data=pd.DataFrame(
   {
    "Date": ["2022-08-01", "2022-08-02", "2022-08-03", "2022-08-04", "2022-08-05", "2022-08-08", "2022-08-09", "2022-08-10", "2022-08-11", "2022-08-12", "2022-08-15", "2022-08-16", "2022-08-17", "2022-08-18", "2022-08-19", "2022-08-22", "2022-08-23", "2022-08-24", "2022-08-25", "2022-08-26", "2022-08-29", "2022-08-31"],
    "Open":[50.75, 51.80, 52.95, 55.70, 55.65, 58.40, 58.75, 58.80, 61.90, 61.85, 62.60, 63.70, 68.00, 68.80, 69.55, 68.50, 67.80, 68.20, 69.30, 70.90, 69.40, 71.00],
    "High":[51.60, 53.20, 55.00, 56.35, 58.40, 59.00, 59.65, 60.35, 62.45, 63.35, 63.65, 68.05, 69.10, 70.05, 70.20, 69.35, 69.05, 71.70, 71.00, 71.15, 70.85, 73.40],
    "Low":[50.65, 51.65, 52.80, 55.10, 55.20, 57.60, 57.90, 58.60, 60.10, 61.25, 62.10, 61.70, 66.00, 66.80, 68.15, 67.50, 67.00, 68.10, 68.80, 67.40, 68.50, 70.70],
    "Close":[51.50, 52.35, 55.00, 55.30, 58.20, 58.75, 58.60, 60.15, 61.40, 62.40, 63.60, 67.85, 68.70, 69.65, 68.50, 67.75, 67.80, 68.65, 70.30, 69.70, 70.45, 73.10],
    "Adj Close":[50.83, 51.67, 54.28, 54.58, 57.44, 57.98, 57.84, 59.37, 60.60, 61.59, 62.77, 66.96, 67.80, 68.74, 67.61, 66.87, 66.92, 67.75, 69.38, 68.79, 69.53, 72.15],
    "Volume":[71659508, 137217705, 133031485, 108646850, 99647394, 87925247, 102076610, 92599249, 136866004, 101328664, 84075886, 144211535, 140910417, 99352988, 78852077, 76273653, 77832215, 158320953, 100888883, 93635673, 86908068, 105936916]
   } 
)

veri=data.copy()

veri["Day"] = veri["Date"].astype(str).str.split("-").str[2]

y = veri["Adj Close"]
X = veri["Day"]

y = np.array(y).reshape(-1, 1)
X = np.array(X).reshape(-1, 1)

from sklearn.preprocessing import StandardScaler

scy = StandardScaler()
scx = StandardScaler()

X = scx.fit_transform(X)
y = scy.fit_transform(y)

from sklearn.svm import SVR

# RBF kernel
svr_rbf = SVR(kernel="rbf")
svr_rbf.fit(X, y)
tahminrbf = svr_rbf.predict(X)

# Linear kernel
svr_lin = SVR(kernel="linear")
svr_lin.fit(X, y)
tahminlin = svr_lin.predict(X)

# Poly kernel
svrpoly=SVR(kernel="poly")
svrpoly.fit(X,y)
tahminpoly=svrpoly.predict(X)

import matplotlib.pyplot as plt

plt.scatter(X, y, color="red")
plt.plot(X, tahminrbf, color="green", label="RBF Model")
plt.plot(X, tahminlin, color="blue", label="Linear Model")
plt.plot(X, tahminpoly, color="black", label="Poly Model")
plt.legend()
plt.show()
```

![image](./images/svr9.png)

Modele en uygun olan kernel rbf'tir.

```Python
import sklearn.metrics as mt

r2 = mt.r2_score(y, tahminrbf)
rmse = np.sqrt(mt.mean_squared_error(y, tahminrbf))

print("R2: {}   RMSE: {}".format(r2, rmse))
# R2: 0.951723235923062   RMSE: 0.21971973984359727

svrrbf = SVR(kernel="rbf", C=10000)
svrrbf.fit(X, y)
tahminrbf = svrrbf.predict(X)

plt.scatter(X, y, color="red")
plt.plot(X, tahminrbf, color="green", label="RBF Model")
plt.show()
```

![image](./images/svr10.png)

```Python
r2 = mt.r2_score(y, tahminrbf)
rmse = np.sqrt(mt.mean_squared_error(y, tahminrbf))

print("R2: {}   RMSE: {}".format(r2, rmse))
# R2: 0.9862387371531867   RMSE: 0.11730840910528659
```

```Python
svrrbf = SVR()
svrrbf.fit(X, y)
tahminrbf = svrrbf.predict(X)

from sklearn.model_selection import GridSearchCV

parametreler = {
    "C": [1, 10, 100, 1000, 10000],
    "gamma": [1, 0.1, 0.001],
    "kernel": ["rbf", "linear", "poly"]
}

tuning = GridSearchCV(estimator=SVR(), param_grid=parametreler, cv=10)

tuning.fit(X, y)

print(tuning.best_params_) # {'C': 100, 'gamma': 1, 'kernel': 'rbf'}

svrrbf = SVR(kernel="rbf", C=100, gamma=1)
svrrbf.fit(X, y)
tahminrbf = svrrbf.predict(X)

r2 = mt.r2_score(y, tahminrbf)
rmse = np.sqrt(mt.mean_squared_error(y, tahminrbf))

print("R2: {}   RMSE: {}".format(r2, rmse)) # R2: 0.9829947573169796   RMSE: 0.13040415132586977
```

R2 0,95'ten 0,98'e çıkarken RMSE 0,21'den 0,13'e düştü.

![image](./images/svr11.png) 

# Karar Ağacı Regresyonu

```python
import pandas as pd

data=pd.DataFrame({
    "Position":["Business Analyst", "Junior Consultant", "Senior Consultant", "Manager", "Country Manager", "Region Manager", "Partner", "Senior Partner", "C-level", "CEO"],
    "Level":[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    "Salary":[45000, 50000, 60000, 80000, 110000, 150000, 200000, 300000, 500000, 1000000]
})

veri = data.copy()

y=veri["Salary"]
X=veri["Level"]

# Eğitim test olarak ayrılmadığı için veriler dataframe formatında. Ama array olmadan modele sokamayız. O yüzden aşağıda array a çevireceğiz.

import numpy as np

y = np.array(y).reshape(-1, 1)
X = np.array(X).reshape(-1, 1)

from sklearn.preprocessing import StandardScaler

scx = StandardScaler()
scy = StandardScaler()

y = scy.fit_transform(y)
X = scx.fit_transform(X)

from sklearn.tree import DecisionTreeRegressor

dtr = DecisionTreeRegressor(random_state=0)
dtr.fit(X, y)
tahmin = dtr.predict(X)

import matplotlib.pyplot as plt

plt.scatter(X, y, color="red")
plt.plot(X, tahmin)
plt.show()

```

![image](./images/kar1.png) 

Aşırı uyum gözükmektedir. Burada makina ezberlemiş, öğrenmemiştir.

```python
from sklearn.tree import plot_tree

dtr = DecisionTreeRegressor(random_state=0, max_leaf_nodes=3)
dtr.fit(X, y)
tahmin = dtr.predict(X)

plt.figure(figsize=(20,10), dpi=100)
plot_tree(dtr, feature_names="Level", class_names="Salary", rounded=True, filled=True)
plt.show()
```

![image](./images/kar2.png) 

---

Dataset:https://raw.githubusercontent.com/mk-gurucharan/Regression/master/IceCreamData.csv

```Python
import pandas as pd

data=pd.read_csv("IceCreamData.csv")

veri=data.copy()

y=veri["Revenue"]
X=veri["Temperature"]

from sklearn.model_selection import train_test_split

X_train,X_test,y_train,y_test=train_test_split(X,y,test_size=0.1,random_state=42)

from sklearn.tree import DecisionTreeRegressor

model=DecisionTreeRegressor(random_state=0)
model.fit(X_train.values.reshape(-1,1),y_train.values.reshape(-1,1))
tahmin=model.predict(X_test.values.reshape(-1,1))

import sklearn.metrics as mt

r2=mt.r2_score(y_test, tahmin) 
mse=mt.mean_squared_error(y_test, tahmin)
print("R2: {} MSE: {}".format(r2,mse)) #R2: 0.96447587862359 MSE: 1333.2485215987253

model=DecisionTreeRegressor(random_state=0,max_leaf_nodes=20) # max leaf eklendi.
model.fit(X_train.values.reshape(-1,1),y_train.values.reshape(-1,1))
tahmin=model.predict(X_test.values.reshape(-1,1))

r2=mt.r2_score(y_test, tahmin) 
mse=mt.mean_squared_error(y_test, tahmin)
print("R2: {} MSE: {}".format(r2,mse)) # R2: 0.9781967895258501 MSE: 818.291825510698 R2nin arttığı görülüyor.

from sklearn.model_selection import GridSearchCV

parametreler={"min_samples_ split":range(2,50), "max_leaf_nodes":range(2, 50)}
grid=GridSearchCV(estimator=model, param_grid=parametreler, cv=10)
# {'max_leaf_nodes': 21, 'min_samples_split': 17}

model=DecisionTreeRegressor(random_state=0,max_leaf_nodes=21,min_samples_split=17)
model.fit(X_train.values.reshape(-1,1),y_train.values.reshape(-1,1))
tahmin=model.predict(X_test.values.reshape(-1,1))

r2=mt.r2_score(y_test, tahmin) 
mse=mt.mean_squared_error(y_test, tahmin)
print("R2: {} MSE: {}".format(r2,mse)) # R2: 0.9789043773351418 MSE: 791.7354924027405
```

R2 0.96'dan 0.97'ye çıktı. MSE 1333'den 791'e düştü.

# Bagging Karar Ağacı Regresyonu

Datayı parçalayarak birden fazla karar ağacı üretmeye çalışıyor.

```Python
import pandas as pd 

data = pd.DataFrame({
        "TV":[230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8.6, 199.8, 66.1, 214.7, 23.8, 97.5, 204.1, 195.4, 67.8, 281.4, 69.2, 147.3, 218.4, 237.4, 13.2, 228.3, 62.3, 262.9, 142.9, 240.1, 248.8, 70.6, 292.9, 112.9, 97.2, 265.6, 95.7, 290.7, 266.9, 74.7, 43.1, 228, 202.5, 177, 293.6, 206.9, 25.1, 175.1, 89.7, 239.9, 227.2, 66.9, 199.8, 100.4, 216.4, 182.6, 262.7, 198.9, 7.3, 136.2, 210.8, 210.7, 53.5, 261.3, 239.3, 102.7, 131.1, 69, 31.5, 139.3, 237.4, 216.8, 199.1, 109.8, 26.8, 129.4, 213.4, 16.9, 27.5, 120.5, 5.4, 116, 76.4, 239.8, 75.3, 68.4, 213.5, 193.2, 76.3, 110.7, 88.3, 109.8, 134.3, 28.6, 217.7, 250.9, 107.4, 163.3, 197.6, 184.9, 289.7, 135.2, 222.4, 296.4, 280.2, 187.9, 238.2, 137.9, 25, 90.4, 13.1, 255.4, 225.8, 241.7, 175.7, 209.6, 78.2, 75.1, 139.2, 76.4, 125.7, 19.4, 141.3, 18.8, 224, 123.1, 229.5, 87.2, 7.8, 80.2, 220.3, 59.6, 0.7, 265.2, 8.4, 219.8, 36.9, 48.3, 25.6, 273.7, 43, 184.9, 73.4, 193.7, 220.5, 104.6, 96.2, 140.3, 240.1, 243.2, 38, 44.7, 280.7, 121, 197.6, 171.3, 187.8, 4.1, 93.9, 149.8, 11.7, 131.7, 172.5, 85.7, 188.4, 163.5, 117.2, 234.5, 17.9, 206.8, 215.4, 284.3, 50, 164.5, 19.6, 168.4, 222.4, 276.9, 248.4, 170.2, 276.7, 165.6, 156.6, 218.5, 56.2, 287.6, 253.8, 205, 139.5, 191.1, 286, 18.7, 39.5, 75.5, 17.2, 166.8, 149.7, 38.2, 94.2, 177, 283.6, 232.1],
        "Radio":[37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1, 2.6, 5.8, 24, 35.1, 7.6, 32.9, 47.7, 36.6, 39.6, 20.5, 23.9, 27.7, 5.1, 15.9, 16.9, 12.6, 3.5, 29.3, 16.7, 27.1, 16, 28.3, 17.4, 1.5, 20, 1.4, 4.1, 43.8, 49.4, 26.7, 37.7, 22.3, 33.4, 27.7, 8.4, 25.7, 22.5, 9.9, 41.5, 15.8, 11.7, 3.1, 9.6, 41.7, 46.2, 28.8, 49.4, 28.1, 19.2, 49.6, 29.5, 2, 42.7, 15.5, 29.6, 42.8, 9.3, 24.6, 14.5, 27.5, 43.9, 30.6, 14.3, 33, 5.7, 24.6, 43.7, 1.6, 28.5, 29.9, 7.7, 26.7, 4.1, 20.3, 44.5, 43, 18.4, 27.5, 40.6, 25.5, 47.8, 4.9, 1.5, 33.5, 36.5, 14, 31.6, 3.5, 21, 42.3, 41.7, 4.3, 36.3, 10.1, 17.2, 34.3, 46.4, 11, 0.3, 0.4, 26.9, 8.2, 38, 15.4, 20.6, 46.8, 35, 14.3, 0.8, 36.9, 16, 26.8, 21.7, 2.4, 34.6, 32.3, 11.8, 38.9, 0, 49, 12, 39.6, 2.9, 27.2, 33.5, 38.6, 47, 39, 28.9, 25.9, 43.9, 17, 35.4, 33.2, 5.7, 14.8, 1.9, 7.3, 49, 40.3, 25.8, 13.9, 8.4, 23.3, 39.7, 21.1, 11.6, 43.5, 1.3, 36.9, 18.4, 18.1, 35.8, 18.1, 36.8, 14.7, 3.4, 37.6, 5.2, 23.6, 10.6, 11.6, 20.9, 20.1, 7.1, 3.4, 48.9, 30.2, 7.8, 2.3, 10, 2.6, 5.4, 5.7, 43, 21.3, 45.1, 2.1, 28.7, 13.9, 12.1, 41.1, 10.8, 4.1, 42, 35.6, 3.7, 4.9, 9.3, 42, 8.6],
        "Newspaper":[69.2, 45.1, 69.3, 58.5, 58.4, 75, 23.5, 11.6, 1, 21.2, 24.2, 4, 65.9, 7.2, 46, 52.9, 114, 55.8, 18.3, 19.1, 53.4, 23.5, 49.6, 26.2, 18.3, 19.5, 12.6, 22.9, 22.9, 40.8, 43.2, 38.6, 30, 0.3, 7.4, 8.5, 5, 45.7, 35.1, 32, 31.6, 38.7, 1.8, 26.4, 43.3, 31.5, 35.7, 18.5, 49.9, 36.8, 34.6, 3.6, 39.6, 58.7, 15.9, 60, 41.4, 16.6, 37.7, 9.3, 21.4, 54.7, 27.3, 8.4, 28.9, 0.9, 2.2, 10.2, 11, 27.2, 38.7, 31.7, 19.3, 31.3, 13.1, 89.4, 20.7, 14.2, 9.4, 23.1, 22.3, 36.9, 32.5, 35.6, 33.8, 65.7, 16, 63.2, 73.4, 51.4, 9.3, 33, 59, 72.3, 10.9, 52.9, 5.9, 22, 51.2, 45.9, 49.8, 100.9, 21.4, 17.9, 5.3, 59, 29.7, 23.2, 25.6, 5.5, 56.5, 23.2, 2.4, 10.7, 34.5, 52.7, 25.6, 14.8, 79.2, 22.3, 46.2, 50.4, 15.6, 12.4, 74.2, 25.9, 50.6, 9.2, 3.2, 43.1, 8.7, 43, 2.1, 45.1, 65.6, 8.5, 9.3, 59.7, 20.5, 1.7, 12.9, 75.6, 37.9, 34.4, 38.9, 9, 8.7, 44.3, 11.9, 20.6, 37, 48.7, 14.2, 37.7, 9.5, 5.7, 50.5, 24.3, 45.2, 34.6, 30.7, 49.3, 25.6, 7.4, 5.4, 84.8, 21.6, 19.4, 57.6, 6.4, 18.4, 47.4, 17, 12.8, 13.1, 41.8, 20.3, 35.2, 23.7, 17.6, 8.3, 27.4, 29.7, 71.8, 30, 19.6, 26.6, 18.2, 3.7, 23.4, 5.8, 6, 31.6, 3.6, 6, 13.8, 8.1, 6.4, 66.2, 8.7],
        "Sales":[22.1, 10.4, 12, 16.5, 17.9, 7.2, 11.8, 13.2, 4.8, 15.6, 12.6, 17.4, 9.2, 13.7, 19, 22.4, 12.5, 24.4, 11.3, 14.6, 18, 17.5, 5.6, 20.5, 9.7, 17, 15, 20.9, 18.9, 10.5, 21.4, 11.9, 13.2, 17.4, 11.9, 17.8, 25.4, 14.7, 10.1, 21.5, 16.6, 17.1, 20.7, 17.9, 8.5, 16.1, 10.6, 23.2, 19.8, 9.7, 16.4, 10.7, 22.6, 21.2, 20.2, 23.7, 5.5, 13.2, 23.8, 18.4, 8.1, 24.2, 20.7, 14, 16, 11.3, 11, 13.4, 18.9, 22.3, 18.3, 12.4, 8.8, 11, 17, 8.7, 6.9, 14.2, 5.3, 11, 11.8, 17.3, 11.3, 13.6, 21.7, 20.2, 12, 16, 12.9, 16.7, 14, 7.3, 19.4, 22.2, 11.5, 16.9, 16.7, 20.5, 25.4, 17.2, 16.7, 23.8, 19.8, 19.7, 20.7, 15, 7.2, 12, 5.3, 19.8, 18.4, 21.8, 17.1, 20.9, 14.6, 12.6, 12.2, 9.4, 15.9, 6.6, 15.5, 7, 16.6, 15.2, 19.7, 10.6, 6.6, 11.9, 24.7, 9.7, 1.6, 17.7, 5.7, 19.6, 10.8, 11.6, 9.5, 20.8, 9.6, 20.7, 10.9, 19.2, 20.1, 10.4, 12.3, 10.3, 18.2, 25.4, 10.9, 10.1, 16.1, 11.6, 16.6, 16, 20.6, 3.2, 15.3, 10.1, 7.3, 12.9, 16.4, 13.3, 19.9, 18, 11.9, 16.9, 8, 17.2, 17.1, 20, 8.4, 17.5, 7.6, 16.7, 16.5, 27, 20.2, 16.7, 16.8, 17.6, 15.5, 17.2, 8.7, 26.2, 17.6, 22.6, 10.3, 17.3, 20.9, 6.7, 10.8, 11.9, 5.9, 19.6, 17.3, 7.6, 14, 14.8, 25.5, 18.4]
    })

y=veri["Sales"]
X=veri.drop(columns="Sales",axis=1)

from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test=train_test_split(X,y,test_size=0.2,random_state=42)

from sklearn.tree import DecisionTreeRegressor
dtmodel=DecisionTreeRegressor(random_state=0)
dtmodel.fit(X_train,y_train)
dttahmin=dtmodel.predict(X_test)

import sklearn.metrics as mt
import numpy as np

r2=mt.r2_score(y_test,dttahmin) 
rmse=np.sqrt(mt.mean_squared_error(y_test,dttahmin))

import sklearn.metrics as mt
import numpy as np

r2=mt.r2_score(y_test,dttahmin) 
rmse=np.sqrt(mt.mean_squared_error(y_test,dttahmin))
print("Karar Ağacı R2: {} Karar Ağacı RMSE: {}".format(r2, rmse))
# Karar Ağacı R2: 0.879284232600134 Karar Ağacı RMSE: 1.9313855130449744

from sklearn.ensemble import BaggingRegressor

bgmodel=BaggingRegressor(random_state=0)
bgmodel.fit(X_train,y_train)
bgtahmin=bgmodel.predict(X_test)

r22=mt.r2_score(y_test, bgtahmin)
rmse2=np.sqrt(mt.mean_squared_error(y_test,bgtahmin))

print( "Bagging R2: {} Bagging RMSE: {}".format(r22,rmse2))
# Bagging R2: 0.9512905689441477 Bagging RMSE: 1.2268557372405282

from sklearn.model_selection import GridSearchCV

parametreler1={"min_samples_split":range(2,25),"max_leaf_nodes":range(2,25)}
grid1=GridSearchCV(estimator=dtmodel, param_grid=parametreler1, cv=10)
grid1.fit(X_train,y_train)
print(grid1.best_params_)

# {'max_leaf_nodes': 18, 'min_samples_split': 4}

parametreler2={"n_estimators":range(2,25)}
grid2=GridSearchCV(estimator=bgmodel, param_grid=parametreler2,cv=10)
grid2.fit(X_train,y_train)
print(grid2.best_params_)

# {'n_estimators': 23}

dtmodel=DecisionTreeRegressor(random_state=0, max_leaf_nodes=18, min_samples_split=4)
dtmodel.fit(X_train,y_train)
dttahmin=dtmodel.predict(X_test)

r2=mt.r2_score(y_test,dttahmin) 
rmse=np.sqrt(mt.mean_squared_error(y_test,dttahmin))
print("Karar Ağacı R2: {} Karar Ağacı RMSE: {}".format(r2, rmse))

# Karar Ağacı R2: 0.8944127560700043 Karar Ağacı RMSE: 1.8063117071549948

bgmodel=BaggingRegressor(random_state=0,n_estimators=23)
bgmodel.fit(X_train,y_train)
bgtahmin=bgmodel.predict(X_test)

r22=mt.r2_score(y_test, bgtahmin)
rmse2=np.sqrt(mt.mean_squared_error(y_test,bgtahmin))

print( "Bagging R2: {} Bagging RMSE: {}".format(r22,rmse2))

# Bagging R2: 0.9568205184197045 Bagging RMSE: 1.1551162185082802
```

# Random Forest Regresyon

```Python
import pandas as pd 

data = pd.DataFrame({
    "Gözlem":[1, 2, 3, 4, 5],
    "X1":[3, 2, 5, 6, 4],
    "X2":[2, 6, 1, 3, 6],
    "X3":[2, 6, 1, 1, 2],
    "X4":[3, 5, 2, 1, 6],
    "Y":[3, 2, 1, 3, 1]
    })
```

Bagging bütün sütunları tutarken Random Forest Sample alırken sütunlarda da eksiltme yapıyor. Mesela X2 ve X5'in olmadığı durumu da model oluştururken değerlendiriyor. Bütün değişkenleri kullanmak yerine bazı değişkenleri kullanarak değerlendiriyor. (X1, X3 ve Y değişkenlerini kullanıyor ve diğer denemede X2,X3 ve Y değişkenlerini kullanıyor gibi.)

```Python
import pandas as pd

data=pd.DataFrame({
    "Position":["Business Analyst", "Junior Consultant", "Senior Consultant", "Manager", "Country Manager", "Region Manager", "Partner", "Senior Partner", "C-level", "CEO"],
    "Level":[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    "Salary":[45000, 50000, 60000, 80000, 110000, 150000, 200000, 300000, 500000, 1000000]
})

veri=data.copy()

y=veri["Salary"]
X=veri["Level"]

y=np.array(y).reshape(-1,1)
X=np.array(X).reshape(-1,1)

from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor

dtmodel=DecisionTreeRegressor(random_state=0)
dtmodel.fit(X,y)
dttahmin=dtmodel.predict(X)


rfmodel=RandomForestRegressor(random_state=0)
rfmodel.fit(X,y)
rftahmin=rfmodel.predict(X)

import matplotlib.pyplot as plt

plt.scatter(X,y,color="red")
plt.show()
```

![image](./images/rd1.png) 

```Python
plt.scatter(X,y,color="red")
plt.plot(X,dttahmin, color="blue")
plt.show()
```

![image](./images/rd2.png)

Karar ağacı görüldüğü gibi yapıyı ezberlemiştir. Random forest değerlerine bakalım;

```Python
plt.scatter(X,y,color="red")
plt.plot(X,rftahmin, color="blue")
plt.show()
```

![image](./images/rd3.png) 

---

```Python
import pandas as pd 

data = pd.DataFrame({
        "TV":[230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8.6, 199.8, 66.1, 214.7, 23.8, 97.5, 204.1, 195.4, 67.8, 281.4, 69.2, 147.3, 218.4, 237.4, 13.2, 228.3, 62.3, 262.9, 142.9, 240.1, 248.8, 70.6, 292.9, 112.9, 97.2, 265.6, 95.7, 290.7, 266.9, 74.7, 43.1, 228, 202.5, 177, 293.6, 206.9, 25.1, 175.1, 89.7, 239.9, 227.2, 66.9, 199.8, 100.4, 216.4, 182.6, 262.7, 198.9, 7.3, 136.2, 210.8, 210.7, 53.5, 261.3, 239.3, 102.7, 131.1, 69, 31.5, 139.3, 237.4, 216.8, 199.1, 109.8, 26.8, 129.4, 213.4, 16.9, 27.5, 120.5, 5.4, 116, 76.4, 239.8, 75.3, 68.4, 213.5, 193.2, 76.3, 110.7, 88.3, 109.8, 134.3, 28.6, 217.7, 250.9, 107.4, 163.3, 197.6, 184.9, 289.7, 135.2, 222.4, 296.4, 280.2, 187.9, 238.2, 137.9, 25, 90.4, 13.1, 255.4, 225.8, 241.7, 175.7, 209.6, 78.2, 75.1, 139.2, 76.4, 125.7, 19.4, 141.3, 18.8, 224, 123.1, 229.5, 87.2, 7.8, 80.2, 220.3, 59.6, 0.7, 265.2, 8.4, 219.8, 36.9, 48.3, 25.6, 273.7, 43, 184.9, 73.4, 193.7, 220.5, 104.6, 96.2, 140.3, 240.1, 243.2, 38, 44.7, 280.7, 121, 197.6, 171.3, 187.8, 4.1, 93.9, 149.8, 11.7, 131.7, 172.5, 85.7, 188.4, 163.5, 117.2, 234.5, 17.9, 206.8, 215.4, 284.3, 50, 164.5, 19.6, 168.4, 222.4, 276.9, 248.4, 170.2, 276.7, 165.6, 156.6, 218.5, 56.2, 287.6, 253.8, 205, 139.5, 191.1, 286, 18.7, 39.5, 75.5, 17.2, 166.8, 149.7, 38.2, 94.2, 177, 283.6, 232.1],
        "Radio":[37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1, 2.6, 5.8, 24, 35.1, 7.6, 32.9, 47.7, 36.6, 39.6, 20.5, 23.9, 27.7, 5.1, 15.9, 16.9, 12.6, 3.5, 29.3, 16.7, 27.1, 16, 28.3, 17.4, 1.5, 20, 1.4, 4.1, 43.8, 49.4, 26.7, 37.7, 22.3, 33.4, 27.7, 8.4, 25.7, 22.5, 9.9, 41.5, 15.8, 11.7, 3.1, 9.6, 41.7, 46.2, 28.8, 49.4, 28.1, 19.2, 49.6, 29.5, 2, 42.7, 15.5, 29.6, 42.8, 9.3, 24.6, 14.5, 27.5, 43.9, 30.6, 14.3, 33, 5.7, 24.6, 43.7, 1.6, 28.5, 29.9, 7.7, 26.7, 4.1, 20.3, 44.5, 43, 18.4, 27.5, 40.6, 25.5, 47.8, 4.9, 1.5, 33.5, 36.5, 14, 31.6, 3.5, 21, 42.3, 41.7, 4.3, 36.3, 10.1, 17.2, 34.3, 46.4, 11, 0.3, 0.4, 26.9, 8.2, 38, 15.4, 20.6, 46.8, 35, 14.3, 0.8, 36.9, 16, 26.8, 21.7, 2.4, 34.6, 32.3, 11.8, 38.9, 0, 49, 12, 39.6, 2.9, 27.2, 33.5, 38.6, 47, 39, 28.9, 25.9, 43.9, 17, 35.4, 33.2, 5.7, 14.8, 1.9, 7.3, 49, 40.3, 25.8, 13.9, 8.4, 23.3, 39.7, 21.1, 11.6, 43.5, 1.3, 36.9, 18.4, 18.1, 35.8, 18.1, 36.8, 14.7, 3.4, 37.6, 5.2, 23.6, 10.6, 11.6, 20.9, 20.1, 7.1, 3.4, 48.9, 30.2, 7.8, 2.3, 10, 2.6, 5.4, 5.7, 43, 21.3, 45.1, 2.1, 28.7, 13.9, 12.1, 41.1, 10.8, 4.1, 42, 35.6, 3.7, 4.9, 9.3, 42, 8.6],
        "Newspaper":[69.2, 45.1, 69.3, 58.5, 58.4, 75, 23.5, 11.6, 1, 21.2, 24.2, 4, 65.9, 7.2, 46, 52.9, 114, 55.8, 18.3, 19.1, 53.4, 23.5, 49.6, 26.2, 18.3, 19.5, 12.6, 22.9, 22.9, 40.8, 43.2, 38.6, 30, 0.3, 7.4, 8.5, 5, 45.7, 35.1, 32, 31.6, 38.7, 1.8, 26.4, 43.3, 31.5, 35.7, 18.5, 49.9, 36.8, 34.6, 3.6, 39.6, 58.7, 15.9, 60, 41.4, 16.6, 37.7, 9.3, 21.4, 54.7, 27.3, 8.4, 28.9, 0.9, 2.2, 10.2, 11, 27.2, 38.7, 31.7, 19.3, 31.3, 13.1, 89.4, 20.7, 14.2, 9.4, 23.1, 22.3, 36.9, 32.5, 35.6, 33.8, 65.7, 16, 63.2, 73.4, 51.4, 9.3, 33, 59, 72.3, 10.9, 52.9, 5.9, 22, 51.2, 45.9, 49.8, 100.9, 21.4, 17.9, 5.3, 59, 29.7, 23.2, 25.6, 5.5, 56.5, 23.2, 2.4, 10.7, 34.5, 52.7, 25.6, 14.8, 79.2, 22.3, 46.2, 50.4, 15.6, 12.4, 74.2, 25.9, 50.6, 9.2, 3.2, 43.1, 8.7, 43, 2.1, 45.1, 65.6, 8.5, 9.3, 59.7, 20.5, 1.7, 12.9, 75.6, 37.9, 34.4, 38.9, 9, 8.7, 44.3, 11.9, 20.6, 37, 48.7, 14.2, 37.7, 9.5, 5.7, 50.5, 24.3, 45.2, 34.6, 30.7, 49.3, 25.6, 7.4, 5.4, 84.8, 21.6, 19.4, 57.6, 6.4, 18.4, 47.4, 17, 12.8, 13.1, 41.8, 20.3, 35.2, 23.7, 17.6, 8.3, 27.4, 29.7, 71.8, 30, 19.6, 26.6, 18.2, 3.7, 23.4, 5.8, 6, 31.6, 3.6, 6, 13.8, 8.1, 6.4, 66.2, 8.7],
        "Sales":[22.1, 10.4, 12, 16.5, 17.9, 7.2, 11.8, 13.2, 4.8, 15.6, 12.6, 17.4, 9.2, 13.7, 19, 22.4, 12.5, 24.4, 11.3, 14.6, 18, 17.5, 5.6, 20.5, 9.7, 17, 15, 20.9, 18.9, 10.5, 21.4, 11.9, 13.2, 17.4, 11.9, 17.8, 25.4, 14.7, 10.1, 21.5, 16.6, 17.1, 20.7, 17.9, 8.5, 16.1, 10.6, 23.2, 19.8, 9.7, 16.4, 10.7, 22.6, 21.2, 20.2, 23.7, 5.5, 13.2, 23.8, 18.4, 8.1, 24.2, 20.7, 14, 16, 11.3, 11, 13.4, 18.9, 22.3, 18.3, 12.4, 8.8, 11, 17, 8.7, 6.9, 14.2, 5.3, 11, 11.8, 17.3, 11.3, 13.6, 21.7, 20.2, 12, 16, 12.9, 16.7, 14, 7.3, 19.4, 22.2, 11.5, 16.9, 16.7, 20.5, 25.4, 17.2, 16.7, 23.8, 19.8, 19.7, 20.7, 15, 7.2, 12, 5.3, 19.8, 18.4, 21.8, 17.1, 20.9, 14.6, 12.6, 12.2, 9.4, 15.9, 6.6, 15.5, 7, 16.6, 15.2, 19.7, 10.6, 6.6, 11.9, 24.7, 9.7, 1.6, 17.7, 5.7, 19.6, 10.8, 11.6, 9.5, 20.8, 9.6, 20.7, 10.9, 19.2, 20.1, 10.4, 12.3, 10.3, 18.2, 25.4, 10.9, 10.1, 16.1, 11.6, 16.6, 16, 20.6, 3.2, 15.3, 10.1, 7.3, 12.9, 16.4, 13.3, 19.9, 18, 11.9, 16.9, 8, 17.2, 17.1, 20, 8.4, 17.5, 7.6, 16.7, 16.5, 27, 20.2, 16.7, 16.8, 17.6, 15.5, 17.2, 8.7, 26.2, 17.6, 22.6, 10.3, 17.3, 20.9, 6.7, 10.8, 11.9, 5.9, 19.6, 17.3, 7.6, 14, 14.8, 25.5, 18.4]
    })

y=veri["Sales"]
X=veri.drop(columns="Sales",axis=1)

from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test=train_test_split(X,y,test_size=0.2,random_state=42)

from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import BaggingRegressor, RandomForestRegressor
import sklearn.metrics as mt
import numpy as np

# Decision Tree Regressor
dtmodel = DecisionTreeRegressor(random_state=0)
dtmodel.fit(X_train, y_train)
dttahmin = dtmodel.predict(X_test)

# Bagging Regressor
bgmodel = BaggingRegressor(random_state=0)
bgmodel.fit(X_train, y_train)
bgtahmin = bgmodel.predict(X_test)

# Random Forest Regressor
rfmodel = RandomForestRegressor(random_state=0)
rfmodel.fit(X_train, y_train)
rftahmin = rfmodel.predict(X_test)

r2dt = mt.r2_score(y_test, dttahmin)
r2bg = mt.r2_score(y_test, bgtahmin)
r2rf = mt.r2_score(y_test, rftahmin)

rmsetd = np.sqrt(mt.mean_squared_error(y_test, dttahmin))
rmsebg = np.sqrt(mt.mean_squared_error(y_test, bgtahmin))
rmserf = np.sqrt(mt.mean_squared_error(y_test, rftahmin))

print("Karar Ağacı Modeli R2: {} RMSE: {}".format(r2dt, rmsetd))
print("Bag Modeli R2: {} RMSE: {}".format(r2bg, rmsebg))
print("Random Forest Modeli R2: {} RMSE: {}".format(r2rf, rmserf))

# Karar Ağacı Modeli R2: 0.94549137316456 RMSE: 1.3347284367990366
# Bag Modeli R2: 0.958059260347154 RMSE: 1.1707881960457234
# Random Forest Modeli R2: 0.9570119634426565 RMSE: 1.185315865075636

from sklearn.model_selection import GridSearchCV

dtparametreler = {"min_samples_split": range(2, 20), "max_leaf_nodes": range(2, 20)}
dtgrid = GridSearchCV(estimator=dtmodel, param_grid=dtparametreler, cv=10)
dtgrid.fit(X_train, y_train)
print(dtgrid.best_params_)
# {'max_leaf_nodes': 19, 'min_samples_split': 6}

bgparametreler = {"n_estimators": range(2, 20)}
bggrid = GridSearchCV(estimator=bgmodel, param_grid=bgparametreler, cv=10)
bggrid.fit(X_train, y_train)
print(bggrid.best_params_)
# {'n_estimators': 13}

rfparametreler = {"max_depth": range(2, 20), "max_features": range(2, 20), "n_estimators": range(2, 20)}
# rfgrid = GridSearchCV(estimator=rfmodel, param_grid=rfparametreler, cv=10)
# rfgrid.fit(X_train, y_train)
# print(rfgrid.best_params_) çok uzun sürüyor

# %100 CPU kullanımı ve süreyi azaltmak için;

dtparametreler = {"min_samples_split": range(2, 20), "max_leaf_nodes": range(2, 20)}
dtgrid = GridSearchCV(estimator=dtmodel, param_grid=dtparametreler, cv=10, n_jobs=-1)
dtgrid.fit(X_train, y_train)
print(dtgrid.best_params_)
# {'max_leaf_nodes': 19, 'min_samples_split': 6}

bgparametreler = {"n_estimators": range(2, 20)}
bggrid = GridSearchCV(estimator=bgmodel, param_grid=bgparametreler, cv=10, n_jobs=-1)
bggrid.fit(X_train, y_train)
print(bggrid.best_params_)
# {'n_estimators': 13}

rfparametreler = {"max_depth": range(2, 20), "max_features": range(2, 20), "n_estimators": range(2, 20)}
rfgrid = GridSearchCV(estimator=rfmodel, param_grid=rfparametreler, cv=10, n_jobs=-1)
rfgrid.fit(X_train, y_train)
print(rfgrid.best_params_)
# {'max_depth': 13, 'max_features': 2, 'n_estimators': 19}

# Karar Ağacı Modeli R2: 0.93453209257754829 RMSE: 1.4627849453010757
# Bag Modeli R2: 0.9562521167856068 RMSE: 1.1957456637210488
# Random Forest Modeli R2: 0.9639233775870784 RMSE: 1.085858481874723
```

Karar ağacının ve Bag modelinin R2 değeri düşmüş. Demek ki tanımlarken kurduğumuz Range fonksiyonu daha geniş olmalı. 

# Toplu Model Çözümü

```Python
import pandas as pd 

data = pd.DataFrame({
        "TV":[230.1, 44.5, 17.2, 151.5, 180.8, 8.7, 57.5, 120.2, 8.6, 199.8, 66.1, 214.7, 23.8, 97.5, 204.1, 195.4, 67.8, 281.4, 69.2, 147.3, 218.4, 237.4, 13.2, 228.3, 62.3, 262.9, 142.9, 240.1, 248.8, 70.6, 292.9, 112.9, 97.2, 265.6, 95.7, 290.7, 266.9, 74.7, 43.1, 228, 202.5, 177, 293.6, 206.9, 25.1, 175.1, 89.7, 239.9, 227.2, 66.9, 199.8, 100.4, 216.4, 182.6, 262.7, 198.9, 7.3, 136.2, 210.8, 210.7, 53.5, 261.3, 239.3, 102.7, 131.1, 69, 31.5, 139.3, 237.4, 216.8, 199.1, 109.8, 26.8, 129.4, 213.4, 16.9, 27.5, 120.5, 5.4, 116, 76.4, 239.8, 75.3, 68.4, 213.5, 193.2, 76.3, 110.7, 88.3, 109.8, 134.3, 28.6, 217.7, 250.9, 107.4, 163.3, 197.6, 184.9, 289.7, 135.2, 222.4, 296.4, 280.2, 187.9, 238.2, 137.9, 25, 90.4, 13.1, 255.4, 225.8, 241.7, 175.7, 209.6, 78.2, 75.1, 139.2, 76.4, 125.7, 19.4, 141.3, 18.8, 224, 123.1, 229.5, 87.2, 7.8, 80.2, 220.3, 59.6, 0.7, 265.2, 8.4, 219.8, 36.9, 48.3, 25.6, 273.7, 43, 184.9, 73.4, 193.7, 220.5, 104.6, 96.2, 140.3, 240.1, 243.2, 38, 44.7, 280.7, 121, 197.6, 171.3, 187.8, 4.1, 93.9, 149.8, 11.7, 131.7, 172.5, 85.7, 188.4, 163.5, 117.2, 234.5, 17.9, 206.8, 215.4, 284.3, 50, 164.5, 19.6, 168.4, 222.4, 276.9, 248.4, 170.2, 276.7, 165.6, 156.6, 218.5, 56.2, 287.6, 253.8, 205, 139.5, 191.1, 286, 18.7, 39.5, 75.5, 17.2, 166.8, 149.7, 38.2, 94.2, 177, 283.6, 232.1],
        "Radio":[37.8, 39.3, 45.9, 41.3, 10.8, 48.9, 32.8, 19.6, 2.1, 2.6, 5.8, 24, 35.1, 7.6, 32.9, 47.7, 36.6, 39.6, 20.5, 23.9, 27.7, 5.1, 15.9, 16.9, 12.6, 3.5, 29.3, 16.7, 27.1, 16, 28.3, 17.4, 1.5, 20, 1.4, 4.1, 43.8, 49.4, 26.7, 37.7, 22.3, 33.4, 27.7, 8.4, 25.7, 22.5, 9.9, 41.5, 15.8, 11.7, 3.1, 9.6, 41.7, 46.2, 28.8, 49.4, 28.1, 19.2, 49.6, 29.5, 2, 42.7, 15.5, 29.6, 42.8, 9.3, 24.6, 14.5, 27.5, 43.9, 30.6, 14.3, 33, 5.7, 24.6, 43.7, 1.6, 28.5, 29.9, 7.7, 26.7, 4.1, 20.3, 44.5, 43, 18.4, 27.5, 40.6, 25.5, 47.8, 4.9, 1.5, 33.5, 36.5, 14, 31.6, 3.5, 21, 42.3, 41.7, 4.3, 36.3, 10.1, 17.2, 34.3, 46.4, 11, 0.3, 0.4, 26.9, 8.2, 38, 15.4, 20.6, 46.8, 35, 14.3, 0.8, 36.9, 16, 26.8, 21.7, 2.4, 34.6, 32.3, 11.8, 38.9, 0, 49, 12, 39.6, 2.9, 27.2, 33.5, 38.6, 47, 39, 28.9, 25.9, 43.9, 17, 35.4, 33.2, 5.7, 14.8, 1.9, 7.3, 49, 40.3, 25.8, 13.9, 8.4, 23.3, 39.7, 21.1, 11.6, 43.5, 1.3, 36.9, 18.4, 18.1, 35.8, 18.1, 36.8, 14.7, 3.4, 37.6, 5.2, 23.6, 10.6, 11.6, 20.9, 20.1, 7.1, 3.4, 48.9, 30.2, 7.8, 2.3, 10, 2.6, 5.4, 5.7, 43, 21.3, 45.1, 2.1, 28.7, 13.9, 12.1, 41.1, 10.8, 4.1, 42, 35.6, 3.7, 4.9, 9.3, 42, 8.6],
        "Newspaper":[69.2, 45.1, 69.3, 58.5, 58.4, 75, 23.5, 11.6, 1, 21.2, 24.2, 4, 65.9, 7.2, 46, 52.9, 114, 55.8, 18.3, 19.1, 53.4, 23.5, 49.6, 26.2, 18.3, 19.5, 12.6, 22.9, 22.9, 40.8, 43.2, 38.6, 30, 0.3, 7.4, 8.5, 5, 45.7, 35.1, 32, 31.6, 38.7, 1.8, 26.4, 43.3, 31.5, 35.7, 18.5, 49.9, 36.8, 34.6, 3.6, 39.6, 58.7, 15.9, 60, 41.4, 16.6, 37.7, 9.3, 21.4, 54.7, 27.3, 8.4, 28.9, 0.9, 2.2, 10.2, 11, 27.2, 38.7, 31.7, 19.3, 31.3, 13.1, 89.4, 20.7, 14.2, 9.4, 23.1, 22.3, 36.9, 32.5, 35.6, 33.8, 65.7, 16, 63.2, 73.4, 51.4, 9.3, 33, 59, 72.3, 10.9, 52.9, 5.9, 22, 51.2, 45.9, 49.8, 100.9, 21.4, 17.9, 5.3, 59, 29.7, 23.2, 25.6, 5.5, 56.5, 23.2, 2.4, 10.7, 34.5, 52.7, 25.6, 14.8, 79.2, 22.3, 46.2, 50.4, 15.6, 12.4, 74.2, 25.9, 50.6, 9.2, 3.2, 43.1, 8.7, 43, 2.1, 45.1, 65.6, 8.5, 9.3, 59.7, 20.5, 1.7, 12.9, 75.6, 37.9, 34.4, 38.9, 9, 8.7, 44.3, 11.9, 20.6, 37, 48.7, 14.2, 37.7, 9.5, 5.7, 50.5, 24.3, 45.2, 34.6, 30.7, 49.3, 25.6, 7.4, 5.4, 84.8, 21.6, 19.4, 57.6, 6.4, 18.4, 47.4, 17, 12.8, 13.1, 41.8, 20.3, 35.2, 23.7, 17.6, 8.3, 27.4, 29.7, 71.8, 30, 19.6, 26.6, 18.2, 3.7, 23.4, 5.8, 6, 31.6, 3.6, 6, 13.8, 8.1, 6.4, 66.2, 8.7],
        "Sales":[22.1, 10.4, 12, 16.5, 17.9, 7.2, 11.8, 13.2, 4.8, 15.6, 12.6, 17.4, 9.2, 13.7, 19, 22.4, 12.5, 24.4, 11.3, 14.6, 18, 17.5, 5.6, 20.5, 9.7, 17, 15, 20.9, 18.9, 10.5, 21.4, 11.9, 13.2, 17.4, 11.9, 17.8, 25.4, 14.7, 10.1, 21.5, 16.6, 17.1, 20.7, 17.9, 8.5, 16.1, 10.6, 23.2, 19.8, 9.7, 16.4, 10.7, 22.6, 21.2, 20.2, 23.7, 5.5, 13.2, 23.8, 18.4, 8.1, 24.2, 20.7, 14, 16, 11.3, 11, 13.4, 18.9, 22.3, 18.3, 12.4, 8.8, 11, 17, 8.7, 6.9, 14.2, 5.3, 11, 11.8, 17.3, 11.3, 13.6, 21.7, 20.2, 12, 16, 12.9, 16.7, 14, 7.3, 19.4, 22.2, 11.5, 16.9, 16.7, 20.5, 25.4, 17.2, 16.7, 23.8, 19.8, 19.7, 20.7, 15, 7.2, 12, 5.3, 19.8, 18.4, 21.8, 17.1, 20.9, 14.6, 12.6, 12.2, 9.4, 15.9, 6.6, 15.5, 7, 16.6, 15.2, 19.7, 10.6, 6.6, 11.9, 24.7, 9.7, 1.6, 17.7, 5.7, 19.6, 10.8, 11.6, 9.5, 20.8, 9.6, 20.7, 10.9, 19.2, 20.1, 10.4, 12.3, 10.3, 18.2, 25.4, 10.9, 10.1, 16.1, 11.6, 16.6, 16, 20.6, 3.2, 15.3, 10.1, 7.3, 12.9, 16.4, 13.3, 19.9, 18, 11.9, 16.9, 8, 17.2, 17.1, 20, 8.4, 17.5, 7.6, 16.7, 16.5, 27, 20.2, 16.7, 16.8, 17.6, 15.5, 17.2, 8.7, 26.2, 17.6, 22.6, 10.3, 17.3, 20.9, 6.7, 10.8, 11.9, 5.9, 19.6, 17.3, 7.6, 14, 14.8, 25.5, 18.4]
    })

veri=data.copy()

print(veri.isnull().sum())

# TV           0
# Radio        0
# Newspaper    0
# Sales        0
# dtype: int64

y=veri["Sales"]
X=veri.drop(columns="Sales",axis=1)

from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test=train_test_split(X,y,test_size=0.1,random_state=42)

# Polinomsal yapıyı almıyoruz. Transform olması gerekiyor. Ama sonra deneyebilirim.

from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet
from sklearn.svm import SVR
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import BaggingRegressor, RandomForestRegressor
import sklearn.metrics as mt
import numpy as np

def modeltahmin(model):
    model.fit(X_train, y_train)  # Train the model
    tahmin = model.predict(X_test)  # Make predictions
    r2 = mt.r2_score(y_test, tahmin)  # Calculate R² score
    rmse = np.sqrt(mt.mean_squared_error(y_test, tahmin))  # Calculate RMSE
    return [r2, rmse]

modeller = [
    LinearRegression(), 
    Ridge(), 
    Lasso(), 
    ElasticNet(),
    SVR(),
    DecisionTreeRegressor(random_state=0),
    BaggingRegressor(random_state=0),
    RandomForestRegressor(random_state=0)
]

ad = [
    "Linear Model", 
    "Ridge Model", 
    "Lasso Model", 
    "ElasticNet Model", 
    "SVR Model", 
    "Karar Ağacı Modeli", 
    "Bag Model", 
    "Random Forest Model"
]

sonuc = [modeltahmin(i) for i in modeller]

df = pd.DataFrame(ad, columns=["Model Adı"])
df2 = pd.DataFrame(sonuc, columns=["R2", "RMSE"])

df = df.join(df2)
df

#              Model Adı        R2      RMSE
# 0         Linear Model  0.912542  1.690675
# 1          Ridge Model  0.912541  1.690689
# 2          Lasso Model  0.911034  1.705185
# 3     ElasticNet Model  0.911678  1.699006
# 4            SVR Model  0.883462  1.951614
# 5   Karar Ağacı Modeli  0.945491  1.334728
# 6            Bag Model  0.958059  1.170788
# 7  Random Forest Model  0.957012  1.185316
```

Grid search ile en iyi parametreleri bulalım (Chatgpt ile);

```Python
from sklearn.model_selection import GridSearchCV

# Define parameter grids for each model
param_grids = {
    'LinearRegression': {},
    'Ridge': {
        'alpha': [0.1, 1, 10, 100],
        'max_iter': [100, 200, 300]
    },
    'Lasso': {
        'alpha': [0.1, 1, 10, 100]
    },
    'ElasticNet': {
        'alpha': [0.1, 1, 10, 100],
        'l1_ratio': [0.1, 0.2, 0.3]
    },
    'SVR': {
        'kernel': ['linear', 'rbf'],
        'C': [1, 10, 100],
        'gamma': ['scale', 'auto']
    },
    'DecisionTreeRegressor': {
        'max_depth': [3, 5, 7],
        'min_samples_split': [2, 5, 10]
    },
    'BaggingRegressor': {
        'n_estimators': [10, 50, 100],
        'max_features': [0.5, 1.0]
    },
    'RandomForestRegressor': {
        'n_estimators': [10, 50, 100],
        'max_depth': [5, 10, 15],
        'min_samples_split': [2, 5, 10]
    }
}

# Initialize models
modeller = [
    LinearRegression(),
    Ridge(),
    Lasso(),
    ElasticNet(),
    SVR(),
    DecisionTreeRegressor(random_state=0),
    BaggingRegressor(random_state=0),
    RandomForestRegressor(random_state=0)
]

# Define model names
ad = [
    "Linear Model", 
    "Ridge Model", 
    "Lasso Model", 
    "ElasticNet Model", 
    "SVR Model", 
    "Karar Ağacı Modeli", 
    "Bag Model", 
    "Random Forest Model"
]

# Function to use GridSearchCV to get the best model
def modeltahmin(model, param_grid):
    grid_search = GridSearchCV(model, param_grid, cv=5, n_jobs=-1, verbose=1)
    grid_search.fit(X_train, y_train)
    best_model = grid_search.best_estimator_
    tahmin = best_model.predict(X_test)
    r2 = mt.r2_score(y_test, tahmin)
    rmse = np.sqrt(mt.mean_squared_error(y_test, tahmin))
    return [r2, rmse, grid_search.best_params_]

# Split data
X = data.drop(columns="Sales", axis=1)
y = data["Sales"]

from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.1, random_state=42)

# Get results for each model
sonuc = [modeltahmin(modeller[i], param_grids[model.__class__.__name__]) for i, model in enumerate(modeller)]

# Create DataFrame with model names, R2, RMSE, and best params
df = pd.DataFrame(ad, columns=["Model Adı"])
df2 = pd.DataFrame(sonuc, columns=["R2", "RMSE", "Best Params"])

# Join the results
df = df.join(df2)

# Print the result
print(df)

#              Model Adı        R2      RMSE  \
# 0         Linear Model  0.912542  1.690675   
# 1          Ridge Model  0.912393  1.692115   
# 2          Lasso Model  0.911034  1.705185   
# 3     ElasticNet Model  0.911922  1.696657   
# 4            SVR Model  0.963607  1.090607   
# 5   Karar Ağacı Modeli  0.931355  1.497834   
# 6            Bag Model  0.957974  1.171981   
# 7  Random Forest Model  0.957187  1.182901   

#                                          Best Params  
# 0                                                 {}  
# 1                    {'alpha': 100, 'max_iter': 100}  
# 2                                       {'alpha': 1}  
# 3                      {'alpha': 1, 'l1_ratio': 0.3}  
# 4      {'C': 100, 'gamma': 'scale', 'kernel': 'rbf'}  
# 5          {'max_depth': 5, 'min_samples_split': 10}  
# 6         {'max_features': 1.0, 'n_estimators': 100}  
# 7  {'max_depth': 15, 'min_samples_split': 2, 'n_e... 
```

# Lojistik Regresyon

Lojistik Regresyon formülü;

$$
\sigma(y) = \frac{1}{1 + e^{-y}}
$$

$ y = \beta_0 + \beta_1 \cdot x $ formülünden yola çıkarak;

$$
\sigma(y) = \frac{1}{1 + e^{-(\beta_0 + \beta_1 \cdot x)}}
$$

Sigmoid fonksiyonunu görelim. 

```Python
import pandas as pd
import numpy as np

sig = pd.DataFrame({"y":range(-10, 11)})

# DataFrame oluşturuluyor
sig = pd.DataFrame({"y": range(-10, 11)})

# Sigmoid fonksiyonu
sig['Fonk'] = 1 / (1 + np.exp(-sig['y']))

import matplotlib.pyplot as plt

plt.scatter(sig['y'], sig['Fonk'])
plt.xlabel('y')  # x eksenine label ekliyoruz
plt.ylabel('Fonk')  # y eksenine label ekliyoruz
plt.title('Sigmoid Fonksiyonu Grafiği')  # Başlık ekliyoruz
plt.show()
```

![image](./images/lr1.png) 

Bu fonksiyonda en küçük kareler yöntemini kullanamayız. Maksimum olabilirlik tahmini (MLE) yapısı kullanılır.

Lojistik regresyon 3 çeşittir;

- İkili Lojistik Regresyon (Erkek, Kadın gibi)
- Çoklu Lojistik Regresyon (Birden fazla Kategorik değişken)
- Çoklu Lojistik Regresyon (İyi, Orta, Kötü gibi sıralı)

## Örnekler

Dataset: [Kanser dataset](https://www.kaggle.com/code/kanncaa1/logistic-regression-implementation/data)

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

# id                           0
# diagnosis                    0
# radius_mean                  0
# texture_mean                 0
# perimeter_mean               0
# area_mean                    0
# smoothness_mean              0
# compactness_mean             0
# concavity_mean               0
# concave points_mean          0
# symmetry_mean                0
# fractal_dimension_mean       0
# radius_se                    0
# texture_se                   0
# perimeter_se                 0
# area_se                      0
# smoothness_se                0
# compactness_se               0
# concavity_se                 0
# concave points_se            0
# symmetry_se                  0
# fractal_dimension_se         0
# radius_worst                 0
# texture_worst                0
# perimeter_worst              0
# area_worst                   0
# smoothness_worst             0
# compactness_worst            0
# concavity_worst              0
# concave points_worst         0
# symmetry_worst               0
# fractal_dimension_worst      0
# Unnamed: 32                569
# dtype: int64

veri = veri.drop(columns=["id", "Unnamed: 32"], axis=1)

y = veri["diagnosis"]
X = veri.drop(columns="diagnosis", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.linear_model import LogisticRegression

model = LogisticRegression(random_state=0)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)
print(tahmin)

# ['B' 'M' 'M' 'B' 'B' 'M' 'M' 'M' 'B' 'B' 'B' 'M' 'B' 'M' 'B' 'M' 'B' 'B' 'B' 'M' 'B' 'B' 'M' 'B' 'B' 'B' 'B' 'B' 'B' 'M' 'B' 'B' 'B' 'B' 'B' 'B' 'M' 'B' 'M' 'B' 'B' 'M' 'B' 'B' 'B' 'B' 'B' 'B' 'B' 'B' 'M' 'M' 'B' 'B' 'B' 'B' 'B' 'M' 'M' 'B' 'B' 'M' 'M' 'B' 'B' 'B' 'M' 'M' 'B' 'B' 'M' 'M' 'B' 'M' 'B' 'B' 'B' 'B' 'B' 'B' 'M' 'B' 'M' 'M' 'M' 'M' 'M' 'M' 'B' 'B' 'B' 'B' 'B' 'B' 'B' 'B' 'M' 'M' 'B' 'M' 'M' 'B' 'M' 'M' 'B' 'B' 'B' 'M' 'B' 'B' 'M' 'B' 'M' 'M']
```

---

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

veri.diagnosis = [1 if kod == "M" else 0 for kod in veri.diagnosis]

y = veri["diagnosis"]
X = veri.drop(columns="diagnosis", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.linear_model import LogisticRegression

model = LogisticRegression(random_state=0)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)
print(tahmin)

# [0 1 1 0 0 1 1 1 0 0 0 1 0 1 0 1 0 0 0 1 0 0 1 0 0 0 0 0 0 1 0 0 0 0 0 0 1 0 1 0 0 1 0 0 0 0 0 0 0 0 1 1 0 0 0 0 0 1 1 0 0 1 1 0 0 0 1 1 0 0 1 1 0 1 0 0 0 0 0 0 1 0 1 1 1 1 1 1 0 0 0 0 0 0 0 0 1 1 0 1 1 0 1 1 0 0 0 1 0 0 1 0 1 1]

from sklearn.metrics import confusion_matrix, accuracy_score, classification_report, roc_curve, roc_auc_score
import matplotlib.pyplot as plt

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[70  1]
#  [ 2 41]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9736842105263158

cr = classification_report(y_test, tahmin)
print(cr)
#               precision    recall  f1-score   support

#            0       0.97      0.99      0.98        71
#            1       0.98      0.95      0.96        43

#     accuracy                           0.97       114
#    macro avg       0.97      0.97      0.97       114
# weighted avg       0.97      0.97      0.97       114

auc = roc_auc_score(y_test, tahmin)

fpr, tpr, threshold = roc_curve(y_test, model.predict_proba(X_test)[:, 1])
plt.plot(fpr, tpr, label="Model AUC (Alan=%0.2f)" % auc)
plt.plot([0, 1], [0, 1], "r--")
plt.show()
```

![image](./images/lr2.png) 

---

dataset: kaggle iris species

```Python
import pandas as pd

data=pd.read_csv("Iris.csv")
veri=data.copy()

print(veri.isnull().sum())
# Id               0
# SepalLengthCm    0
# SepalWidthCm     0
# PetalLengthCm    0
# PetalWidthCm     0
# Species          0
# dtype: int64

veri=veri.drop("Id",axis=1)

print(veri)
#      SepalLengthCm  SepalWidthCm  PetalLengthCm  PetalWidthCm         Species
# 0              5.1           3.5            1.4           0.2     Iris-setosa
# 1              4.9           3.0            1.4           0.2     Iris-setosa
# 2              4.7           3.2            1.3           0.2     Iris-setosa
# 3              4.6           3.1            1.5           0.2     Iris-setosa
# 4              5.0           3.6            1.4           0.2     Iris-setosa
# ..             ...           ...            ...           ...             ...
# 145            6.7           3.0            5.2           2.3  Iris-virginica
# 146            6.3           2.5            5.0           1.9  Iris-virginica
# 147            6.5           3.0            5.2           2.0  Iris-virginica
# 148            6.2           3.4            5.4           2.3  Iris-virginica
# 149            5.9           3.0            5.1           1.8  Iris-virginica

# [150 rows x 5 columns]

veri["Species"].unique()
# ['Iris-setosa', 'Iris-versicolor', 'Iris-virginica']
```

2 den fazla grup olduğu için method çoklu sınıflandırmaya girer.

```Python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()
veri["Species"] = le.fit_transform(veri["Species"])
print(veri)
#      SepalLengthCm  SepalWidthCm  PetalLengthCm  PetalWidthCm  Species
# 0              5.1           3.5            1.4           0.2        0
# 1              4.9           3.0            1.4           0.2        0
# 2              4.7           3.2            1.3           0.2        0
# 3              4.6           3.1            1.5           0.2        0
# 4              5.0           3.6            1.4           0.2        0
# ..             ...           ...            ...           ...      ...
# 145            6.7           3.0            5.2           2.3        2
# 146            6.3           2.5            5.0           1.9        2
# 147            6.5           3.0            5.2           2.0        2
# 148            6.2           3.4            5.4           2.3        2
# 149            5.9           3.0            5.1           1.8        2

y = veri["Species"]
X = veri.drop(columns="Species", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.linear_model import LogisticRegression

model = LogisticRegression(random_state=0)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)
print(tahmin) # [1 0 2 1 1 0 1 2 1 1 2 0 0 0 0 1 2 1 1 2 0 2 0 2 2 2 2 2 0 0]

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
acs = accuracy_score(y_test, tahmin)

print(cm)
# [[10  0  0]
#  [ 0  9  0]
#  [ 0  0 11]]

print(acs)
# 1.0
```

Başarı %100 çıktı. Şüphelenmeliyiz.

```Python
from sklearn.model_selection import cross_val_score

cv = cross_val_score(model, X_test, y_test, cv=10)
print(cv) # [1.         1.         1.         1.         1.         1.  0.66666667 1.         1.         0.66666667]

```

```python
import pandas as pd

data=pd.read_csv("WineQT.csv")
veri=data.copy()

print(veri.isnull().sum())
# fixed acidity           0
# volatile acidity        0
# citric acid             0
# residual sugar          0
# chlorides               0
# free sulfur dioxide     0
# total sulfur dioxide    0
# density                 0
# pH                      0
# sulphates               0
# alcohol                 0
# quality                 0
# Id                      0
# dtype: int64

veri["quality"].unique()
# [5, 6, 7, 4, 8, 3]
```

En yüksek 8 ve en düşük kalite 3 olacak şekilde sıralı değerlerdir.

```python
from sklearn.preprocessing import OrdinalEncoder

kategori = ["3", "4", "5", "6", "7", "8"]

oe = OrdinalEncoder(categories=[kategori])
veri["Kalite"] = oe.fit_transform(veri[["quality"]].values.reshape(-1, 1))

veri = veri.drop(columns=["quality","id"], axis=1)

y = veri["Kalite"]
X = veri.drop(columns="Kalite", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.linear_model import LogisticRegression

model = LogisticRegression(random_state=0, max_iter=1000)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[ 0  3  3  0  0]
#  [ 1 70 23  2  0]
#  [ 0 28 63  8  0]
#  [ 0  2 11 13  0]
#  [ 0  0  0  2  0]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.6375545851528385
```

# KKN 

Dataset: [Kanser dataset](https://www.kaggle.com/code/kanncaa1/logistic-regression-implementation/data)

```python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

M=veri[veri[ "diagnosis" ]=="M"]
B=veri[veri[ "diagnosis" ]=="B"]

import matplotlib.pyplot as plt

plt.scatter(M.radius_mean, M.texture_mean, color="red", label="Kötü Huylu")
plt.scatter(B.radius_mean, B.texture_mean, color="green", label="İyi Huylu")
plt.legend()
plt.show()
```

![image](./images/kkn1.png) 

---

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

veri = veri.drop(columns=["id", "Unnamed: 32"], axis=1)

veri.diagnosis = [1 if i == "M" else 0 for i in veri.diagnosis]

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.neighbors import KNeighborsClassifier

model = KNeighborsClassifier()
model.fit(X_train, y_train)
tahmin = model.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[68  3]
#  [ 3 40]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9473684210526315

import matplotlib.pyplot as plt

basari = []

for k in range(1, 20):
    knn = KNeighborsClassifier(n_neighbors=k)
    knn.fit(X_train, y_train)
    tahmin2 = knn.predict(X_test)
    basari.append(accuracy_score(y_test, tahmin2))

print(max(basari)) # 0.9649122807017544

plt.plot(range(1, 20), basari)
plt.xlabel("K")
plt.ylabel("Başarı")
plt.show()
```

![image](./images/kkn2.png)

9 değerinin en yüksek başarıyı verdiği görülür.

```Python
knn = KNeighborsClassifier(n_neighbors=9)
knn.fit(X_train, y_train)
tahmin2 = knn.predict(X_test)
print(accuracy_score(y_test, tahmin2)) # 0.9649122807017544
```

Görüldüğü gibi en yüksek başarı puanına ulaşıldığı görülür.

# SVM (Support Vector Classifier/Machine)

![image](./images/svm1.png) 

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

veri = veri.drop(columns=["id", "Unnamed: 32"], axis=1)

veri.diagnosis = [1 if kod == "M" else 0 for kod in veri.diagnosis]

y = veri["diagnosis"]
X = veri.drop(columns="diagnosis", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.svm import SVC

svm = SVC(random_state=0)
svm.fit(X_train, y_train)
tahmin = svm.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[72  0]
#  [ 3 39]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9736842105263158

from sklearn.model_selection import GridSearchCV

parametreler = {"C": range(1, 20), "kernel": ["linear", "poly", "rbf"]}
grid = GridSearchCV(estimator=svm, param_grid=parametreler, cv=10, n_jobs=-1)
grid.fit(X_train, y_train)
print(grid.best_params_) # {'C': 9, 'kernel': 'rbf'}

svm = SVC(random_state=0, C=9, kernel="rbf")
svm.fit(X_train, y_train)
tahmin = svm.predict(X_test)

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[72  0]
#  [ 3 39]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9736842105263158
```

Sonuç değişmedi.

# Bayes Teoremi

Ders Notları;

https://web.cs.hacettepe.edu.tr/~pinar/teaching.html
https://web.cs.hacettepe.edu.tr/~pinar/courses/BBS654/index.html

🔷 **Temel Bayes Teoremi**

Temel haliyle şu şekilde tanımlanır:

$$
P(A|B) = \frac{P(B|A) \cdot P(A)}{P(B)}
$$

* **P(A|B)**: B gözlemi verildiğinde A olayının olasılığı (posterior)
* **P(B|A)**: A olayı gerçekleştiğinde B’nin olasılığı (likelihood)
* **P(A)**: A olayının ön olasılığı (prior)
* **P(B)**: B olayının toplam olasılığı (evidence)

---

🔷 **Bayes Sınıflandırıcıları (Bayesian Classifiers)**

Bayes Teoremi, özellikle **makine öğrenmesinde** çok kullanılır. Bu kullanım türleri:

- **Naive Bayes Sınıflandırıcı**

    * Özelliklerin birbirinden bağımsız olduğu varsayılır.
    * Hızlı ve basit bir sınıflandırıcıdır.
    * Metin sınıflandırma (spam filtresi gibi) alanında yaygındır.

- **Gaussian Naive Bayes**

    * Sürekli değişkenler için kullanılır.
    * Verilerin **normal dağıldığı** varsayılır.

- **Multinomial Naive Bayes**

    * Genellikle kelime sayımlarına dayalı veriyle çalışır (metin madenciliği).
    * Özellikle belge sınıflandırmada yaygındır.

- **Bernoulli Naive Bayes**

    * Özellikler ikili (0 veya 1) olduğunda kullanılır.
    * Genellikle "kelime var mı, yok mu" gibi durumlar için.

## Örnekler

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

veri = veri.drop(columns=["id", "Unnamed: 32"], axis=1)

veri.diagnosis = [1 if kod == "M" else 0 for kod in veri.diagnosis]

y = veri["diagnosis"]
X = veri.drop(columns="diagnosis", axis=1)



from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)



from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)



from sklearn.naive_bayes import GaussianNB, BernoulliNB, MultinomialNB

nbg = GaussianNB()
nbg.fit(X_train, y_train)
tahmin = nbg.predict(X_test)



from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[72  0]
#  [ 7 35]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9385964912280702
```

# Sms Spam Detection

```Python
import pandas as pd

data=pd.read_csv("spam.csv") # Hata aldık 

import chardet

with open("spam.csv", "rb") as x:
    sonuc = chardet.detect(x.read())

print(sonuc) # {'encoding': 'Windows-1252', 'confidence': 0.7269493857068697, 'language': ''}


import pandas as pd

data=pd.read_csv("spam.csv", encoding="Windows-1252") 
veri=data.copy()


veri = veri.drop(columns=["Unnamed: 2", "Unnamed: 3", "Unnamed: 4"], axis=1)
veri = veri.rename(columns={"v1": "Etiket", "v2": "Sms"})

print(veri.groupby("Etiket").count())

#          Sms
# Etiket      
# ham     4825
# spam     747

print(veri.describe())

#        Etiket                     Sms
# count    5572                    5572
# unique      2                    5169
# top       ham  Sorry, I'll call later
# freq     4825                      30


veri=veri.drop_duplicates()
print(veri.describe())

#        Etiket                                                Sms
# count    5169                                               5169
# unique      2                                               5169
# top       ham  Go until jurong point, crazy.. Available only ...
# freq     4516                                                  1


print(veri.isnull().sum())

# Etiket    0
# Sms       0
# dtype: int64

veri["Karakter Sayısı"]=veri["Sms"].apply(len) 

import matplotlib.pyplot as plt

veri.hist(column="Karakter Sayısı",by="Etiket",bins=50)
plt.show()
```

![image](./images/sms1.png)

```Python
veri.Etiket = [1 if i=="spam" else 0 for i in veri.Etiket]
veri

#        Etiket  Sms                                                 Karakter Sayısı 
#  0     0       Go until jurong point, crazy.. Available only ...   111             
#  1     0       Ok lar... Joking wif u oni...                       29              
#  2     1       Free entry in 2 a wkly comp to win FA Cup fina...   155             
#  3     0       U dun say so early hor... U c already then say...   49              
#  4     0       Nah I don't think he goes to usf, he lives aro...   61              
#  ...   ...     ...                                                 ...             
#  5567  1       This is the 2nd time we have tried 2 contact u...   161             
#  5568  0       Will l\_b going to esplanade fr home?               37              
#  5569  0       Pity, \* was in mood for that. So...any other s...  57              
#  5570  0       The guy did some bitching but I acted like i'd...   125             
#  5571  0       Rofl. Its true to its name                          26              

import re

mesaj=re.sub("[^a-zA-Z]", " ", veri["Sms"][0]) 
print(veri["Sms"][0]) # Go until jurong point, crazy.. Available only in bugis n great world la e buffet... Cine there got amore wat...
print(mesaj) # Go until jurong point  crazy   Available only in bugis n great world la e buffet    Cine there got amore wat   

def harfler(cumle):
    yer=re.compile("[^a-zA-Z]")
    return re.sub(yer, " ", cumle)

print(harfler("tuyRTF11")) # tuyRTF  

print(veri["Sms"][0]) # Go until jurong point, crazy.. Available only in bugis n great world la e buffet... Cine there got amore wat...
print(harfler(veri["Sms"][0])) # Go until jurong point  crazy   Available only in bugis n great world la e buffet    Cine there got amore wat   

def harfler(cumle):
    yer = re.compile("[^a-zA-Z]")
    temiz = re.sub(yer, " ", cumle)         # Harf olmayanları boşluk yap
    return re.sub(r"\s+", " ", temiz).strip()

print(veri["Sms"][0]) # Go until jurong point, crazy.. Available only in bugis n great world la e buffet... Cine there got amore wat...
print(harfler(veri["Sms"][0])) # Go until jurong point crazy Available only in bugis n great world la e buffet Cine there got amore wat

import nltk
nltk.download("stopwords")
from nltk.corpus import stopwords

durdurma = stopwords.words("english")
print(durdurma)
# ['a', 'about', 'above', 'after', 'again', 'against', 'ain', 'all', 'am', 'an', 'and', 'any', 'are', 'aren', "aren't", 'as', 'at', 'be', 'because', 'been', 'before', 'being', 'below', 'between', 'both', 'but', 'by', 'can', 'couldn', "couldn't", 'd', 'did', 'didn', "didn't", 'do', 'does', 'doesn', "doesn't", 'doing', 'don', "don't", 'down', 'during', 'each', 'few', 'for', 'from', 'further', 'had', 'hadn', "hadn't", 'has', 'hasn', "hasn't", 'have', 'haven', "haven't", 'having', 'he', "he'd", "he'll", 'her', 'here', 'hers', 'herself', "he's", 'him', 'himself', 'his', 'how', 'i', "i'd", 'if', "i'll", "i'm", 'in', 'into', 'is', 'isn', "isn't", 'it', "it'd", "it'll", "it's", 'its', 'itself', "i've", 'just', 'll', 'm', 'ma', 'me', 'mightn', "mightn't", 'more', 'most', 'mustn', "mustn't", 'my', 'myself', 'needn', "needn't", 'no', 'nor', 'not', 'now', 'o', 'of', 'off', 'on', 'once', 'only', 'or', 'other', 'our', 'ours', 'ourselves', 'out', 'over', 'own', 're', 's', 'same', 'shan', "shan't", 'she', "she'd", "she'll", "she's", 'should', 'shouldn', "shouldn't", "should've", 'so', 'some', 'such', 't', 'than', 'that', "that'll", 'the', 'their', 'theirs', 'them', 'themselves', 'then', 'there', 'these', 'they', "they'd", "they'll", "they're", "they've", 'this', 'those', 'through', 'to', 'too', 'under', 'until', 'up', 've', 'very', 'was', 'wasn', "wasn't", 'we', "we'd", "we'll", "we're", 'were', 'weren', "weren't", "we've", 'what', 'when', 'where', 'which', 'while', 'who', 'whom', 'why', 'will', 'with', 'won', "won't", 'wouldn', "wouldn't", 'y', 'you', "you'd", "you'll", 'your', "you're", 'yours', 'yourself', 'yourselves', "you've"]

spam = []
ham = []
tumcumleler = []

for i in range(len(veri["Sms"].values)):
    r1 = veri["Sms"].values[i]
    r2 = veri["Etiket"].values[i]

    temizcumle = []
    cumleler = harfler(r1)
    cumleler = cumleler.lower()

    for kelimeler in cumleler.split():
        temizcumle.append(kelimeler)

    if r2 == 1:
        spam.append(cumleler)
    else:
        ham.append(cumleler)
        
    tumcumleler.append(" ".join(temizcumle))

veri["Yeni Sms"] = tumcumleler

veri

#  Etiket  Sms                                                 Karakter Sayısı  Yeni Sms                                          
#  ------  --------------------------------------------------  ---------------  ------------------------------------------------- 
#  0       Go until jurong point, crazy.. Available only ...   111              go until jurong point crazy available only in ... 
#  1       Ok lar... Joking wif u oni...                       29               ok lar joking wif u oni                           
#  2       Free entry in 2 a wkly comp to win FA Cup fina...   155              free entry in a wkly comp to win fa cup final ... 
#  3       U dun say so early hor... U c already then say...   49               u dun say so early hor u c already then say       
#  4       Nah I don't think he goes to usf, he lives aro...   61               nah i don t think he goes to usf he lives arou    
#  ...     ...                                                 ...              ...                                               
#  5567    This is the 2nd time we have tried 2 contact u...   161              this is the nd time we have tried contact u u     
#  5568    Will l\_b going to esplanade fr home?               37               will b going to esplanade fr home                 
#  5569    Pity, \* was in mood for that. So...any other s...  57               pity was in mood for that so any other suggest    
#  5570    The guy did some bitching but I acted like i'd...   125              the guy did some bitching but i acted like i d    
#  5571    Rofl. Its true to its name                          26               rofl its true to its name                         

veri = veri.drop(columns=["Sms", "Karakter Sayısı"], axis=1)
veri

#  Etiket  Yeni Sms                                           
#  ------  -------------------------------------------------- 
#  0       go until jurong point crazy available only in ...  
#  1       ok lar joking wif u oni                            
#  2       free entry in a wkly comp to win fa cup final ...  
#  3       u dun say so early hor u c already then say        
#  4       nah i don t think he goes to usf he lives arou ... 
#  ...     ...                                                
#  5567    this is the nd time we have tried contact u u ...  
#  5568    will b going to esplanade fr home                  
#  5569    pity was in mood for that so any other suggest ... 
#  5570    the guy did some bitching but i acted like i d ... 
#  5571    rofl its true to its name                          

from sklearn.feature_extraction.text import CountVectorizer

cv = CountVectorizer()
x = cv.fit_transform(veri["Yeni Sms"]).toarray()

y = veri["Etiket"]
X = x

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.naive_bayes import MultinomialNB

model = MultinomialNB()
model.fit(X_train, y_train)
tahmin = model.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[874  15]
#  [  7 138]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9787234042553191

import numpy as np

for i in np.arange(0.0, 1.1, 0.1):
    model = MultinomialNB(alpha=i)
    model.fit(X_train, y_train)
    tahmin = model.predict(X_test)
    skor = accuracy_score(y_test, tahmin)
    print("Alfa {} değeri için Skor: {}".format(round(i, 1), round(skor * 100, 2)))

# Alfa 0.0 değeri için Skor: 85.98
# Alfa 0.1 değeri için Skor: 98.07
# Alfa 0.2 değeri için Skor: 97.97
# Alfa 0.3 değeri için Skor: 97.97
# Alfa 0.4 değeri için Skor: 97.87
# Alfa 0.5 değeri için Skor: 97.87
# Alfa 0.6 değeri için Skor: 97.87
# Alfa 0.7 değeri için Skor: 97.78
# Alfa 0.8 değeri için Skor: 97.87
# Alfa 0.9 değeri için Skor: 97.87
# Alfa 1.0 değeri için Skor: 97.87

import numpy as np

for i in np.arange(0.0, 0.2, 0.01):
    model = MultinomialNB(alpha=i)
    model.fit(X_train, y_train)
    tahmin = model.predict(X_test)
    skor = accuracy_score(y_test, tahmin)
    print("Alfa {} değeri için Skor: {}".format(i, skor * 100))

# Alfa 0.0 değeri için Skor: 85.97678916827853
# Alfa 0.01 değeri için Skor: 97.48549323017409
# Alfa 0.02 değeri için Skor: 97.678916827853
# Alfa 0.03 değeri için Skor: 97.87234042553192
# Alfa 0.04 değeri için Skor: 97.87234042553192
# Alfa 0.05 değeri için Skor: 98.06576402321083
# Alfa 0.06 değeri için Skor: 98.16247582205028
# Alfa 0.07 değeri için Skor: 98.16247582205028
# Alfa 0.08 değeri için Skor: 98.16247582205028
# Alfa 0.09 değeri için Skor: 98.16247582205028
# Alfa 0.1 değeri için Skor: 98.06576402321083
# Alfa 0.11 değeri için Skor: 98.06576402321083
# Alfa 0.12 değeri için Skor: 98.06576402321083
# Alfa 0.13 değeri için Skor: 98.06576402321083
# Alfa 0.14 değeri için Skor: 98.06576402321083
# Alfa 0.15 değeri için Skor: 97.96905222437138
# Alfa 0.16 değeri için Skor: 98.06576402321083
# Alfa 0.17 değeri için Skor: 98.06576402321083
# Alfa 0.18 değeri için Skor: 98.06576402321083
# Alfa 0.19 değeri için Skor: 97.96905222437138

model = MultinomialNB(alpha=0.06)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[875  14]
#  [  5 140]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9816247582205029
```

# Karar Ağacı

Dataset: [Kanser dataset](https://www.kaggle.com/code/kanncaa1/logistic-regression-implementation/data)

```Python
import pandas as pd

data=pd.read_csv("data.csv")
veri=data.copy()

veri = veri.drop(columns=["id", "Unnamed: 32"], axis=1)

veri.diagnosis = [1 if kod == "M" else 0 for kod in veri.diagnosis]

y = veri["diagnosis"]
X = veri.drop(columns="diagnosis", axis=1)



from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)



from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)



from sklearn.tree import DecisionTreeClassifier

model = DecisionTreeClassifier()
model.fit(X_train, y_train)
tahmin = model.predict(X_test)



from sklearn.metrics import confusion_matrix, accuracy_score, classification_report, roc_curve, roc_auc_score
import matplotlib.pyplot as plt

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[69  3]
#  [ 6 36]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9210526315789473
```

![image](./images/krr1.png) 

```Python
from sklearn.tree import plot_tree
import matplotlib.pyplot as plt

plt.figure(figsize=(40, 20))
plot_tree(model, filled=True, fontsize=15)
plt.show()
```

![image](./images/krr2.png)

```Python
parametreler = {
    "criterion": ["gini", "entropy", "log_loss"],            # Hedef fonksiyonu
    "max_leaf_nodes": range(2, 10),                          # Maksimum yaprak sayısı
    "max_depth": range(2, 10),                               # Maksimum derinlik
    "min_samples_split": range(2, 10),                       # Dallanma için min örnek sayısı
    "min_samples_leaf": range(2, 10)                         # Yaprakta kalması gereken min örnek sayısı
}

grid = GridSearchCV(model, param_grid=parametreler, cv=10, n_jobs=-1)

grid.fit(X_train, y_train)
print(grid.best_params_) # {'criterion': 'entropy', 'max_depth': 4, 'max_leaf_nodes': 8, 'min_samples_leaf': 6, 'min_samples_split': 2}



model = DecisionTreeClassifier(random_state=0, criterion='entropy', max_depth=4, max_leaf_nodes=8, min_samples_leaf=6, min_samples_split=2)
model.fit(X_train, y_train)
tahmin = model.predict(X_test)



from sklearn.metrics import confusion_matrix, accuracy_score, classification_report, roc_curve, roc_auc_score
import matplotlib.pyplot as plt

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[72  0]
#  [ 8 34]]

acs = accuracy_score(y_test, tahmin)
print(acs) # 0.9298245614035088
```

---

Dataset kaggle diabetes Mehmet Aktürk

```Python
import pandas as pd

data=pd.read_csv("diabetes.csv")
veri=data.copy()
veri

#  Index  Pregnancies  Glucose  BloodPressure  SkinThickness  Insulin   BMI   DiabetesPedigreeFunction  Age  Outcome 
# ----------------------------------------------------------------------------------------------------------------------
#    0         6         148          72              35           0     33.6             0.627             50     1    
#    1         1         85           66              29           0     26.6             0.351             31     0    
#    2         8         183          64               0           0     23.3             0.672             32     1    
#    3         1         89           66              23          94     28.1             0.167             21     0    
#    4         0         137          40              35         168     43.1             2.288             33     1    
#   ...       ...        ...         ...             ...         ...     ...              ...              ...    ...   
#  763        10         101          76              48         180     32.9             0.171             63     0    
#  764         2         122          70              27           0     36.8             0.340             27     0    
#  765         5         121          72              23         112     26.2             0.245             30     0    
#  766         1         126          60               0           0     30.1             0.349             47     1    
#  767         1         93           70              31           0     30.4             0.315             23     0    

veri.info()

# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 768 entries, 0 to 767
# Data columns (total 9 columns):
#  #   Column                    Non-Null Count  Dtype  
# ---  ------                    --------------  -----  
#  0   Pregnancies               768 non-null    int64  
#  1   Glucose                   768 non-null    int64  
#  2   BloodPressure             768 non-null    int64  
#  3   SkinThickness             768 non-null    int64  
#  4   Insulin                   768 non-null    int64  
#  5   BMI                       768 non-null    float64
#  6   DiabetesPedigreeFunction  768 non-null    float64
#  7   Age                       768 non-null    int64  
#  8   Outcome                   768 non-null    int64  
# dtypes: float64(2), int64(7)
# memory usage: 54.1 KB

y = veri["Outcome"]
X = veri.drop(columns="Outcome", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.linear_model import LogisticRegression
from sklearn.neighbors import KNeighborsClassifier
from sklearn.svm import SVC
from sklearn.naive_bayes import GaussianNB
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score

def modeller(model):
    model.fit(X_train, y_train)
    tahmin = model.predict(X_test)
    skor = accuracy_score(y_test, tahmin)
    return round(skor * 100, 2)

models = []

models.append(("Log Regresyon", LogisticRegression(random_state=0)))
models.append(("KNN", KNeighborsClassifier()))
models.append(("SVC", SVC(random_state=0)))
models.append(("Bayes", GaussianNB()))
models.append(("Karar Ağacı", DecisionTreeClassifier(random_state=0)))

modelad = []
basari = []

for i in models:
    modelad.append(i[0])
    basari.append(modeller(i[1]))

a = list(zip(modelad, basari))
sonuc = pd.DataFrame(a, columns=["Model", "Skor"])
sonuc

#  Model          Skor  
#  -------------  ----- 
#  Log Regresyon  97.37 
#  KNN            94.74 
#  SVC            98.25 
#  Bayes          96.49 
#  Karar Ağacı    93.86 


```

# Random Forest

```Python
import pandas as pd

data=pd.read_csv("diabetes.csv")
veri=data.copy()

veri.info()

y = veri["Outcome"]
X = veri.drop(columns="Outcome", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from sklearn.ensemble import RandomForestClassifier

model=RandomForestClassifier(random_state=0)
model.fit(X_train, y_train)
tahmin=model.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[81 18]
#  [18 37]]

accuracy_score(y_test, tahmin) # 0.7662337662337663



from sklearn.model_selection import GridSearchCV

parametreler = {
    "criterion": ["gini", "entropy"],
    "max_depth": [2, 5, 10],
    "min_samples_split": [2, 5, 10],
    "n_estimators": [50, 200, 500, 1000]
}

grid = GridSearchCV(model, param_grid=parametreler, cv=10, n_jobs=-1)
grid.fit(X_train,y_train)
grid.best_params_ {'criterion': 'entropy', 'max_depth': 10, 'min_samples_split': 5, 'n_estimators': 1000}

model=RandomForestClassifier(random_state=0, criterion='entropy', max_depth=10, min_samples_split=5, n_estimators=1000)
model.fit(X_train, y_train)
tahmin=model.predict(X_test)

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[80 19]
#  [19 36]]

accuracy_score(y_test, tahmin) # 0.7532467532467533
```

![image](./images/rndfr1.png)

```Python
önem = pd.DataFrame({"Önem": model.feature_importances_}, index=X.columns)
önem.sort_values(by="Önem", axis=0, ascending=True).plot(kind="barh", color="blue")
plt.title("Değişken Önem Seviyeleri")
plt.show()
```

![image](./images/rndfr2.png) 

# XGBoost 

```Python
from xgboost import XGBClassifier
import pandas as pd

data=pd.read_csv("diabetes.csv")
veri=data.copy()

y = veri["Outcome"]
X = veri.drop(columns="Outcome", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

modelxgb = XGBClassifier()
modelxgb.fit(X_train, y_train)
tahminxgb = modelxgb.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahminxgb)
print(cm)
# [[72 27]
#  [16 39]]

accuracy_score(y_test, tahminxgb) # 0.7207792207792207

from sklearn.naive_bayes import GaussianNB

nbg = GaussianNB()
nbg.fit(X_train, y_train)
tahminnbg = nbg.predict(X_test)

cm = confusion_matrix(y_test, tahminnbg)
print(cm)
# [[79 20]
#  [16 39]]

accuracy_score(y_test, tahminnbg) # 0.7662337662337663

from sklearn.model_selection import GridSearchCV

parametreler = {
    "max_depth": [3, 5, 7],
    "subsample": [0.2, 0.5, 0.7],
    "n_estimators": [500, 1000, 2000],
    "learning_rate": [0.2, 0.5, 0.7]
}

grid = GridSearchCV(modelxgb, param_grid=parametreler, cv=10, n_jobs=-1)
grid.fit(X_train, y_train)
print(grid.best_params_) # {'learning_rate': 0.7, 'max_depth': 7, 'n_estimators': 500, 'subsample': 0.7}

modelxgb = XGBClassifier(learning_rate=0.7, max_depth=7, n_estimators=500, subsample=0.7)
modelxgb.fit(X_train, y_train)
tahminxgb = modelxgb.predict(X_test)

cm = confusion_matrix(y_test, tahminxgb)
print(cm)
# [[71 28]
#  [18 37]]

accuracy_score(y_test, tahminxgb) # 0.7012987012987013
```

# LightGBM 

Satır sayısı 10000 ve üzeri olduğunda genellikle tercih edilir. Veri seti az satırlıysa genelde performansı düştüğü için tercih edilmez.

```Python
from lightgbm import LGBMClassifier
import pandas as pd

data=pd.read_csv("diabetes.csv")
veri=data.copy()

y = veri["Outcome"]
X = veri.drop(columns="Outcome", axis=1)

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

modellgbm = LGBMClassifier()
modellgbm.fit(X_train, y_train)
tahmin = modellgbm.predict(X_test)

from sklearn.metrics import confusion_matrix, accuracy_score

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[72 27]
#  [18 37]]

accuracy_score(y_test, tahmin) # 0.7077922077922078

parametreler = {
    "learning_rate": [0.001, 0.01, 0.1],
    "n_estimators": [200, 500, 1000],
    "max_depth": [3, 5, 7],
    "subsample": [0.6, 0.8, 1.0]
}

from sklearn.model_selection import GridSearchCV

grid = GridSearchCV(modellgbm, param_grid=parametreler, cv=10, n_jobs=-1)
grid.fit(X_train, y_train)
print(grid.best_params_) # {'learning_rate': 0.01, 'max_depth': 3, 'n_estimators': 1000, 'subsample': 0.6}

modellgbm = LGBMClassifier(learning_rate=0.01, max_depth=3, n_estimators=1000, subsample=0.6)
modellgbm.fit(X_train, y_train)
tahmin = modellgbm.predict(X_test)

modellgbm = LGBMClassifier(learning_rate=0.01, max_depth=3, n_estimators=1000, subsample=0.6)
modellgbm.fit(X_train, y_train)
tahmin = modellgbm.predict(X_test)

cm = confusion_matrix(y_test, tahmin)
print(cm)
# [[79 20]
#  [18 37]]

accuracy_score(y_test, tahmin) # 0.7532467532467533
```

Doğruluk skoru 70'ten 75'e çıkarılmıştır. 

# Accuracy Paradox

Eğer TP veya TN üzerinde tahminler yoğunlaşıyorsa bu istenmeyen şekilde bizi yönlendirir.

# Müşteri Kayıp Analiz Modeli

Dataset: Telco Customer Churn Kaggle

```Python
import pandas as pd

data=pd.read_csv("telco.csv")
veri=data.copy()

veri = veri.drop(columns="customerID", axis=1)

veri = veri.rename({
    "gender": "Cinsiyet",
    "SeniorCitizen": "65 Yaş Üstü",
    "Partner": "Medeni Durum",
    "Dependents": "Bakma Sorumluluğu",
    "tenure": "Müşteri Olma Süresi (Ay)",
    "PhoneService": "Ev Telefonu Aboneliği",
    "MultipleLines": "Birden Fazla Abonelik Durumu",
    "InternetService": "İnternet Aboneliği",
    "OnlineSecurity": "Güvenlik Hizmeti Aboneliği",
    "OnlineBackup": "Yedekleme Hizmeti Aboneliği",
    "DeviceProtection": "Ekipman Güvenlik Aboneliği",
    "TechSupport": "Teknik Destek Aboneliği",
    "StreamingTV": "IP Tv Aboneliği",
    "StreamingMovies": "Film Aboneliği",
    "Contract": "Sözleşme Süresi",
    "PaperlessBilling": "Online Fatura (Kağıtsız)",
    "PaymentMethod": "Ödeme Şekli",
    "MonthlyCharges": "Aylık Ücret",
    "TotalCharges": "Toplam Ücret",
    "Churn": "Kayıp Durumu"
},axis=1)

for i in veri.columns:
    print(i)
    print(veri[i].dtype)
    print(veri[i].unique())
    print()

# Cinsiyet
# object
# ['Female' 'Male']

# 65 Yaş Üstü
# int64
# [0 1]

# Medeni Durum
# object
# ['Yes' 'No']

# Bakma Sorumluluğu
# object
# ['No' 'Yes']

# Müşteri Olma Süresi (Ay)
# int64
# [ 1 34  2 45  8 22 10 28 62 13 16 58 49 25 69 52 71 21 12 30 47 72 17 27
#   5 46 11 70 63 43 15 60 18 66  9  3 31 50 64 56  7 42 35 48 29 65 38 68
#  32 55 37 36 41  6  4 33 67 23 57 61 14 20 53 40 59 24 44 19 54 51 26  0
#  39]

# Ev Telefonu Aboneliği
# object
# ['No' 'Yes']

# Birden Fazla Abonelik Durumu
# object
# ['No phone service' 'No' 'Yes']

# İnternet Aboneliği
# object
# ['DSL' 'Fiber optic' 'No']

# Güvenlik Hizmeti Aboneliği
# object
# ['No' 'Yes' 'No internet service']

# Yedekleme Hizmeti Aboneliği
# object
# ['Yes' 'No' 'No internet service']

# Ekipman Güvenlik Aboneliği
# object
# ['No' 'Yes' 'No internet service']

# Teknik Destek Aboneliği
# object
# ['No' 'Yes' 'No internet service']

# IP Tv Aboneliği
# object
# ['No' 'Yes' 'No internet service']

# Film Aboneliği
# object
# ['No' 'Yes' 'No internet service']

# Sözleşme Süresi
# object
# ['Month-to-month' 'One year' 'Two year']

# Online Fatura (Kağıtsız)
# object
# ['Yes' 'No']

# Ödeme Şekli
# object
# ['Electronic check' 'Mailed check' 'Bank transfer (automatic)'
#  'Credit card (automatic)']

# Aylık Ücret
# float64
# [29.85 56.95 53.85 ... 63.1  44.2  78.7 ]

# Toplam Ücret
# object
# ['29.85' '1889.5' '108.15' ... '346.45' '306.6' '6844.5']

# Kayıp Durumu
# object
# ['No' 'Yes']

# Cinsiyet
veri["Cinsiyet"] = veri["Cinsiyet"].map({"Male": "Erkek", "Female": "Kadın"})

# 65 Yaş Üstü
veri["65 Yaş Üstü"] = veri["65 Yaş Üstü"].map({1: "Evet", 0: "Hayır"})

# Medeni Durum
veri["Medeni Durum"] = veri["Medeni Durum"].map({"Yes": "Evli", "No": "Bekar"})

# Bakma Sorumluluğu
veri["Bakma Sorumluluğu"] = veri["Bakma Sorumluluğu"].map({"Yes": "Var", "No": "Yok"})

# Ev Telefonu Aboneliği
veri["Ev Telefonu Aboneliği"] = veri["Ev Telefonu Aboneliği"].map({"Yes": "Var", "No": "Yok"})

# Birden Fazla Abonelik Durumu
veri["Birden Fazla Abonelik Durumu"] = veri["Birden Fazla Abonelik Durumu"].map({"Yes": "Var", "No": "Yok", "No phone service": "Yok"})

# İnternet Aboneliği
veri["İnternet Aboneliği"] = veri["İnternet Aboneliği"].map({'DSL': "Var", 'Fiber optic': "Var",  "No": "Yok"})

# Güvenlik Hizmeti Aboneliği
veri["Güvenlik Hizmeti Aboneliği"] = veri["Güvenlik Hizmeti Aboneliği"].map({"Yes": "Var", "No internet service":"Yok", "No": "Yok"})

# Yedekleme Hizmeti Aboneliği
veri["Yedekleme Hizmeti Aboneliği"] = veri["Yedekleme Hizmeti Aboneliği"].map({"Yes": "Var", "No": "Yok", "No internet service": "Yok"})

# Ekipman Güvenlik Aboneliği
veri["Ekipman Güvenlik Aboneliği"] = veri["Ekipman Güvenlik Aboneliği"].map({"Yes": "Var", "No": "Yok", "No internet service": "Yok"})

# Teknik Destek Aboneliği
veri["Teknik Destek Aboneliği"] = veri["Teknik Destek Aboneliği"].map({"Yes": "Var", "No": "Yok", "No internet service": "Yok"})

# IP Tv Aboneliği
veri["IP Tv Aboneliği"] = veri["IP Tv Aboneliği"].map({"Yes": "Var", "No": "Yok", "No internet service": "Yok"})

# Film Aboneliği
veri["Film Aboneliği"] = veri["Film Aboneliği"].map({"Yes": "Var", "No": "Yok", "No internet service": "Yok"})

# Sözleşme Süresi
veri["Sözleşme Süresi"] = veri["Sözleşme Süresi"].map({
    "One year": "1 Yıllık",
    "Two year": "2 Yıllık",
    "Month-to-month": "1 Aylık"
})

# Online Fatura (Kağıtsız)
veri["Online Fatura (Kağıtsız)"] = veri["Online Fatura (Kağıtsız)"].map({"Yes": "Evet", "No": "Hayır"})

# Ödeme Şekli
veri["Ödeme Şekli"] = veri["Ödeme Şekli"].map({
    "Electronic check": "Elektronik",
    "Mailed check": "Mail",
    "Bank transfer (automatic)": "Havale",
    "Credit card (automatic)": "Kredi Kartı"
})

# Kayıp Durumu
veri["Kayıp Durumu"] = veri["Kayıp Durumu"].map({"Yes": "Evet", "No": "Hayır"})



for i in veri.columns:
    print(i)
    print(veri[i].dtype)
    print(veri[i].unique())
    print()

# Cinsiyet
# object
# ['Kadın' 'Erkek']

# 65 Yaş Üstü
# object
# ['Hayır' 'Evet']

# Medeni Durum
# object
# ['Evli' 'Bekar']

# Bakma Sorumluluğu
# object
# ['Yok' 'Var']

# Müşteri Olma Süresi (Ay)
# int64
# [ 1 34  2 45  8 22 10 28 62 13 16 58 49 25 69 52 71 21 12 30 47 72 17 27
#   5 46 11 70 63 43 15 60 18 66  9  3 31 50 64 56  7 42 35 48 29 65 38 68
#  32 55 37 36 41  6  4 33 67 23 57 61 14 20 53 40 59 24 44 19 54 51 26  0
#  39]

# Ev Telefonu Aboneliği
# object
# ['Yok' 'Var']

# Birden Fazla Abonelik Durumu
# object
# ['Yok' 'Var']

# İnternet Aboneliği
# object
# ['Var' 'Yok']

# Güvenlik Hizmeti Aboneliği
# object
# ['Yok' 'Var']

# Yedekleme Hizmeti Aboneliği
# object
# ['Var' 'Yok']

# Ekipman Güvenlik Aboneliği
# object
# ['Yok' 'Var']

# Teknik Destek Aboneliği
# object
# ['Yok' 'Var']

# IP Tv Aboneliği
# object
# ['Yok' 'Var']

# Film Aboneliği
# object
# ['Yok' 'Var']

# Sözleşme Süresi
# object
# ['1 Aylık' '1 Yıllık' '2 Yıllık']

# Online Fatura (Kağıtsız)
# object
# ['Evet' 'Hayır']

# Ödeme Şekli
# object
# ['Elektronik' 'Mail' 'Havale' 'Kredi Kartı']

# Aylık Ücret
# float64
# [29.85 56.95 53.85 ... 63.1  44.2  78.7 ]

# Toplam Ücret
# object
# ['29.85' '1889.5' '108.15' ... '346.45' '306.6' '6844.5']

# Kayıp Durumu
# object
# ['Hayır' 'Evet']

veri["Toplam Ücret"] = pd.to_numeric(veri["Toplam Ücret"], errors="coerce")

veri.info()

# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 7043 entries, 0 to 7042
# Data columns (total 20 columns):
#  #   Column                        Non-Null Count  Dtype  
# ---  ------                        --------------  -----  
#  0   Cinsiyet                      7043 non-null   object 
#  1   65 Yaş Üstü                   7043 non-null   object 
#  2   Medeni Durum                  7043 non-null   object 
#  3   Bakma Sorumluluğu             7043 non-null   object 
#  4   Müşteri Olma Süresi (Ay)      7043 non-null   int64  
#  5   Ev Telefonu Aboneliği         7043 non-null   object 
#  6   Birden Fazla Abonelik Durumu  7043 non-null   object 
#  7   İnternet Aboneliği            7043 non-null   object 
#  8   Güvenlik Hizmeti Aboneliği    7043 non-null   object 
#  9   Yedekleme Hizmeti Aboneliği   7043 non-null   object 
#  10  Ekipman Güvenlik Aboneliği    7043 non-null   object 
#  11  Teknik Destek Aboneliği       7043 non-null   object 
#  12  IP Tv Aboneliği               7043 non-null   object 
#  13  Film Aboneliği                7043 non-null   object 
#  14  Sözleşme Süresi               7043 non-null   object 
#  15  Online Fatura (Kağıtsız)      7043 non-null   object 
#  16  Ödeme Şekli                   7043 non-null   object 
#  17  Aylık Ücret                   7043 non-null   float64
#  18  Toplam Ücret                  7032 non-null   float64
#  19  Kayıp Durumu                  7043 non-null   object 
# dtypes: float64(2), int64(1), object(17)
# memory usage: 1.1+ MB

veri.isnull().sum()

# Cinsiyet                         0
# 65 Yaş Üstü                      0
# Medeni Durum                     0
# Bakma Sorumluluğu                0
# Müşteri Olma Süresi (Ay)         0
# Ev Telefonu Aboneliği            0
# Birden Fazla Abonelik Durumu     0
# İnternet Aboneliği               0
# Güvenlik Hizmeti Aboneliği       0
# Yedekleme Hizmeti Aboneliği      0
# Ekipman Güvenlik Aboneliği       0
# Teknik Destek Aboneliği          0
# IP Tv Aboneliği                  0
# Film Aboneliği                   0
# Sözleşme Süresi                  0
# Online Fatura (Kağıtsız)         0
# Ödeme Şekli                      0
# Aylık Ücret                      0
# Toplam Ücret                    11
# Kayıp Durumu                     0
# dtype: int64

# Toplam ücretin aylık ücret ile müşteri olma süresi çarpımıyla doldurabilir miyiz diye düşünüyoruz. 

(veri["Toplam Ücret"] - veri["Müşteri Olma Süresi (Ay)"]*veri["Aylık Ücret"]).max() # 373.2500000000009
(veri["Toplam Ücret"] - veri["Müşteri Olma Süresi (Ay)"]*veri["Aylık Ücret"]).min() # -370.84999999999945

# Müşteri olma süresi 0 olursa hesabımızı yanlış etkileyebilir.

veri[veri["Müşteri Olma Süresi (Ay)"] == 0]
```

|      | Cinsiyet | 65 Yaş Üstü | Medeni Durum | Bakma Sorumluluğu | Müşteri Olma Süresi (Ay) | Ev Telefonu Aboneliği | Birden Fazla Abonelik Durumu | İnternet Aboneliği | Güvenlik Hizmeti Aboneliği | Yedekleme Hizmeti Aboneliği | Ekipman Güvenlik Aboneliği | Teknik Destek Aboneliği | IP Tv Aboneliği | Film Aboneliği | Sözleşme Süresi | Online Fatura (Kağıtsız) | Ödeme Şekli | Aylık Ücret | Toplam Ücret | Kayıp Durumu |
| ---- | -------- | ----------- | ------------ | ----------------- | ------------------------ | --------------------- | ---------------------------- | ------------------ | -------------------------- | --------------------------- | -------------------------- | ----------------------- | --------------- | -------------- | --------------- | ------------------------ | ----------- | ----------- | ------------ | ------------ |
| 488  | Kadın    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Var                | Yok                        | Yok                         | Yok                        | Var                     | Var             | Yok            | 2 Yıllık        | Evet                     | Havale      | 52.55       | NaN          | Hayır        |
| 753  | Erkek    | Hayır       | Bekar        | Var               | 0                        | Var                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 20.25       | NaN          | Hayır        |
| 936  | Kadın    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Var                | Var                        | Yok                         | Var                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 80.85       | NaN          | Hayır        |
| 1082 | Erkek    | Hayır       | Evli         | Var               | 0                        | Var                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Var                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 23.75       | NaN          | Hayır        |
| 1340 | Kadın    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Var                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Kredi Kartı | 56.05       | NaN          | Hayır        |
| 3331 | Erkek    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 19.85       | NaN          | Hayır        |
| 3826 | Erkek    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 25.35       | NaN          | Hayır        |
| 4380 | Erkek    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 20.00       | NaN          | Hayır        |
| 5218 | Erkek    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 19.75       | NaN          | Hayır        |
| 6670 | Kadın    | Hayır       | Evli         | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Hayır                    | Mail        | 33.45       | NaN          | Hayır        |
| 6754 | Erkek    | Hayır       | Bekar        | Var               | 0                        | Yok                   | Yok                          | Yok                | Yok                        | Yok                         | Yok                        | Yok                     | Yok             | Yok            | 2 Yıllık        | Evet                     | Havale      | 61.90       | NaN          | Hayır        |

Aylık ücreti 0 olanlar da hesaplamada sorun çıkartabilir. Onlara bakalım;

```Python
veri[veri["Aylık Ücret"] == 0]

# yok

# Eksik değerleri 0 ay abonelikle alakalı olduğu için ve çok az kayıtta olduğu için siliyoruz.
veri = veri.dropna()
veri.isnull().sum()

import matplotlib.pyplot as plt

plt.boxplot(veri["Müşteri Olma Süresi (Ay)"])
plt.show()
```

![image](./images/mstr1.png) 

```Python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()
degisken = veri.select_dtypes(include="object").columns
veri.update(veri[degisken].apply(le.fit_transform))

veri["Kayıp Durumu"] = [1 if kod == 0 else 0 for kod in veri["Kayıp Durumu"]]

y = veri["Kayıp Durumu"]
X = veri.drop(columns="Kayıp Durumu", axis=1)



from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

from sklearn.preprocessing import StandardScaler

sc=StandardScaler()
X_train=sc.fit_transform(X_train)
X_test=sc.transform(X_test)

from lazypredict.Supervised import LazyClassifier

clf = LazyClassifier()
modeller, tahmin = clf.fit(X_train, X_test, y_train, y_test)
modeller

#  Model                          Accuracy  Balanced Accuracy  ROC AUC  F1 Score  Time Taken 
#  -----------------------------  --------  -----------------  -------  --------  ---------- 
#  NearestCentroid                0.71      0.74               0.74     0.73      0.05       
#  SGDClassifier                  0.76      0.74               0.74     0.77      0.14       
#  GaussianNB                     0.72      0.74               0.74     0.74      0.07       
#  BernoulliNB                    0.74      0.73               0.73     0.75      0.06       
#  LogisticRegression             0.80      0.73               0.73     0.80      0.08       
#  PassiveAggressiveClassifier    0.70      0.73               0.73     0.72      0.06       
#  LinearSVC                      0.80      0.72               0.72     0.79      0.07       
#  LinearDiscriminantAnalysis     0.79      0.72               0.72     0.79      0.08       
#  CalibratedClassifierCV         0.80      0.72               0.72     0.80      0.14       
#  RidgeClassifier                0.79      0.71               0.71     0.79      0.07       
#  RidgeClassifierCV              0.79      0.71               0.71     0.79      0.06       
#  AdaBoostClassifier             0.79      0.71               0.71     0.79      0.45       
#  KNeighborsClassifier           0.76      0.70               0.70     0.76      0.08       
#  LGBMClassifier                 0.78      0.70               0.70     0.77      0.31       
#  SVC                            0.79      0.70               0.70     0.78      1.52       
#  XGBClassifier                  0.76      0.69               0.69     0.76      0.18       
#  RandomForestClassifier         0.79      0.69               0.69     0.78      0.87       
#  NuSVC                          0.79      0.68               0.68     0.78      1.77       
#  ExtraTreesClassifier           0.77      0.68               0.68     0.76      0.90       
#  BaggingClassifier              0.77      0.67               0.67     0.76      0.36       
#  QuadraticDiscriminantAnalysis  0.64      0.67               0.67     0.67      0.06       
#  LabelPropagation               0.73      0.66               0.66     0.73      1.04       
#  LabelSpreading                 0.73      0.66               0.66     0.73      1.49       
#  ExtraTreeClassifier            0.72      0.65               0.65     0.72      0.07       
#  DecisionTreeClassifier         0.71      0.64               0.64     0.71      0.09       
#  Perceptron                     0.76      0.60               0.60     0.72      0.07       
#  DummyClassifier                0.73      0.50               0.50     0.62      0.06       

sıra=modeller.sort_values(by="Accuracy", ascending=True)
sıra
#tablo sırası farklı

#  Model                          Accuracy  Balanced Accuracy  ROC AUC  F1 Score  Time Taken 
#  -----------------------------  --------  -----------------  -------  --------  ---------- 
#  CalibratedClassifierCV         0.80      0.72               0.72     0.80      0.14       
#  LogisticRegression             0.80      0.73               0.73     0.80      0.08       
#  LinearSVC                      0.80      0.72               0.72     0.79      0.07       
#  RidgeClassifier                0.79      0.71               0.71     0.79      0.07       
#  RidgeClassifierCV              0.79      0.71               0.71     0.79      0.06       
#  LinearDiscriminantAnalysis     0.79      0.72               0.72     0.79      0.08       
#  NuSVC                          0.79      0.68               0.68     0.78      1.77       
#  AdaBoostClassifier             0.79      0.71               0.71     0.79      0.45       
#  SVC                            0.79      0.70               0.70     0.78      1.52       
#  RandomForestClassifier         0.79      0.69               0.69     0.78      0.87       
#  LGBMClassifier                 0.78      0.70               0.70     0.77      0.31       
#  BaggingClassifier              0.77      0.67               0.67     0.76      0.36       
#  ExtraTreesClassifier           0.77      0.68               0.68     0.76      0.90       
#  Perceptron                     0.76      0.60               0.60     0.72      0.07       
#  XGBClassifier                  0.76      0.69               0.69     0.76      0.18       
#  KNeighborsClassifier           0.76      0.70               0.70     0.76      0.08       
#  SGDClassifier                  0.76      0.74               0.74     0.77      0.14       
#  BernoulliNB                    0.74      0.73               0.73     0.75      0.06       
#  DummyClassifier                0.73      0.50               0.50     0.62      0.06       
#  LabelPropagation               0.73      0.66               0.66     0.73      1.04       
#  LabelSpreading                 0.73      0.66               0.66     0.73      1.49       
#  GaussianNB                     0.72      0.74               0.74     0.74      0.07       
#  ExtraTreeClassifier            0.72      0.65               0.65     0.72      0.07       
#  DecisionTreeClassifier         0.71      0.64               0.64     0.71      0.09       
#  NearestCentroid                0.71      0.74               0.74     0.73      0.05       
#  PassiveAggressiveClassifier    0.70      0.73               0.73     0.72      0.06       
#  QuadraticDiscriminantAnalysis  0.64      0.67               0.67     0.67      0.06       

plt.barh(sıra.index, sıra["Accuracy"])
plt.show()
```
![image](./images/mstr2.png)

```Python
from sklearn.svm import LinearSVC, SVC
from sklearn.linear_model import RidgeClassifier, LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from lightgbm import LGBMClassifier
from xgboost import XGBClassifier
from sklearn.model_selection import GridSearchCV
from sklearn.metrics import accuracy_score

models=["LinearSVC","SVC","Ridge","Logistic","RandomForest","LGBM","XGBM"]

sınıflar=[LinearSVC(random_state=0),SVC(random_state=0),RidgeClassifier(random_state=0),LogisticRegression(random_state=0),RandomForestClassifier(random_state=0),LGBMClassifier(random_state=0),XGBClassifier()]

parametreler={
    models[0]:{"C":[0.1,1,10,100],"penalty":["l1","l2"]},
    models[1]:{"kernel":["linear","rbf"],"C":[0.1,1],"gamma":[0.01,0.001]},
    models[2]:{"alpha":[0.1,1.0]},
    models[3]:{"C":[0.1,1],"penalty":["l1","l2"]},
    models[4]:{"n_estimators":[1000,2000],"max_depth":[4,10],"min_samples_split":[2,5]},
    models[5]:{"learning_rate":[0.001,0.01],"n_estimators":[1000,2000],"max_depth":[4,10],"subsample":[0.6,0.8]},
    models[6]:{"learning_rate":[0.001,0.01],"n_estimators":[1000,2000],"max_depth":[4,10],"subsample":[0.6,0.8]}
}

def cozum(model):
    model.fit(X_train,y_train)
    return model

def skor(model2):
    tahmin=cozum(model2).predict(X_test)
    acs=accuracy_score(y_test,tahmin)
    return acs*100

for i,j in zip(models,sınıflar):
    print(i)
    grid=GridSearchCV(cozum(j),parametreler[i],cv=10,n_jobs=-1)
    grid.fit(X_train,y_train)
    print(grid.best_params_)
    print()

# LinearSVC
# {'C': 0.1, 'penalty': 'l1'}

# SVC
# {'C': 1, 'gamma': 0.01, 'kernel': 'rbf'}

# Ridge
# {'alpha': 0.1}

# Logistic
# {'C': 0.1, 'penalty': 'l2'}

# RandomForest
# {'max_depth': 10, 'min_samples_split': 2, 'n_estimators': 2000}

# LGBM
# {'learning_rate': 0.01, 'max_depth': 4, 'n_estimators': 1000, 'subsample': 0.6}

# XGBM
# {'learning_rate': 0.001, 'max_depth': 4, 'n_estimators': 2000, 'subsample': 0.6}

sınıflar=[
    LinearSVC(random_state=0, C=0.1, penalty="l1",dual=False),
    SVC(random_state=0, C=1, gamma=0.01, kernel='rbf'),
    RidgeClassifier(random_state=0, alpha=0.1),
    LogisticRegression(random_state=0, C=0.1, penalty="l2",dual=False),
    RandomForestClassifier(random_state=0, max_depth=10, min_samples_split=2, n_estimators=2000),
    LGBMClassifier(random_state=0, learning_rate=0.01, max_depth=4, n_estimators=1000, subsample=0.6),
    XGBClassifier(learning_rate=0.001, max_depth=4, n_estimators=2000, subsample=0.6)
]

basari = []

for i in sınıflar:
    basari.append(skor(i))

a = list(zip(models, basari))
sonuc = pd.DataFrame(a, columns=["Model", "Başarı"])
sonuc.sort_values("Başarı", ascending=False)

#  Model         Başarı 
#  ------------  ------ 
#  LinearSVC     79.67  
#  Ridge         79.46  
#  RandomForest  79.46  
#  LGBM          79.18  
#  SVC           79.10  
#  XGBM          79.03  
#  Logistic      78.96  
```

# Denetimsiz Öğrenme Algoritmaları

Algoritma ile veriyi bölen ve bağımlı değişkeni olmayan yapılardır.

# K-Means Algoritması

Noktalar arasında küme içi benzerliklerin maksimum olacağı şekilde kümeleme yapar. Kümeleme sayısı olan k yi bizim vermemiz gerekir.

## Örnek

```python
import pandas as pd

data=pd.read_csv("customers.csv")
veri=data.copy()

veri = veri.drop(columns="CustomerID", axis=1)

print(veri.info())

# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 200 entries, 0 to 199
# Data columns (total 4 columns):
#  #   Column                  Non-Null Count  Dtype 
# ---  ------                  --------------  ----- 
#  0   Gender                  200 non-null    object
#  1   Age                     200 non-null    int64 
#  2   Annual Income (k$)      200 non-null    int64 
#  3   Spending Score (1-100)  200 non-null    int64 
# dtypes: int64(3), object(1)
# memory usage: 6.4+ KB
# None

veri.describe()

#                       Age     Annual Income (k\$)  Spending Score (1-100) 
#  -------------------  ------  -------------------  ---------------------- 
#  **count**            200.00  200.00               200.00                 
#  **mean**             38.85   60.56                50.20                  
#  **std**              13.97   26.26                25.82                  
#  **min**              18.00   15.00                1.00                   
#  **25%** (1. çeyrek)  28.75   41.50                34.75                  
#  **50%** (medyan)     36.00   61.50                50.00                  
#  **75%** (3. çeyrek)  49.00   78.00                73.00                  
#  **max**              70.00   137.00               99.00                  

X = veri.iloc[:, 1:3]

import matplotlib.pyplot as plt
import seaborn as sns

plt.scatter(X.iloc[:, 0], X.iloc[:, 1], color="black")
plt.show()
```
![image](./images/kmns1.png) 

```python
from sklearn.cluster import KMeans

kmodel = KMeans(n_clusters=2, random_state=0)
kfit = kmodel.fit(X)
kumeler = kfit.labels_
merkezler = kfit.cluster_centers_

figure, axis = plt.subplots(1, 2)
axis[0].scatter(X.iloc[:, 0], X.iloc[:, 1], color="black")
axis[1].scatter(X.iloc[:, 0], X.iloc[:, 1], c=kumeler, cmap="winter")
axis[1].scatter(merkezler[:, 0], merkezler[:, 1], c="red", s=200)
plt.show()
```

![image](./images/kmns2.png) 

K sayısı kaç olmalı ona bakalım;

```python
wcss = []

for k in range(1, 20):
    kmodel = KMeans(n_clusters=k, random_state=0)
    kmodel.fit(X)
    wcss.append(kmodel.inertia_)

plt.plot(range(1, 20), wcss)
plt.xlabel("Küme Sayısı")
plt.ylabel("WCSS")
plt.show()
```

![image](./images/kmns3.png) 

En son görülen belirgin kırılım 6 değerinde olduğu için 6 olarak belirleyebiliriz. Başka bir yöntem;

```python
from yellowbrick.cluster import KElbowVisualizer

kmodel = KMeans(random_state=0)
grafik = KElbowVisualizer(kmodel, k=(1, 20))
grafik.fit(X)
grafik.poof()
```

![image](./images/kmns4.png) 

Olması gereken değerin 4 olduğunu bize gösteriyor.

```python
from sklearn.cluster import KMeans

kmodel = KMeans(n_clusters=4, random_state=0)
kfit = kmodel.fit(X)
kumeler = kfit.labels_
merkezler = kfit.cluster_centers_

figure, axis = plt.subplots(1, 2)
axis[0].scatter(X.iloc[:, 0], X.iloc[:, 1], color="black")
axis[1].scatter(X.iloc[:, 0], X.iloc[:, 1], c=kumeler, cmap="winter")
axis[1].scatter(merkezler[:, 0], merkezler[:, 1], c="red", s=200)
plt.show()
```

![image](./images/kmns5.png)

# Hiyerarşik Kümeleme Algoritması (HCA)

İki çeşittir;

- Birleştirici (Agglomerative): Bu, "aşağıdan yukarıya" bir yaklaşımdır: Her gözlem kendi kümesinde başlar ve kümeler yukarı doğru ilerledikçe birleştirilir.

- Bölücü (Divisive): Bu, "yukarıdan aşağıya" bir yaklaşımdır: Tüm gözlemler tek bir kümede başlar ve aşağı doğru ilerledikçe kümeler tekrar tekrar bölünür.

```python
import pandas as pd

data=pd.read_csv("customers.csv")
veri=data.copy()

veri = veri.drop(columns="CustomerID", axis=1)

X = veri.iloc[:, 1:3]

from sklearn.cluster import AgglomerativeClustering

model = AgglomerativeClustering()
tahmin = model.fit_predict(X)
print(tahmin)

# [1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 1 0 1 1 1 0 0 0 0 0 1 0 1 0 0 0 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 1 0 1 1 1 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]

model = AgglomerativeClustering(n_clusters=3)
tahmin = model.fit_predict(X)
print(tahmin)

# [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 1 0 0 0 1 1 1 1 1 0 1 0 1 1 1 0 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 0 1 1 1 0 1 0 0 0 1 1 1 1 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 1 2 1 2 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2]

import matplotlib.pyplot as plt

model = AgglomerativeClustering()
tahmin = model.fit_predict(X)

X["Label"] = tahmin
print(X)

#      Age  Annual Income (k$)  Label
# 0     19                  15      1
# 1     21                  15      1
# 2     20                  16      1
# 3     23                  16      1
# 4     31                  17      1
# ..   ...                 ...    ...
# 195   35                 120      0
# 196   45                 126      0
# 197   32                 126      0
# 198   32                 137      0
# 199   30                 137      0

print(X["Age"][X["Label"] == 0])
# 66     43
# 71     47
# 75     26
# 76     45
# 77     40
#        ..
# 195    35
# 196    45
# 197    32
# 198    32
# 199    30
# Name: Age, Length: 117, dtype: int64

print(X["Age"][X["Label"] == 1])
# 0      19
# 1      21
# 2      20
# 3      23
# 4      31
#        ..
# 106    66
# 108    68
# 109    66
# 110    65
# 116    63
# Name: Age, Length: 83, dtype: int64

plt.scatter(X["Age"][X["Label"] == 0], X["Annual Income (k$)"][X["Label"] == 0], c="red")
plt.scatter(X["Age"][X["Label"] == 1], X["Annual Income (k$)"][X["Label"] == 1], c="black")
plt.show()

![image](./images/hca1.png) 

from scipy.cluster.hierarchy import dendrogram, linkage

link = linkage(X)
dendrogram(link)
plt.xlabel("Veri Noktaları")
plt.ylabel("Mesafe")
plt.show()
```

---

```python
import pandas as pd

data=pd.read_csv("iris.csv")
veri=data.copy()

X = veri.drop(columns=["Id", "Species"], axis=1)

from scipy.cluster.hierarchy import linkage, dendrogram
import matplotlib.pyplot as plt
import seaborn as sns

hcsingle = linkage(X, method="single")
hccomplete = linkage(X, method="complete")
hcaverage = linkage(X, method="average")
hccentroid = linkage(X, method="centroid")
hcmedian = linkage(X, method="median")
hcward = linkage(X, method="ward")

fig, axes = plt.subplots(2, 3)
dendrogram(hcsingle, ax=axes[0, 0])
axes[0, 0].set_title("Single")
dendrogram(hccomplete, ax=axes[0, 1])
axes[0, 1].set_title("Complete")
dendrogram(hcaverage, ax=axes[0, 2])
axes[0, 2].set_title("Average")
dendrogram(hccentroid, ax=axes[1, 0])
axes[1, 0].set_title("Centroid")
dendrogram(hcmedian, ax=axes[1, 1])
axes[1, 1].set_title("Median")
dendrogram(hcward, ax=axes[1, 2])
axes[1, 2].set_title("Ward")
plt.show()

```

![image](./images/hca2.png) 

Aşağıdaki gibi incelendğinde en uygun bölünebileceği uzun alan 2 kez kestiği yerdir. En çok kullanılan modeller ward ile single'dır

![image](./images/hca3.png) 

```python
from sklearn.cluster import AgglomerativeClustering

model = AgglomerativeClustering(n_clusters=2, linkage="ward")
tahmin = model.fit_predict(X)
labels = model.labels_

sns.scatterplot(x="SepalLengthCm", y="SepalWidthCm", data=X, hue=labels, palette="deep")
plt.show()
```

![image](./images/hca4.png)

# Bist30 Kümeleme Analizi

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://www.isyatirim.com.tr/tr-tr/analiz/hisse/Sayfalar/Temel-Degerler-Ve-Oranlar.aspx?endeks=03#page-1"
r = requests.get(url)
s = BeautifulSoup(r.text, "html.parser")

tablo = s.find("table", {"id": "summaryBasicData"})
tablo = pd.read_html(str(tablo), flavor="bs4")[0]


hisseler=[
    'AKBNK', 'AKSEN', 'ARCLK', 'ASELS', 'BIMAS', 'EKGYO', 'EREGL',
    'FROTO', 'GARAN', 'GUBRF', 'HEKTS', 'ISCTR', 'KCHOL', 'KOZAA',
    'KOZAL', 'KRDMD', 'PETKM', 'PGSUS', 'SAHOL', 'SASA', 'SISE',
    'TAVHL', 'TCELL', 'THYAO', 'TKFEN', 'TOASO', 'TTKOM', 'TUPRS',
    'VESTL', 'YKBNK'
]

# hisseler=[i for i in tablo["Kod"]]
# print(hisseler)
# ['AEFES', 'AKBNK', 'ASELS', 'ASTOR', 'BIMAS', 'CIMSA', 'EKGYO', 'ENKAI', 'EREGL', 'FROTO', 'GARAN', 'GUBRF', 'ISCTR', 'KCHOL', 'KOZAL', 'KRDMD', 'MGROS', 'PETKM', 'PGSUS', 'SAHOL', 'SASA', 'SISE', 'TAVHL', 'TCELL', 'THYAO', 'TOASO', 'TTKOM', 'TUPRS', 'ULKER', 'YKBNK']

# print(len(hisseler)) 
# 30

# ⚠️⚠️⚠️ https://www.isyatirim.com.tr/tr-tr/analiz/hisse/Sayfalar/Tarihsel-Fiyat-Bilgileri.aspx linkinde hisse seçince link değişmiyor ama veri değişiyor. İncele deyip tekrar veri talep edince ağ kısmında bir link görülecektir;
# https://www.isyatirim.com.tr/_layouts/15/Isyatirim.Website/Common/Data.aspx/HisseTekil?hisse=AKBNK&startdate=07-09-2023&enddate=07-09-2025
# Bu linki "https://www.isyatirim.com.tr/_layouts/15/Isyatirim.Website/Common/Data.aspx/HisseTekil?" ve hisse=AKBNK&startdate=07-09-2023&enddate=07-09-2025 olarak ikiye ayırıyoruz.

parametreler = (
    ("hisse", "AKBNK"),
    ("startdate", "28-11-2020"),
    ("enddate", "28-11-2022")
)

url2 = "https://www.isyatirim.com.tr/_layouts/15/Isyatirim.Website/Common/Data.aspx/HisseTekil?"

# Linkte bilgiler value sözlüğünde olduğu için ["value"] ile bitiriyoruz.  
r2 = requests.get(url2, params=parametreler).json()["value"]
veri = pd.DataFrame.from_dict(r2)

# print(veri)
#     HGDG_HS_KODU  HGDG_TARIH  HGDG_KAPANIS   HGDG_AOF   HGDG_MIN   HGDG_MAX  \
# 0          AKBNK  30-11-2020        4.8341   4.965814   4.834120   5.042903   
# 1          AKBNK  01-12-2020        5.0670   4.948148   4.842151   5.066993   
# 2          AKBNK  02-12-2020        5.0188   5.040494   4.970632   5.099113   
# 3          AKBNK  03-12-2020        5.0028   4.989904   4.914421   5.058964   
# 4          AKBNK  04-12-2020        4.9626   4.968223   4.938512   5.026843   
# ..           ...         ...           ...        ...        ...        ...   
# 496        AKBNK  22-11-2022       14.4321  14.231405  13.930346  14.517156   
# 497        AKBNK  23-11-2022       14.9934  14.700002  14.296040  15.120976   
# 498        AKBNK  24-11-2022       14.6277  14.934728  14.500146  15.197515   
# 499        AKBNK  25-11-2022       14.3045  14.461876  14.287535  14.687246   
# 500        AKBNK  28-11-2022       14.8743  14.782496  14.355571  15.052939   

#        HGDG_HACIM END_ENDEKS_KODU      END_TARIH  END_SEANS  ...  HG_MIN  \
# 0    9.761698e+08              01  1606683600000          2  ...    6.02   
# 1    7.437196e+08              01  1606770000000          2  ...    6.03   
# 2    5.958969e+08              01  1606856400000          2  ...    6.19   
# 3    6.962983e+08              01  1606942800000          2  ...    6.12   
# 4    3.508742e+08              01  1607029200000          2  ...    6.15   
# ..            ...             ...            ...        ...  ...     ...   
# 496  4.193727e+09              01  1669064400000          2  ...   16.38   
# 497  4.581212e+09              01  1669150800000          2  ...   16.81   
# 498  4.967338e+09              01  1669237200000          2  ...   17.05   
# 499  2.567450e+09              01  1669323600000          2  ...   16.80   
# 500  3.301224e+09              01  1669582800000          2  ...   16.88   

#     HG_MAX            PD        PD_USD        HAO_PD    HAO_PD_USD  \
# 0     6.28  3.130400e+10  4.011688e+09  1.583669e+10  2.029513e+09   
# 1     6.31  3.281200e+10  4.177052e+09  1.659959e+10  2.113171e+09   
# 2     6.35  3.250000e+10  4.154894e+09  1.644175e+10  2.101961e+09   
# 3     6.30  3.239600e+10  4.120895e+09  1.638914e+10  2.084761e+09   
# 4     6.26  3.213600e+10  4.123383e+09  1.625760e+10  2.086020e+09   
# ..     ...           ...           ...           ...           ...   
# 496  17.07  8.824400e+10  4.734907e+09  4.479265e+10  2.403439e+09   
# 497  17.78  9.167600e+10  4.918319e+09  4.653474e+10  2.496539e+09   
# 498  17.87  8.944000e+10  4.798798e+09  4.539975e+10  2.435870e+09   
# 499  17.27  8.746400e+10  4.692124e+09  4.439673e+10  2.381722e+09   
# 500  17.70  9.094800e+10  4.879080e+09  4.616520e+10  2.476621e+09   

#          HG_HACIM  DOLAR_BAZLI_MIN  DOLAR_BAZLI_MAX  DOLAR_BAZLI_AOF  
# 0    9.761698e+08           0.6195           0.6463           0.6364  
# 1    7.437196e+08           0.6164           0.6450           0.6299  
# 2    5.958969e+08           0.6355           0.6519           0.6444  
# 3    6.962983e+08           0.6251           0.6435           0.6347  
# 4    3.508742e+08           0.6337           0.6450           0.6375  
# ..            ...              ...              ...              ...  
# 496  4.193727e+09           0.7475           0.7789           0.7636  
# 497  4.581212e+09           0.7670           0.8112           0.7886  
# 498  4.967338e+09           0.7780           0.8154           0.8013  
# 499  2.567450e+09           0.7665           0.7879           0.7758  
# 500  3.301224e+09           0.7701           0.8075           0.7930

veri=veri[["HGDG_HS_KODU", "HGDG_TARIH", "HGDG_KAPANIS"]]
# print(veri)

#     HGDG_HS_KODU  HGDG_TARIH  HGDG_KAPANIS
# 0          AKBNK  30-11-2020        4.8341
# 1          AKBNK  01-12-2020        5.0670
# 2          AKBNK  02-12-2020        5.0188
# 3          AKBNK  03-12-2020        5.0028
# 4          AKBNK  04-12-2020        4.9626
# ..           ...         ...           ...
# 496        AKBNK  22-11-2022       14.4321
# 497        AKBNK  23-11-2022       14.9934
# 498        AKBNK  24-11-2022       14.6277
# 499        AKBNK  25-11-2022       14.3045
# 500        AKBNK  28-11-2022       14.8743

data = {"Tarih": veri["HGDG_TARIH"], veri["HGDG_HS_KODU"][0]: veri["HGDG_KAPANIS"]}
veri=pd.DataFrame(data)
# print(veri)
#           Tarih    AKBNK
# 0    30-11-2020   4.8341
# 1    01-12-2020   5.0670
# 2    02-12-2020   5.0188
# 3    03-12-2020   5.0028
# 4    04-12-2020   4.9626
# ..          ...      ...
# 496  22-11-2022  14.4321
# 497  23-11-2022  14.9934
# 498  24-11-2022  14.6277
# 499  25-11-2022  14.3045
# 500  28-11-2022  14.8743

# print(hisseler[0])
# AKBNK

hisseler = [h for h in hisseler if h not in ["AKBNK"]]

# print(hisseler)
# ['AKSEN', 'ARCLK', 'ASELS', 'BIMAS', 'EKGYO', 'EREGL', 'FROTO', 'GARAN', 'GUBRF', 'HEKTS', 'ISCTR', 'KCHOL', 'KOZAA', 'KOZAL', 'KRDMD', 'PETKM', 'PGSUS', 'SAHOL', 'SASA', 'SISE', 'TAVHL', 'TCELL', 'THYAO', 'TKFEN', 'TOASO', 'TTKOM', 'TUPRS', 'VESTL', 'YKBNK']

tumveri=[veri]

# print(tumveri)
# [          Tarih    AKBNK
# 0    30-11-2020   4.8341
# 1    01-12-2020   5.0670
# 2    02-12-2020   5.0188
# 3    03-12-2020   5.0028
# 4    04-12-2020   4.9626
# ..          ...      ...
# 496  22-11-2022  14.4321
# 497  23-11-2022  14.9934
# 498  24-11-2022  14.6277
# 499  25-11-2022  14.3045
# 500  28-11-2022  14.8743

# [501 rows x 2 columns]]

for j in hisseler:
    parametreler = (
        ("hisse", j),
        ("startdate", "28-11-2020"),
        ("enddate", "28-11-2022")
    )

    url2 = "https://www.isyatirim.com.tr/_layouts/15/Isyatirim.Website/Common/Data.aspx/HisseTekil?"
    r2 = requests.get(url2, params=parametreler).json()["value"]
    veri = pd.DataFrame.from_dict(r2)
    veri = veri.iloc[:, 0:3]
    
    data = {"Tarih": veri["HGDG_TARIH"], veri["HGDG_HS_KODU"][0]: veri["HGDG_KAPANIS"]}
    veri = pd.DataFrame(data)
    
    tumveri.append(veri)

# print(tumveri)
# [          Tarih    AKBNK
# 0    30-11-2020   4.8341
# 1    01-12-2020   5.0670
# 2    02-12-2020   5.0188
# 3    03-12-2020   5.0028
# 4    04-12-2020   4.9626
# ..          ...      ...
# 496  22-11-2022  14.4321
# 497  23-11-2022  14.9934
# 498  24-11-2022  14.6277
# 499  25-11-2022  14.3045
# 500  28-11-2022  14.8743

# [501 rows x 2 columns],           Tarih    AKSEN
# 0    30-11-2020   3.2387
# 1    01-12-2020   3.3359
# 2    02-12-2020   3.3775
# 3    03-12-2020   3.4284
# 4    04-12-2020   3.4007
# ..          ...      ...
# 496  22-11-2022  46.9622
# 497  23-11-2022  46.4144
# 498  24-11-2022  47.2739
# 499  25-11-2022  50.1075
# 500  28-11-2022  48.1712

# [501 rows x 2 columns],           Tarih    ARCLK
# 0    30-11-2020  24.1219
# 1    01-12-2020  24.9006
# 2    02-12-2020  24.8121
# 3    03-12-2020  25.4669
# 4    04-12-2020  25.1661
# ..          ...      ...
# 496  22-11-2022  86.9464
# 497  23-11-2022  88.8141
# 498  24-11-2022  89.8463
# 499  25-11-2022  90.3378
# 500  28-11-2022  90.6818

# [501 rows x 2 columns],           Tarih    ASELS
# 0    30-11-2020   8.4684
# 1    01-12-2020   8.8112
# 2    02-12-2020   8.7328
# 3    03-12-2020   8.7328
# 4    04-12-2020   8.6741
# ..          ...      ...
# 496  22-11-2022  21.2251
# 497  23-11-2022  21.9126
# 498  24-11-2022  22.8693
# 499  25-11-2022  24.0152
# 500  28-11-2022  23.4273

# [501 rows x 2 columns],           Tarih     BIMAS
# 0    30-11-2020   59.2427
# 1    01-12-2020   59.5389
# 2    02-12-2020   58.9888
# 3    03-12-2020   60.1737
# 4    04-12-2020   59.8351
# ..          ...       ...
# 496  22-11-2022  123.9258
# 497  23-11-2022  122.3262
# 498  24-11-2022  124.7727
# 499  25-11-2022  124.4904
# 500  28-11-2022  125.4314

# [501 rows x 2 columns],           Tarih   EKGYO
# 0    30-11-2020  1.9390
# 1    01-12-2020  1.9659
# 2    02-12-2020  1.9480
# 3    03-12-2020  1.9480
# 4    04-12-2020  1.9121
# ..          ...     ...
# 496  22-11-2022  5.7349
# 497  23-11-2022  5.7254
# 498  24-11-2022  5.9609
# 499  25-11-2022  6.5070
# 500  28-11-2022  6.5165

# [501 rows x 2 columns],           Tarih    EREGL
# 0    30-11-2020   4.1551
# 1    01-12-2020   4.2684
# 2    02-12-2020   4.3817
# 3    03-12-2020   4.3893
# 4    04-12-2020   4.3893
# ..          ...      ...
# 496  22-11-2022  19.9155
# 497  23-11-2022  20.2776
# 498  24-11-2022  20.2972
# 499  25-11-2022  20.4929
# 500  28-11-2022  20.5027

# [501 rows x 2 columns],           Tarih    FROTO
# 0    30-11-2020   8.5744
# 1    01-12-2020   9.1391
# 2    02-12-2020   9.3459
# 3    03-12-2020   9.1709
# 4    04-12-2020   9.7277
# ..          ...      ...
# 496  22-11-2022  38.5454
# 497  23-11-2022  38.7891
# 498  24-11-2022  38.4496
# 499  25-11-2022  38.9544
# 500  28-11-2022  38.8065

# [501 rows x 2 columns],           Tarih    GARAN
# 0    30-11-2020   7.1880
# 1    01-12-2020   7.5823
# 2    02-12-2020   7.5741
# 3    03-12-2020   7.5577
# 4    04-12-2020   7.4838
# ..          ...      ...
# 496  22-11-2022  22.9638
# 497  23-11-2022  23.6836
# 498  24-11-2022  23.2209
# 499  25-11-2022  22.6039
# 500  28-11-2022  23.1523

# [501 rows x 2 columns],           Tarih   GUBRF
# 0    30-11-2020   51.70
# 1    01-12-2020   51.40
# 2    02-12-2020   49.80
# 3    03-12-2020   49.82
# 4    04-12-2020   51.10
# ..          ...     ...
# 496  22-11-2022  171.80
# 497  23-11-2022  177.30
# 498  24-11-2022  191.00
# 499  25-11-2022  187.40
# 500  28-11-2022  192.60

# [501 rows x 2 columns],           Tarih    HEKTS
# 0    30-11-2020   0.4411
# 1    01-12-2020   0.4495
# 2    02-12-2020   0.4554
# 3    03-12-2020   0.4611
# 4    04-12-2020   0.4547
# ..          ...      ...
# 496  22-11-2022  14.7890
# 497  23-11-2022  14.5805
# 498  24-11-2022  13.6459
# 499  25-11-2022  12.5746
# 500  28-11-2022  13.0851

# [501 rows x 2 columns],           Tarih   ISCTR
# 0    30-11-2020  0.9789
# 1    01-12-2020  1.0210
# 2    02-12-2020  1.0119
# 3    03-12-2020  1.0089
# 4    04-12-2020  0.9939
# ..          ...     ...
# 496  22-11-2022  3.6112
# 497  23-11-2022  3.8038
# 498  24-11-2022  3.7646
# 499  25-11-2022  3.6575
# 500  28-11-2022  3.6861

# [501 rows x 2 columns],           Tarih    KCHOL
# 0    30-11-2020  15.0226
# 1    01-12-2020  15.6102
# 2    02-12-2020  15.7124
# 3    03-12-2020  15.9168
# 4    04-12-2020  15.7464
# ..          ...      ...
# 496  22-11-2022  61.3667
# 497  23-11-2022  61.5017
# 498  24-11-2022  61.7266
# 499  25-11-2022  63.3463
# 500  28-11-2022  63.8412

# [501 rows x 2 columns],           Tarih  KOZAA
# 0    30-11-2020  12.67
# 1    01-12-2020  12.95
# 2    02-12-2020  13.22
# 3    03-12-2020  13.12
# 4    04-12-2020  13.13
# ..          ...    ...
# 496  22-11-2022  40.74
# 497  23-11-2022  40.72
# 498  24-11-2022  44.78
# 499  25-11-2022  44.90
# 500  28-11-2022  44.44

# [501 rows x 2 columns],           Tarih    KOZAL
# 0    30-11-2020   3.5419
# 1    01-12-2020   3.6156
# 2    02-12-2020   3.6870
# 3    03-12-2020   3.7251
# 4    04-12-2020   3.6965
# ..          ...      ...
# 496  22-11-2022  13.8238
# 497  23-11-2022  13.7238
# 498  24-11-2022  15.0952
# 499  25-11-2022  16.2714
# 500  28-11-2022  15.8762

# [501 rows x 2 columns],           Tarih    KRDMD
# 0    30-11-2020   4.5456
# 1    01-12-2020   4.5827
# 2    02-12-2020   4.6848
# 3    03-12-2020   4.6569
# 4    04-12-2020   4.5549
# ..          ...      ...
# 496  22-11-2022  13.0947
# 497  23-11-2022  13.3541
# 498  24-11-2022  13.8440
# 499  25-11-2022  14.2379
# 500  28-11-2022  14.1673

# [501 rows x 2 columns],           Tarih  PETKM
# 0    30-11-2020   4.19
# 1    01-12-2020   4.26
# 2    02-12-2020   4.23
# 3    03-12-2020   4.26
# 4    04-12-2020   4.64
# ..          ...    ...
# 496  22-11-2022  16.19
# 497  23-11-2022  16.83
# 498  24-11-2022  16.70
# 499  25-11-2022  17.35
# 500  28-11-2022  18.25

# [501 rows x 2 columns],           Tarih    PGSUS
# 0    30-11-2020  11.7031
# 1    01-12-2020  12.8693
# 2    02-12-2020  13.0023
# 3    03-12-2020  12.9307
# 4    04-12-2020  12.9409
# ..          ...      ...
# 496  22-11-2022  70.6891
# 497  23-11-2022  74.9857
# 498  24-11-2022  75.8655
# 499  25-11-2022  77.9115
# 500  28-11-2022  77.2977

# [501 rows x 2 columns],           Tarih    SAHOL
# 0    30-11-2020   7.9219
# 1    01-12-2020   8.3739
# 2    02-12-2020   8.4232
# 3    03-12-2020   8.3821
# 4    04-12-2020   8.3821
# ..          ...      ...
# 496  22-11-2022  35.4228
# 497  23-11-2022  35.7619
# 498  24-11-2022  35.2979
# 499  25-11-2022  35.8689
# 500  28-11-2022  36.4578

# [501 rows x 2 columns],           Tarih    SASA
# 0    30-11-2020  0.3284
# 1    01-12-2020  0.3498
# 2    02-12-2020  0.3460
# 3    03-12-2020  0.3425
# 4    04-12-2020  0.3768
# ..          ...     ...
# 496  22-11-2022  8.1196
# 497  23-11-2022  8.2174
# 498  24-11-2022  7.7391
# 499  25-11-2022  7.0761
# 500  28-11-2022  7.2337

# [501 rows x 2 columns],           Tarih     SISE
# 0    30-11-2020   6.2171
# 1    01-12-2020   6.3268
# 2    02-12-2020   6.2994
# 3    03-12-2020   6.3908
# 4    04-12-2020   6.3999
# ..          ...      ...
# 496  22-11-2022  35.5453
# 497  23-11-2022  36.6495
# 498  24-11-2022  36.3639
# 499  25-11-2022  38.0393
# 500  28-11-2022  38.3820

# [501 rows x 2 columns],           Tarih  TAVHL
# 0    30-11-2020  18.99
# 1    01-12-2020  20.40
# 2    02-12-2020  20.00
# 3    03-12-2020  19.95
# 4    04-12-2020  20.08
# ..          ...    ...
# 496  22-11-2022  81.30
# 497  23-11-2022  82.70
# 498  24-11-2022  84.10
# 499  25-11-2022  86.35
# 500  28-11-2022  86.25

# [501 rows x 2 columns],           Tarih    TCELL
# 0    30-11-2020  12.3670
# 1    01-12-2020  12.7765
# 2    02-12-2020  12.6595
# 3    03-12-2020  13.1441
# 4    04-12-2020  13.0773
# ..          ...      ...
# 496  22-11-2022  30.4915
# 497  23-11-2022  30.6593
# 498  24-11-2022  31.9639
# 499  25-11-2022  32.1690
# 500  28-11-2022  32.7094

# [501 rows x 2 columns],           Tarih     THYAO
# 0    30-11-2020   11.1365
# 1    01-12-2020   12.2111
# 2    02-12-2020   11.9668
# 3    03-12-2020   11.9082
# 4    04-12-2020   11.8887
# ..          ...       ...
# 496  22-11-2022  108.6295
# 497  23-11-2022  109.8995
# 498  24-11-2022  109.0203
# 499  25-11-2022  111.1694
# 500  28-11-2022  112.4394

# [501 rows x 2 columns],           Tarih    TKFEN
# 0    30-11-2020  12.7372
# 1    01-12-2020  13.1836
# 2    02-12-2020  13.2624
# 3    03-12-2020  13.1749
# 4    04-12-2020  13.1661
# ..          ...      ...
# 496  22-11-2022  35.2458
# 497  23-11-2022  35.9398
# 498  24-11-2022  37.1816
# 499  25-11-2022  37.1268
# 500  28-11-2022  36.6155

# [501 rows x 2 columns],           Tarih     TOASO
# 0    30-11-2020   20.0022
# 1    01-12-2020   20.4795
# 2    02-12-2020   20.6900
# 3    03-12-2020   20.4795
# 4    04-12-2020   20.7602
# ..          ...       ...
# 496  22-11-2022  107.7684
# 497  23-11-2022  112.9560
# 498  24-11-2022  112.1193
# 499  25-11-2022  112.2030
# 500  28-11-2022  115.8008

# [501 rows x 2 columns],           Tarih    TTKOM
# 0    30-11-2020   6.2962
# 1    01-12-2020   6.3699
# 2    02-12-2020   6.3043
# 3    03-12-2020   6.2798
# 4    04-12-2020   6.4601
# ..          ...      ...
# 496  22-11-2022  15.1700
# 497  23-11-2022  15.5700
# 498  24-11-2022  17.1200
# 499  25-11-2022  17.1000
# 500  28-11-2022  17.2100

# [501 rows x 2 columns],           Tarih    TUPRS
# 0    30-11-2020   9.5233
# 1    01-12-2020   9.7484
# 2    02-12-2020   9.7118
# 3    03-12-2020   9.6647
# 4    04-12-2020   9.8008
# ..          ...      ...
# 496  22-11-2022  49.3181
# 497  23-11-2022  49.3285
# 498  24-11-2022  48.9725
# 499  25-11-2022  49.6008
# 500  28-11-2022  49.2971

# [501 rows x 2 columns],           Tarih    VESTL
# 0    30-11-2020  15.7493
# 1    01-12-2020  15.9068
# 2    02-12-2020  15.9068
# 3    03-12-2020  16.0800
# 4    04-12-2020  16.0170
# ..          ...      ...
# 496  22-11-2022  45.3600
# 497  23-11-2022  46.9200
# 498  24-11-2022  49.7200
# 499  25-11-2022  50.6000
# 500  28-11-2022  52.3000

# [501 rows x 2 columns],           Tarih    YKBNK
# 0    30-11-2020   2.3893
# 1    01-12-2020   2.4979
# 2    02-12-2020   2.4896
# 3    03-12-2020   2.4645
# 4    04-12-2020   2.4311
# ..          ...      ...
# 496  22-11-2022  10.1303
# 497  23-11-2022  10.5608
# 498  24-11-2022  10.3060
# 499  25-11-2022  10.0600
# 500  28-11-2022  10.4554

# [501 rows x 2 columns]]

df = tumveri[0]

for son in tumveri[1:]:
    df = df.merge(son, on="Tarih")

# print(df)
#           Tarih    AKBNK    AKSEN    ARCLK    ASELS     BIMAS   EKGYO  \
# 0    30-11-2020   4.8341   3.2387  24.1219   8.4684   59.2427  1.9390   
# 1    01-12-2020   5.0670   3.3359  24.9006   8.8112   59.5389  1.9659   
# 2    02-12-2020   5.0188   3.3775  24.8121   8.7328   58.9888  1.9480   
# 3    03-12-2020   5.0028   3.4284  25.4669   8.7328   60.1737  1.9480   
# 4    04-12-2020   4.9626   3.4007  25.1661   8.6741   59.8351  1.9121   
# ..          ...      ...      ...      ...      ...       ...     ...   
# 496  22-11-2022  14.4321  46.9622  86.9464  21.2251  123.9258  5.7349   
# 497  23-11-2022  14.9934  46.4144  88.8141  21.9126  122.3262  5.7254   
# 498  24-11-2022  14.6277  47.2739  89.8463  22.8693  124.7727  5.9609   
# 499  25-11-2022  14.3045  50.1075  90.3378  24.0152  124.4904  6.5070   
# 500  28-11-2022  14.8743  48.1712  90.6818  23.4273  125.4314  6.5165   

#        EREGL    FROTO    GARAN  ...     SISE  TAVHL    TCELL     THYAO  \
# 0     4.1551   8.5744   7.1880  ...   6.2171  18.99  12.3670   11.1365   
# 1     4.2684   9.1391   7.5823  ...   6.3268  20.40  12.7765   12.2111   
# 2     4.3817   9.3459   7.5741  ...   6.2994  20.00  12.6595   11.9668   
# 3     4.3893   9.1709   7.5577  ...   6.3908  19.95  13.1441   11.9082   
# 4     4.3893   9.7277   7.4838  ...   6.3999  20.08  13.0773   11.8887   
# ..       ...      ...      ...  ...      ...    ...      ...       ...   
# 496  19.9155  38.5454  22.9638  ...  35.5453  81.30  30.4915  108.6295   
# 497  20.2776  38.7891  23.6836  ...  36.6495  82.70  30.6593  109.8995   
# 498  20.2972  38.4496  23.2209  ...  36.3639  84.10  31.9639  109.0203   
# 499  20.4929  38.9544  22.6039  ...  38.0393  86.35  32.1690  111.1694   
# 500  20.5027  38.8065  23.1523  ...  38.3820  86.25  32.7094  112.4394   

#        TKFEN     TOASO    TTKOM    TUPRS    VESTL    YKBNK  
# 0    12.7372   20.0022   6.2962   9.5233  15.7493   2.3893  
# 1    13.1836   20.4795   6.3699   9.7484  15.9068   2.4979  
# 2    13.2624   20.6900   6.3043   9.7118  15.9068   2.4896  
# 3    13.1749   20.4795   6.2798   9.6647  16.0800   2.4645  
# 4    13.1661   20.7602   6.4601   9.8008  16.0170   2.4311  
# ..       ...       ...      ...      ...      ...      ...  
# 496  35.2458  107.7684  15.1700  49.3181  45.3600  10.1303  
# 497  35.9398  112.9560  15.5700  49.3285  46.9200  10.5608  
# 498  37.1816  112.1193  17.1200  48.9725  49.7200  10.3060  
# 499  37.1268  112.2030  17.1000  49.6008  50.6000  10.0600  
# 500  36.6155  115.8008  17.2100  49.2971  52.3000  10.4554  

veri = df.drop(columns="Tarih", axis=1)
# print(veri)
#        AKBNK    AKSEN    ARCLK    ASELS     BIMAS   EKGYO    EREGL    FROTO  \
# 0     4.8341   3.2387  24.1219   8.4684   59.2427  1.9390   4.1551   8.5744   
# 1     5.0670   3.3359  24.9006   8.8112   59.5389  1.9659   4.2684   9.1391   
# 2     5.0188   3.3775  24.8121   8.7328   58.9888  1.9480   4.3817   9.3459   
# 3     5.0028   3.4284  25.4669   8.7328   60.1737  1.9480   4.3893   9.1709   
# 4     4.9626   3.4007  25.1661   8.6741   59.8351  1.9121   4.3893   9.7277   
# ..       ...      ...      ...      ...       ...     ...      ...      ...   
# 496  14.4321  46.9622  86.9464  21.2251  123.9258  5.7349  19.9155  38.5454   
# 497  14.9934  46.4144  88.8141  21.9126  122.3262  5.7254  20.2776  38.7891   
# 498  14.6277  47.2739  89.8463  22.8693  124.7727  5.9609  20.2972  38.4496   
# 499  14.3045  50.1075  90.3378  24.0152  124.4904  6.5070  20.4929  38.9544   
# 500  14.8743  48.1712  90.6818  23.4273  125.4314  6.5165  20.5027  38.8065   

#        GARAN   GUBRF  ...     SISE  TAVHL    TCELL     THYAO    TKFEN  \
# 0     7.1880   51.70  ...   6.2171  18.99  12.3670   11.1365  12.7372   
# 1     7.5823   51.40  ...   6.3268  20.40  12.7765   12.2111  13.1836   
# 2     7.5741   49.80  ...   6.2994  20.00  12.6595   11.9668  13.2624   
# 3     7.5577   49.82  ...   6.3908  19.95  13.1441   11.9082  13.1749   
# 4     7.4838   51.10  ...   6.3999  20.08  13.0773   11.8887  13.1661   
# ..       ...     ...  ...      ...    ...      ...       ...      ...   
# 496  22.9638  171.80  ...  35.5453  81.30  30.4915  108.6295  35.2458   
# 497  23.6836  177.30  ...  36.6495  82.70  30.6593  109.8995  35.9398   
# 498  23.2209  191.00  ...  36.3639  84.10  31.9639  109.0203  37.1816   
# 499  22.6039  187.40  ...  38.0393  86.35  32.1690  111.1694  37.1268   
# 500  23.1523  192.60  ...  38.3820  86.25  32.7094  112.4394  36.6155   

#         TOASO    TTKOM    TUPRS    VESTL    YKBNK  
# 0     20.0022   6.2962   9.5233  15.7493   2.3893  
# 1     20.4795   6.3699   9.7484  15.9068   2.4979  
# 2     20.6900   6.3043   9.7118  15.9068   2.4896  
# 3     20.4795   6.2798   9.6647  16.0800   2.4645  
# 4     20.7602   6.4601   9.8008  16.0170   2.4311  
# ..        ...      ...      ...      ...      ...  
# 496  107.7684  15.1700  49.3181  45.3600  10.1303  
# 497  112.9560  15.5700  49.3285  46.9200  10.5608  
# 498  112.1193  17.1200  48.9725  49.7200  10.3060  
# 499  112.2030  17.1000  49.6008  50.6000  10.0600  
# 500  115.8008  17.2100  49.2971  52.3000  10.4554 

gelir = veri.pct_change().mean() * 252
sonuc = pd.DataFrame(gelir)
sonuc.columns = ["Gelir"]

import numpy as np
sonuc["Oynaklık"] = veri.pct_change().std() * np.sqrt(252)
sonuc = sonuc.reset_index()
sonuc = sonuc.rename({"index":"Hisse"},axis=1)
# print(sonuc)
#     Hisse     Gelir  Oynaklık
# 0   AKBNK  0.662954  0.438267
# 1   AKSEN  1.461318  0.442212
# 2   ARCLK  0.734791  0.365845
# 3   ASELS  0.599736  0.416809
# 4   BIMAS  0.433783  0.332923
# 5   EKGYO  0.721017  0.468984
# 6   EREGL  0.900761  0.438097
# 7   FROTO  0.882042  0.492344
# 8   GARAN  0.681301  0.428096
# 9   GUBRF  0.789535  0.504619
# 10  HEKTS  1.882756  0.585034
# 11  ISCTR  0.773748  0.458313
# 12  KCHOL  0.808473  0.395130
# 13  KOZAA  0.748871  0.482213
# 14  KOZAL  0.872406  0.481678
# 15  KRDMD  0.682948  0.469010
# 16  PETKM  0.839628  0.440136
# 17  PGSUS  1.075582  0.497158
# 18  SAHOL  0.844788  0.386068
# 19   SASA  1.733621  0.588744
# 20   SISE  0.992491  0.383484
# 21  TAVHL  0.865711  0.452449
# 22  TCELL  0.563173  0.380947
# 23  THYAO  1.264411  0.440684
# 24  TKFEN  0.616058  0.407903
# 25  TOASO  0.985238  0.444968
# 26  TTKOM  0.588538  0.403197
# 27  TUPRS  0.916426  0.416672
# 28  VESTL  0.691594  0.415665
# 29  YKBNK  0.851200  0.461357

from sklearn.preprocessing import MinMaxScaler

ms = MinMaxScaler()
X = ms.fit_transform(sonuc.iloc[:, [1, 2]])
X = pd.DataFrame(X, columns=["Gelir", "Oynaklık"])
# print(X.describe())
#            Gelir   Oynaklık
# count  30.000000  30.000000
# mean    0.309447   0.434068
# std     0.224518   0.219957
# min     0.000000   0.000000
# 25%     0.173452   0.300679
# 50%     0.269341   0.420165
# 75%     0.330391   0.531936
# max     1.000000   1.000000

from yellowbrick.cluster import KElbowVisualizer
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
import seaborn as sns

kmodel = KMeans(random_state=0)
grafik = KElbowVisualizer(kmodel, k=(2, 20))
grafik.fit(X)
grafik.poof()
```
![image](./images/bist1.png) 

```Python
kmodel = KMeans(n_clusters=6, random_state=0)
kfit = kmodel.fit(X)
labels = kfit.labels_

sns.scatterplot(x="Gelir", y="Oynaklık", data=X, hue=labels, palette="deep")
plt.show()
```

![image](./images/bist2.png)

```Python
sonuc["Labels"] = labels

sns.scatterplot(x="Labels", y="Hisse", data=sonuc, hue=labels, palette="deep")
plt.show()
```

![image](./images/bist3.png) 

```Python
from sklearn.cluster import AgglomerativeClustering
from scipy.cluster.hierarchy import linkage, dendrogram

hc = linkage(X, method="single")
dendrogram(hc)
plt.show()
```

![image](./images/bist4.png) 

2 ve 4 nokta geçen kümelemeler önemli gibi gözüküyor.

```Python
model = AgglomerativeClustering(n_clusters=4, linkage="single")
tahmin = model.fit_predict(X)
labels = model.labels_
sonuc["Labels"] = labels

sns.scatterplot(x="Labels", y="Hisse", data=sonuc, hue="Labels", palette="deep")
plt.show()
```

![image](./images/bist5.png) 

```Python
model = AgglomerativeClustering(n_clusters=2, linkage="single")
tahmin = model.fit_predict(X)
labels = model.labels_
sonuc["Labels"] = labels

sns.scatterplot(x="Labels", y="Hisse", data=sonuc, hue="Labels", palette="deep")
plt.show()
```

![image](./images/bist6.png)

# RFM 

RFM, pazarlama ve müşteri ilişkileri yönetiminde kullanılan bir müşteri segmentasyon yöntemidir. RFM, üç temel ölçüme dayanır:

RFM Açılımı:

- R - Recency (En Son Ne Zaman Alışveriş Yaptı?)

    - Müşterinin en son alışveriş yaptığı tarih.

    - Ne kadar yakınsa, müşteri o kadar aktif kabul edilir.

- F - Frequency (Ne Sıklıkla Alışveriş Yaptı?)

    - Müşterinin belirli bir zaman aralığında kaç kez alışveriş yaptığı.

    - Daha sık alışveriş yapan müşteriler, daha sadık sayılır.

- M - Monetary (Ne Kadar Harcama Yaptı?)

    - Müşterinin toplam harcama miktarı.

    - En çok harcama yapan müşteriler genellikle en değerli müşterilerdir


## Örnek

| **Değişken Adı** | **Rolü** | **Türü**  | **Açıklama**                                                                                                                        | **Birim** | **Eksik Değerler** |
| ---------------- | -------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------- | --------- | ------------------ |
| **InvoiceNo**    | Kimlik   | Kategorik | Her işleme benzersiz olarak atanmış 6 basamaklı tamsayı bir numara. Bu kod 'C' harfi ile başlıyorsa, iptal edildiği anlamına gelir. | -         | hayır              |
| **StockCode**    | Kimlik   | Kategorik | Her ürüne benzersiz olarak atanmış 5 basamaklı tamsayı bir numara.                                                                  | -         | hayır              |
| **Description**  | Özellik  | Kategorik | Ürün adı                                                                                                                            | -         | hayır              |
| **Quantity**     | Özellik  | Tamsayı   | Her işlemdeki her ürünün (öğe) miktarı                                                                                              | -         | hayır              |
| **InvoiceDate**  | Özellik  | Tarih     | Her işlemin ne zaman gerçekleştiğini gösteren tarih ve saat                                                                         | -         | hayır              |
| **UnitPrice**    | Özellik  | Sürekli   | Birim ürün fiyatı                                                                                                                   | Sterlin   | hayır              |
| **CustomerID**   | Özellik  | Kategorik | Her müşteriye benzersiz olarak atanmış 5 basamaklı tamsayı bir numara                                                               | -         | hayır              |
| **Country**      | Özellik  | Kategorik | Her müşterinin ikamet ettiği ülkenin adı                                                                                            | -         | hayır              |

```Python
import pandas as pd
import numpy as np

data=pd.read_csv("Online Retail.csv")

veri=data.copy()

# print(veri)
#         Unnamed: 0 InvoiceNo StockCode                          Description  \
# 0                0    536365    85123A   WHITE HANGING HEART T-LIGHT HOLDER   
# 1                1    536365     71053                  WHITE METAL LANTERN   
# 2                2    536365    84406B       CREAM CUPID HEARTS COAT HANGER   
# 3                3    536365    84029G  KNITTED UNION FLAG HOT WATER BOTTLE   
# 4                4    536365    84029E       RED WOOLLY HOTTIE WHITE HEART.   
# ...            ...       ...       ...                                  ...   
# 541904      541904    581587     22613          PACK OF 20 SPACEBOY NAPKINS   
# 541905      541905    581587     22899         CHILDREN'S APRON DOLLY GIRL    
# 541906      541906    581587     23254        CHILDRENS CUTLERY DOLLY GIRL    
# 541907      541907    581587     23255      CHILDRENS CUTLERY CIRCUS PARADE   
# 541908      541908    581587     22138        BAKING SET 9 PIECE RETROSPOT    

#         Quantity          InvoiceDate  UnitPrice  CustomerID         Country  
# 0              6  2010-12-01 08:26:00       2.55     17850.0  United Kingdom  
# 1              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom  
# 2              8  2010-12-01 08:26:00       2.75     17850.0  United Kingdom  
# 3              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom  
# 4              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom  
# ...          ...                  ...        ...         ...             ...  
# 541904        12  2011-12-09 12:50:00       0.85     12680.0          France  
# 541905         6  2011-12-09 12:50:00       2.10     12680.0          France  
# 541906         4  2011-12-09 12:50:00       4.15     12680.0          France  
# 541907         4  2011-12-09 12:50:00       4.15     12680.0          France  
# 541908         3  2011-12-09 12:50:00       4.95     12680.0          France  

# print(veri.isnull().sum())
# Unnamed: 0          0
# InvoiceNo           0
# StockCode           0
# Description      1454
# Quantity            0
# InvoiceDate         0
# UnitPrice           0
# CustomerID     135080
# Country             0
# dtype: int64

veri=veri.dropna()
# print(veri.isnull().sum())
# Unnamed: 0     0
# InvoiceNo      0
# StockCode      0
# Description    0
# Quantity       0
# InvoiceDate    0
# UnitPrice      0
# CustomerID     0
# Country        0
# dtype: int64

veri["Total"] = veri["Quantity"] * veri["UnitPrice"]
# print(veri)
#         Unnamed: 0 InvoiceNo StockCode                          Description  \
# 0                0    536365    85123A   WHITE HANGING HEART T-LIGHT HOLDER   
# 1                1    536365     71053                  WHITE METAL LANTERN   
# 2                2    536365    84406B       CREAM CUPID HEARTS COAT HANGER   
# 3                3    536365    84029G  KNITTED UNION FLAG HOT WATER BOTTLE   
# 4                4    536365    84029E       RED WOOLLY HOTTIE WHITE HEART.   
# ...            ...       ...       ...                                  ...   
# 541904      541904    581587     22613          PACK OF 20 SPACEBOY NAPKINS   
# 541905      541905    581587     22899         CHILDREN'S APRON DOLLY GIRL    
# 541906      541906    581587     23254        CHILDRENS CUTLERY DOLLY GIRL    
# 541907      541907    581587     23255      CHILDRENS CUTLERY CIRCUS PARADE   
# 541908      541908    581587     22138        BAKING SET 9 PIECE RETROSPOT    

#         Quantity          InvoiceDate  UnitPrice  CustomerID         Country  \
# 0              6  2010-12-01 08:26:00       2.55     17850.0  United Kingdom   
# 1              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# 2              8  2010-12-01 08:26:00       2.75     17850.0  United Kingdom   
# 3              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# 4              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# ...          ...                  ...        ...         ...             ...   
# 541904        12  2011-12-09 12:50:00       0.85     12680.0          France   
# 541905         6  2011-12-09 12:50:00       2.10     12680.0          France   
# 541906         4  2011-12-09 12:50:00       4.15     12680.0          France   
# 541907         4  2011-12-09 12:50:00       4.15     12680.0          France   
# 541908         3  2011-12-09 12:50:00       4.95     12680.0          France   

#         Total  
# 0       15.30  
# 1       20.34  
# 2       22.00  
# 3       20.34  
# 4       20.34  
# ...       ...  
# 541904  10.20  
# 541905  12.60  
# 541906  16.60  
# 541907  16.60  
# 541908  14.85 

# print(veri[veri["Total"] <= 0])
#         Unnamed: 0 InvoiceNo StockCode                       Description  \
# 141            141   C536379         D                          Discount   
# 154            154   C536383    35004C   SET OF 3 COLOURED  FLYING DUCKS   
# 235            235   C536391     22556    PLASTERS IN TIN CIRCUS PARADE    
# 236            236   C536391     21984  PACK OF 12 PINK PAISLEY TISSUES    
# 237            237   C536391     21983  PACK OF 12 BLUE PAISLEY TISSUES    
# ...            ...       ...       ...                               ...   
# 540449      540449   C581490     23144   ZINC T-LIGHT HOLDER STARS SMALL   
# 541541      541541   C581499         M                            Manual   
# 541715      541715   C581568     21258        VICTORIAN SEWING BOX LARGE   
# 541716      541716   C581569     84978  HANGING HEART JAR T-LIGHT HOLDER   
# 541717      541717   C581569     20979     36 PENCILS TUBE RED RETROSPOT   

#         Quantity          InvoiceDate  UnitPrice  CustomerID         Country  \
# 141           -1  2010-12-01 09:41:00      27.50     14527.0  United Kingdom   
# 154           -1  2010-12-01 09:49:00       4.65     15311.0  United Kingdom   
# 235          -12  2010-12-01 10:24:00       1.65     17548.0  United Kingdom   
# 236          -24  2010-12-01 10:24:00       0.29     17548.0  United Kingdom   
# 237          -24  2010-12-01 10:24:00       0.29     17548.0  United Kingdom   
# ...          ...                  ...        ...         ...             ...   
# 540449       -11  2011-12-09 09:57:00       0.83     14397.0  United Kingdom   
# 541541        -1  2011-12-09 10:28:00     224.69     15498.0  United Kingdom   
# 541715        -5  2011-12-09 11:57:00      10.95     15311.0  United Kingdom   
# 541716        -1  2011-12-09 11:58:00       1.25     17315.0  United Kingdom   
# 541717        -5  2011-12-09 11:58:00       1.25     17315.0  United Kingdom   

#          Total  
# 141     -27.50  
# 154      -4.65  
# 235     -19.80  
# 236      -6.96  
# 237      -6.96  
# ...        ...  
# 540449   -9.13  
# 541541 -224.69  
# 541715  -54.75  
# 541716   -1.25  
# 541717   -6.25  

# Total sütununda 0 dan küçük olan değerler var. Bunların InvoiceNo  değeri C ile başlıyor.

veri = veri.drop(veri[veri["Total"] <= 0].index)
# print(veri[veri["Total"] <= 0])
# Empty DataFrame
# Columns: [Unnamed: 0, InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country, Total]
# Index: []

# Total sütunu 0 dan küçük değer içeren verileri kaldırdık.

import matplotlib.pyplot as plt
import seaborn as sns

sns.boxplot(veri["Total"])
plt.show()
```

![image](./images/rfm1.png) 

```Python
Q1 = veri["Total"].quantile(0.25)
Q3 = veri["Total"].quantile(0.75)

IQR = Q3 - Q1

altsınır = Q1 - 1.5 * IQR
ustsınır = Q3 + 1.5 * IQR

veri = veri[~((veri["Total"] > ustsınır) | (veri["Total"] < altsınır))]
# veri.shape
# (366643, 10)

# print(veri)
#         Unnamed: 0 InvoiceNo StockCode                          Description  \
# 0                0    536365    85123A   WHITE HANGING HEART T-LIGHT HOLDER   
# 1                1    536365     71053                  WHITE METAL LANTERN   
# 2                2    536365    84406B       CREAM CUPID HEARTS COAT HANGER   
# 3                3    536365    84029G  KNITTED UNION FLAG HOT WATER BOTTLE   
# 4                4    536365    84029E       RED WOOLLY HOTTIE WHITE HEART.   
# ...            ...       ...       ...                                  ...   
# 541904      541904    581587     22613          PACK OF 20 SPACEBOY NAPKINS   
# 541905      541905    581587     22899         CHILDREN'S APRON DOLLY GIRL    
# 541906      541906    581587     23254        CHILDRENS CUTLERY DOLLY GIRL    
# 541907      541907    581587     23255      CHILDRENS CUTLERY CIRCUS PARADE   
# 541908      541908    581587     22138        BAKING SET 9 PIECE RETROSPOT    

#         Quantity          InvoiceDate  UnitPrice  CustomerID         Country  \
# 0              6  2010-12-01 08:26:00       2.55     17850.0  United Kingdom   
# 1              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# 2              8  2010-12-01 08:26:00       2.75     17850.0  United Kingdom   
# 3              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# 4              6  2010-12-01 08:26:00       3.39     17850.0  United Kingdom   
# ...          ...                  ...        ...         ...             ...   
# 541904        12  2011-12-09 12:50:00       0.85     12680.0          France   
# 541905         6  2011-12-09 12:50:00       2.10     12680.0          France   
# 541906         4  2011-12-09 12:50:00       4.15     12680.0          France   
# 541907         4  2011-12-09 12:50:00       4.15     12680.0          France   
# 541908         3  2011-12-09 12:50:00       4.95     12680.0          France   

#         Total  
# 0       15.30  
# 1       20.34  
# 2       22.00  
# 3       20.34  
# 4       20.34  
# ...       ...  
# 541904  10.20  
# 541905  12.60  
# 541906  16.60  
# 541907  16.60  
# 541908  14.85  

# veri indexlerini resetleyelim;
veri = veri.reset_index()
# print(veri)
#          index  Unnamed: 0 InvoiceNo StockCode  \
# 0            0           0    536365    85123A   
# 1            1           1    536365     71053   
# 2            2           2    536365    84406B   
# 3            3           3    536365    84029G   
# 4            4           4    536365    84029E   
# ...        ...         ...       ...       ...   
# 360169  541904      541904    581587     22613   
# 360170  541905      541905    581587     22899   
# 360171  541906      541906    581587     23254   
# 360172  541907      541907    581587     23255   
# 360173  541908      541908    581587     22138   

#                                 Description  Quantity          InvoiceDate  \
# 0        WHITE HANGING HEART T-LIGHT HOLDER         6  2010-12-01 08:26:00   
# 1                       WHITE METAL LANTERN         6  2010-12-01 08:26:00   
# 2            CREAM CUPID HEARTS COAT HANGER         8  2010-12-01 08:26:00   
# 3       KNITTED UNION FLAG HOT WATER BOTTLE         6  2010-12-01 08:26:00   
# 4            RED WOOLLY HOTTIE WHITE HEART.         6  2010-12-01 08:26:00   
# ...                                     ...       ...                  ...   
# 360169          PACK OF 20 SPACEBOY NAPKINS        12  2011-12-09 12:50:00   
# 360170         CHILDREN'S APRON DOLLY GIRL          6  2011-12-09 12:50:00   
# 360171        CHILDRENS CUTLERY DOLLY GIRL          4  2011-12-09 12:50:00   
# 360172      CHILDRENS CUTLERY CIRCUS PARADE         4  2011-12-09 12:50:00   
# 360173        BAKING SET 9 PIECE RETROSPOT          3  2011-12-09 12:50:00   

#         UnitPrice  CustomerID         Country  Total  
# 0            2.55     17850.0  United Kingdom  15.30  
# 1            3.39     17850.0  United Kingdom  20.34  
# 2            2.75     17850.0  United Kingdom  22.00  
# 3            3.39     17850.0  United Kingdom  20.34  
# 4            3.39     17850.0  United Kingdom  20.34  
# ...           ...         ...             ...    ...  
# 360169       0.85     12680.0          France  10.20  
# 360170       2.10     12680.0          France  12.60  
# 360171       4.15     12680.0          France  16.60  
# 360172       4.15     12680.0          France  16.60  
# 360173       4.95     12680.0          France  14.85 

# print(len(veri["CustomerID"].unique()))
# 4194

# print(veri["CustomerID"].nunique()) # Boş değerleri saymaz
# 4194

# print(veri["InvoiceNo"].nunique())
# 16806

veri["CustomerID"] = veri["CustomerID"].astype("int")
# print(veri)
#          index  Unnamed: 0 InvoiceNo StockCode  \
# 0            0           0    536365    85123A   
# 1            1           1    536365     71053   
# 2            2           2    536365    84406B   
# 3            3           3    536365    84029G   
# 4            4           4    536365    84029E   
# ...        ...         ...       ...       ...   
# 366638  541904      541904    581587     22613   
# 366639  541905      541905    581587     22899   
# 366640  541906      541906    581587     23254   
# 366641  541907      541907    581587     23255   
# 366642  541908      541908    581587     22138   

#                                 Description  Quantity          InvoiceDate  \
# 0        WHITE HANGING HEART T-LIGHT HOLDER         6  2010-12-01 08:26:00   
# 1                       WHITE METAL LANTERN         6  2010-12-01 08:26:00   
# 2            CREAM CUPID HEARTS COAT HANGER         8  2010-12-01 08:26:00   
# 3       KNITTED UNION FLAG HOT WATER BOTTLE         6  2010-12-01 08:26:00   
# 4            RED WOOLLY HOTTIE WHITE HEART.         6  2010-12-01 08:26:00   
# ...                                     ...       ...                  ...   
# 366638          PACK OF 20 SPACEBOY NAPKINS        12  2011-12-09 12:50:00   
# 366639         CHILDREN'S APRON DOLLY GIRL          6  2011-12-09 12:50:00   
# 366640        CHILDRENS CUTLERY DOLLY GIRL          4  2011-12-09 12:50:00   
# 366641      CHILDRENS CUTLERY CIRCUS PARADE         4  2011-12-09 12:50:00   
# 366642        BAKING SET 9 PIECE RETROSPOT          3  2011-12-09 12:50:00   

#         UnitPrice  CustomerID         Country  Total  
# 0            2.55       17850  United Kingdom  15.30  
# 1            3.39       17850  United Kingdom  20.34  
# 2            2.75       17850  United Kingdom  22.00  
# 3            3.39       17850  United Kingdom  20.34  
# 4            3.39       17850  United Kingdom  20.34  
# ...           ...         ...             ...    ...  
# 366638       0.85       12680          France  10.20  
# 366639       2.10       12680          France  12.60  
# 366640       4.15       12680          France  16.60  
# 366641       4.15       12680          France  16.60  
# 366642       4.95       12680          France  14.85 

# print(veri.info())
# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 366643 entries, 0 to 366642
# Data columns (total 11 columns):
#  #   Column       Non-Null Count   Dtype  
# ---  ------       --------------   -----  
#  0   index        366643 non-null  int64  
#  1   Unnamed: 0   366643 non-null  int64  
#  2   InvoiceNo    366643 non-null  object 
#  3   StockCode    366643 non-null  object 
#  4   Description  366643 non-null  object 
#  5   Quantity     366643 non-null  int64  
#  6   InvoiceDate  366643 non-null  object 
#  7   UnitPrice    366643 non-null  float64
#  8   CustomerID   366643 non-null  int32  
#  9   Country      366643 non-null  object 
#  10  Total        366643 non-null  float64
# dtypes: float64(2), int32(1), int64(3), object(5)
# memory usage: 29.4+ MB
# None

veri["InvoiceDate"]=pd.to_datetime(veri["InvoiceDate"])
# print(veri.info())
# <class 'pandas.core.frame.DataFrame'>
# RangeIndex: 366643 entries, 0 to 366642
# Data columns (total 11 columns):
#  #   Column       Non-Null Count   Dtype         
# ---  ------       --------------   -----         
#  0   index        366643 non-null  int64         
#  1   Unnamed: 0   366643 non-null  int64         
#  2   InvoiceNo    366643 non-null  object        
#  3   StockCode    366643 non-null  object        
#  4   Description  366643 non-null  object        
#  5   Quantity     366643 non-null  int64         
#  6   InvoiceDate  366643 non-null  datetime64[ns]
#  7   UnitPrice    366643 non-null  float64       
#  8   CustomerID   366643 non-null  int32         
#  9   Country      366643 non-null  object        
#  10  Total        366643 non-null  float64       
# dtypes: datetime64[ns](1), float64(2), int32(1), int64(3), object(4)
# memory usage: 29.4+ MB
# None

# Normalde güncel verilerle çalışıyor olsaydık bugünün tarihini alırdık. Ama veriler güncel tarihli olmadığı için son işlem yapılan tarihi alacağız.
bugün=veri["InvoiceDate"].max()
# print(bugün)
# 2011-12-09 12:50:00

import datetime as dt
bugün=dt.datetime(2011,12,9,12,50,0)
# print(bugün)
# 2011-12-09 12:50:00

r = (bugün - veri.groupby("CustomerID").agg({"InvoiceDate": "max"})).apply(lambda x: x.dt.days)
# print(r)
#             InvoiceDate
# CustomerID             
# 12347                 1
# 12348                74
# 12349                18
# 12350               309
# 12352                35
# ...                 ...
# 18280               277
# 18281               180
# 18282                 7
# 18283                 3
# 18287                42

f = veri.groupby(["CustomerID", "InvoiceNo"]).agg({"InvoiceNo": "count"})
# print(f)
#                       InvoiceNo
# CustomerID InvoiceNo           
# 12347      537626            29
#            542237            29
#            549222            23
#            556201            17
#            562032            20
# ...                         ...
# 18283      579673            52
#            580872            50
# 18287      554065            25
#            570715            31
#            573167             2

f = veri.groupby(["CustomerID", "InvoiceNo"]).agg({"InvoiceNo": "count"})
f = f.groupby("CustomerID").agg({"InvoiceNo": "count"})
# print(f)
#             InvoiceNo
# CustomerID           
# 12347               7
# 12348               4
# 12349               1
# 12350               1
# 12352               7
# ...               ...
# 18280               1
# 18281               1
# 18282               2
# 18283              16
# 18287               3

m = veri.groupby("CustomerID").agg({"Total": "sum"})
# print(m)
#               Total
# CustomerID         
# 12347       3174.62
# 12348        601.64
# 12349       1145.35
# 12350        334.40
# 12352       1505.74
# ...             ...
# 18280        180.60
# 18281         80.82
# 18282        178.05
# 18283       2094.88
# 18287       1068.74

RFM = r.merge(f, on="CustomerID").merge(m, on="CustomerID")
# print(RFM)
#             InvoiceDate  InvoiceNo    Total
# CustomerID                                 
# 12347                 1          7  3174.62
# 12348                74          4   601.64
# 12349                18          1  1145.35
# 12350               309          1   334.40
# 12352                35          7  1505.74
# ...                 ...        ...      ...
# 18280               277          1   180.60
# 18281               180          1    80.82
# 18282                 7          2   178.05
# 18283                 3         16  2094.88
# 18287                42          3  1068.74

RFM=RFM.reset_index()
# print(RFM)
#       CustomerID  InvoiceDate  InvoiceNo    Total
# 0          12347            1          7  3174.62
# 1          12348           74          4   601.64
# 2          12349           18          1  1145.35
# 3          12350          309          1   334.40
# 4          12352           35          7  1505.74
# ...          ...          ...        ...      ...
# 4189       18280          277          1   180.60
# 4190       18281          180          1    80.82
# 4191       18282            7          2   178.05
# 4192       18283            3         16  2094.88
# 4193       18287           42          3  1068.74

RFM = RFM.rename(columns={
    "CustomerID": "Customer",
    "InvoiceDate": "Recency",
    "InvoiceNo": "Frequency",
    "Total": "Monetary"
})
# print(RFM)
#       Customer  Recency  Frequency  Monetary
# 0        12347        1          7   3174.62
# 1        12348       74          4    601.64
# 2        12349       18          1   1145.35
# 3        12350      309          1    334.40
# 4        12352       35          7   1505.74
# ...        ...      ...        ...       ...
# 4189     18280      277          1    180.60
# 4190     18281      180          1     80.82
# 4191     18282        7          2    178.05
# 4192     18283        3         16   2094.88
# 4193     18287       42          3   1068.74

df = RFM.iloc[:, 1:]
# print(df)
#       Recency  Frequency  Monetary
# 0           1          7   3174.62
# 1          74          4    601.64
# 2          18          1   1145.35
# 3         309          1    334.40
# 4          35          7   1505.74
# ...       ...        ...       ...
# 4189      277          1    180.60
# 4190      180          1     80.82
# 4191        7          2    178.05
# 4192        3         16   2094.88
# 4193       42          3   1068.74

from sklearn.preprocessing import MinMaxScaler
sc = MinMaxScaler()
dfnorm = sc.fit_transform(df)
dfnorm = pd.DataFrame(dfnorm, columns=df.columns)
# print(dfnorm)
#        Recency  Frequency  Monetary
# 0     0.002681      0.030  0.035451
# 1     0.198391      0.015  0.006701
# 2     0.048257      0.000  0.012777
# 3     0.828418      0.000  0.003715
# 4     0.093834      0.030  0.016804
# ...        ...        ...       ...
# 4189  0.742627      0.000  0.001997
# 4190  0.482574      0.000  0.000882
# 4191  0.018767      0.005  0.001968
# 4192  0.008043      0.075  0.023386
# 4193  0.112601      0.010  0.011921

# print(dfnorm.describe())
#            Recency    Frequency     Monetary
# count  4194.000000  4194.000000  4194.000000
# mean      0.245479     0.015036     0.011691
# std       0.267357     0.035127     0.024861
# min       0.000000     0.000000     0.000000
# 25%       0.045576     0.000000     0.002481
# 50%       0.134048     0.005000     0.005537
# 75%       0.383378     0.015000     0.013299
# max       1.000000     1.000000     1.000000

from yellowbrick.cluster import KElbowVisualizer
from sklearn.cluster import KMeans

kmodel = KMeans(random_state=0)
grafik = KElbowVisualizer(kmodel, k=(2, 10))
grafik.fit(dfnorm)
grafik.poof()
```

![image](./images/rfm2.png)

```Python
kmodel = KMeans(random_state=0, n_clusters=4, init="k-means++")
kfit = kmodel.fit(dfnorm)
labels = kfit.labels_

sns.scatterplot(x="Recency", y="Frequency", data=dfnorm, hue=labels, palette="deep")
plt.show()
```

![image](./images/rfm3.png)

```Python
RFM["Labels"] = labels
# print(RFM)
#       Customer  Recency  Frequency  Monetary  Labels
# 0        12347        1          7   3174.62       0
# 1        12348       74          4    601.64       3
# 2        12349       18          1   1145.35       0
# 3        12350      309          1    334.40       2
# 4        12352       35          7   1505.74       0
# ...        ...      ...        ...       ...     ...
# 4189     18280      277          1    180.60       2
# 4190     18281      180          1     80.82       1
# 4191     18282        7          2    178.05       0
# 4192     18283        3         16   2094.88       0
# 4193     18287       42          3   1068.74       0

sns.scatterplot(x="Labels", y="Customer", data=RFM, hue=labels, palette="deep")
plt.show()
```

![image](./images/rfm4.png)

```Python
sns.scatterplot(x="Labels", y="Customer", data=RFM, hue=labels, palette="deep")
plt.xlim(-1,5)
plt.show()
```

![image](./images/rfm5.png)

```Python
# print(RFM.groupby("Labels")["Customer"].count())
# Labels
# 0    2047
# 1     613
# 2     480
# 3    1054
# Name: Customer, dtype: int64

# print(RFM.groupby("Labels").mean())
#             Customer     Recency  Frequency     Monetary
# Labels                                                  
# 0       15311.653151   18.258915   6.033708  1600.294378
# 1       15467.446982  189.349103   1.849918   414.972073
# 2       15213.558333  307.245833   1.327083   278.799083
# 3       15200.811195   78.835863   2.546490   694.622583

# print(RFM.groupby("Labels").mean().iloc[:,1:])
#            Recency  Frequency     Monetary
# Labels                                    
# 0        18.258915   6.033708  1600.294378
# 1       189.349103   1.849918   414.972073
# 2       307.245833   1.327083   278.799083
# 3        78.835863   2.546490   694.622583
```



# 

![image](./images/rfm6.png)

https://www.youtube.com/watch?v=YbTxVCE6JKU&list=PLK8LlaNiWQOuTQisICOV6kAL4uoerdFs7&index=92