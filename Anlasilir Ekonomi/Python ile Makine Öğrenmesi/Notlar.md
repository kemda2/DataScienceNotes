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

from sklearn.metrics import mean_squared_error, mean_squared_error

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






















# 

![image](./images/pcaorn5.png)

https://www.youtube.com/watch?v=a5maY3KfPXE&list=PLK8LlaNiWQOuTQisICOV6kAL4uoerdFs7&index=32
0