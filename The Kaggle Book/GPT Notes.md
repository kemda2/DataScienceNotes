# Genel Bilgi

**Özet:** Kitaba göre tabular veri için en etkili yol **(1) sağlam EDA ve veri hazırlığı**, **(2) doğru doğrulama (validation) stratejisi**, **(3) güçlü temel modeller + HPO**, ve **(4) dikkatli ensembling / final retrain** adımlarını sırayla uygulamaktır. Aşağıda her tekniğin *nerede, ne zaman ve nasıl* kullanılacağıyla birlikte kısa, çalışır kod örnekleri verilmiştir.

### Karar noktaları ve öncelikler
- **Veri boyutu ve zaman:** Küçük veri → basit modeller + güçlü özellik müh.; büyük veri → ağaç tabanlı veya GPU hızlandırmalı modeller.  
- **Hedef metrik:** Yarışma metriğine göre (RMSE, AUC vb.) kayıp fonksiyonu ve post-processing seçilir.  
- **Kaynak:** Kısıtlı süre → hızlı LightGBM denemeleri; bol zaman → HPO + stacking.

---

### 1. Keşif ve veri hazırlığı (nerede / ne zaman)
**Ne için:** Hataları, eksik değerleri, dağılım kaymalarını bulmak; feature fikirleri üretmek.  
**Nasıl (örnek):**
```python
import pandas as pd
df = pd.read_csv('train.csv')
print(df.describe())
print(df.isnull().sum())
# Hedef ile korelasyon
print(df.corr()['target'].sort_values(ascending=False).head(20))
```
**İpucu:** **Özellikle dağılım kaymalarını (train/test shift)** kontrol edin; gerekirse hedefe göre gruplandırılmış istatistikler çıkarın.

---

### 2. Doğrulama stratejisi (nerede / ne zaman)
**Ne için:** Gerçek test performansını tahmin etmek; leaderboard sızmasını önlemek.  
**Nasıl:** K-fold (rastgele), zaman serisi için zaman bazlı split, grup bağımlılığı varsa GroupKFold.
```python
from sklearn.model_selection import KFold
kf = KFold(n_splits=5, shuffle=True, random_state=42)
for fold, (tr_idx, val_idx) in enumerate(kf.split(X)):
    X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
```
**Önemli:** **Validation stratejisi yanlışsa model yüksek leaderboard ama düşük gerçek performans verir.**

---

### 3. Modeller ve Hiperparametre optimizasyonu (nerede / ne zaman)
**Ne için:** Hızlı güçlü baz modeller kurmak; HPO ile doğrulamada en iyi parametreleri bulmak.  
**Nasıl (LightGBM + Optuna örneği):**
```python
import lightgbm as lgb
dtrain = lgb.Dataset(X_tr, label=y_tr)
params = {'objective':'regression','metric':'rmse','verbosity':-1}
model = lgb.train(params, dtrain, num_boost_round=1000, valid_sets=[dval], early_stopping_rounds=50)
```
**HPO (kısa):** Optuna ile `n_trials` sınırlı tutun; CV skorunu optimize edin.

---

### 4. Ensemble, stacking ve final retrain (nerede / ne zaman)
**Ne için:** Farklı modellerin hatalarını azaltıp son puanı yükseltmek.  
**Nasıl (basit stacking):**
```python
from sklearn.linear_model import Ridge
# level0_preds: list of OOF tahminleri
meta_X = np.column_stack(level0_oof_preds)
meta_model = Ridge()
meta_model.fit(meta_X, y)
```
**Final adımı:** En iyi modelleri **tüm veriyle yeniden eğitip** ağırlıklı ortalama veya stacking ile son tahmini üretin.

---

### Ek teknikler ve dikkat
- **Pseudo-labeling:** Testten güvenli tahminleri alıp eğitim setine ekleyin; *doğrulama ile dikkatli test edin*.  
- **Feature importance ile iterasyon:** Önemli olmayan özellikleri çıkarın; model hızlanır.  
- **Seed ve reproducibility:** Aynı sonuç için seed sabitleyin.

---

**Riskler:** Yanlış validation, veri sızıntısı, aşırı karmaşık ensemble’ların üretime alınamaması.  
**Kısa yol:** **EDA → Sağlam validation → Baseline modeller → HPO → Ensemble → Final retrain.**


---


# Tabular Modeling Pipeline (Kaggle-style)

Aşağıda **kitaptaki tüm ana strateji ve teknikleri** tek bir Kaggle tarzı yarışma akışında kapsayan, çalıştırılabilir ve adım adım açıklamalı bir Python kodu bulacaksınız. Kod **tek bir veri seti** (standart `train.csv` / `test.csv`) varsayımıyla yazıldı ve şu adımları içerir: **EDA & veri hazırlığı → sağlam doğrulama → güçlü baz modeller → Hiperparametre optimizasyonu → stacking/ensemble → pseudo‑labeling → final retrain & submission**. Her bölümün başında **nerede, ne zaman, ne için** kısa açıklama vardır.

> **Not:** Kod Kaggle Notebook’unda çalıştırılmak üzere tasarlanmıştır. `train.csv` ve `test.csv` dosyalarının kök dizinde olduğunu varsayar. Gerekirse sütun isimlerini (`ID_COL`, `TARGET_COL`) kendi veri setinize göre değiştirin.

---

## 1. Kurulum ve sabitler

**Nerede / Ne zaman / Ne için:** Yarışma başlangıcında; tekrar üretilebilirlik ve ortam hazırlığı için.

```python
# Genel kurulum ve sabitler
import os
import random
import numpy as np
import pandas as pd
import gc
from sklearn.model_selection import KFold, GroupKFold, StratifiedKFold
from sklearn.metrics import mean_squared_error, roc_auc_score, log_loss
from sklearn.preprocessing import LabelEncoder, StandardScaler
from sklearn.linear_model import Ridge
from sklearn.ensemble import RandomForestRegressor
import lightgbm as lgb
import xgboost as xgb
import catboost as cb
import joblib
import warnings
warnings.filterwarnings('ignore')

# Sabitler
RANDOM_SEED = 42
np.random.seed(RANDOM_SEED)
random.seed(RANDOM_SEED)
ID_COL = 'id'          # değiştirin
TARGET_COL = 'target'  # değiştirin
N_FOLDS = 5
VERBOSE = 100
```

---

## 2. Veri yükleme, hızlı EDA ve temel temizleme

**Nerede / Ne zaman / Ne için:** İlk adım; veri sorunlarını, eksikleri ve hedef dağılımını görmek için.

```python
# Veri yükleme
train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')
print("Train shape:", train.shape, "Test shape:", test.shape)

# Hedef dağılımı
print(train[TARGET_COL].describe())

# Eksik değerler ve tipler
def quick_eda(df, name='df'):
    print(f"--- {name} ---")
    print(df.dtypes.value_counts())
    print("Missing per column (top 10):")
    print(df.isnull().sum().sort_values(ascending=False).head(10))
    print("Sample:")
    display(df.head(3))

quick_eda(train, 'train')
quick_eda(test, 'test')

# ID ve hedef ayırma
if ID_COL in train.columns:
    train_idx = train[ID_COL]
    test_idx = test[ID_COL]
    train = train.drop(columns=[ID_COL])
    test = test.drop(columns=[ID_COL])
y = train[TARGET_COL].copy()
train = train.drop(columns=[TARGET_COL])
```

---

## 3. Özellik mühendisliği (feature engineering)

**Nerede / Ne zaman / Ne için:** EDA sonrası; modelin öğrenebileceği anlamlı değişkenler üretmek için.

```python
# Basit otomatik feature engineering fonksiyonu
def basic_feature_engineering(df):
    df = df.copy()
    # Sayısal sütunlar
    num_cols = df.select_dtypes(include=['int64','float64']).columns.tolist()
    # Kategorik sütunlar
    cat_cols = df.select_dtypes(include=['object','category']).columns.tolist()
    
    # Örnek: sayısal sütunlardan istatistiksel meta-features
    for c in num_cols:
        df[f'{c}_log1p'] = np.log1p(df[c].fillna(0))
    
    # Basit etkileşim örneği (ilk iki sayısal sütun varsa)
    if len(num_cols) >= 2:
        a, b = num_cols[0], num_cols[1]
        df[f'{a}_plus_{b}'] = df[a].fillna(0) + df[b].fillna(0)
        df[f'{a}_mul_{b}'] = df[a].fillna(0) * df[b].fillna(0)
    
    # Kategorikleri label encode et (basit)
    for c in cat_cols:
        df[c] = df[c].fillna('NA')
        le = LabelEncoder()
        df[c] = le.fit_transform(df[c].astype(str))
    return df

# Uygula
train_fe = basic_feature_engineering(train)
test_fe = basic_feature_engineering(test)
print("After FE shapes:", train_fe.shape, test_fe.shape)
```

**İpucu:** Kitapta önerildiği gibi, feature importance ile iteratif olarak gereksiz özellikleri çıkarın.

---

## 4. Doğrulama stratejisi (validation)

**Nerede / Ne zaman / Ne için:** Modelleme öncesi; gerçek test performansını tahmin etmek için. Eğer veri zaman serisi ise zaman bazlı split; grup bağımlılığı varsa GroupKFold kullanın.

```python
# Basit K-Fold CV (rastgele)
kf = KFold(n_splits=N_FOLDS, shuffle=True, random_state=RANDOM_SEED)

# Eğer zaman serisi ise:
# from sklearn.model_selection import TimeSeriesSplit
# tscv = TimeSeriesSplit(n_splits=N_FOLDS)

# Eğer grup varsa:
# gkf = GroupKFold(n_splits=N_FOLDS)
```

**Önemli:** Validation stratejisini yarışma veri dağılımına göre seçin; yanlış seçim leaderboard sızmasına yol açar.

---

## 5. Baseline modeller (LightGBM örneği) — hızlı denemeler

**Nerede / Ne zaman / Ne için:** Hızlı başlangıç; güçlü ve hızlı sonuç veren ağaç tabanlı modellerle baseline oluşturma.

```python
# Hazırlık
X = train_fe.copy()
X_test = test_fe.copy()

# LightGBM için dataset hazırlığı
oof_preds = np.zeros(X.shape[0])
test_preds = np.zeros(X_test.shape[0])
feature_importance_df = pd.DataFrame()

lgb_params = {
    'objective': 'regression',
    'metric': 'rmse',
    'boosting_type': 'gbdt',
    'learning_rate': 0.05,
    'num_leaves': 31,
    'feature_fraction': 0.8,
    'bagging_fraction': 0.8,
    'bagging_freq': 5,
    'seed': RANDOM_SEED,
    'verbosity': -1
}

for fold, (tr_idx, val_idx) in enumerate(kf.split(X, y)):
    X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
    y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
    dtrain = lgb.Dataset(X_tr, label=y_tr)
    dval = lgb.Dataset(X_val, label=y_val)
    model = lgb.train(lgb_params, dtrain, num_boost_round=5000, valid_sets=[dtrain, dval],
                      early_stopping_rounds=100, verbose_eval=VERBOSE)
    oof_preds[val_idx] = model.predict(X_val, num_iteration=model.best_iteration)
    test_preds += model.predict(X_test, num_iteration=model.best_iteration) / N_FOLDS
    
    # feature importance
    fold_imp = pd.DataFrame()
    fold_imp['feature'] = X.columns
    fold_imp['importance'] = model.feature_importance(importance_type='gain')
    fold_imp['fold'] = fold + 1
    feature_importance_df = pd.concat([feature_importance_df, fold_imp], axis=0)
    
print("OOF RMSE:", np.sqrt(mean_squared_error(y, oof_preds)))
```

---

## 6. Hiperparametre Optimizasyonu (Optuna ile örnek)

**Nerede / Ne zaman / Ne için:** Baseline sonrası; doğrulama skorunu iyileştirmek için. (Aşağıda kısa bir Optuna örneği; Optuna kurulumu gerektirir.)

```python
# Optuna örneği (kısa)
try:
    import optuna
except Exception as e:
    print("Optuna yüklü değilse pip install optuna ile yükleyin.")

def objective(trial):
    params = {
        'objective': 'regression',
        'metric': 'rmse',
        'boosting_type': 'gbdt',
        'learning_rate': trial.suggest_loguniform('learning_rate', 0.01, 0.2),
        'num_leaves': trial.suggest_int('num_leaves', 16, 128),
        'feature_fraction': trial.suggest_uniform('feature_fraction', 0.4, 1.0),
        'bagging_fraction': trial.suggest_uniform('bagging_fraction', 0.4, 1.0),
        'bagging_freq': trial.suggest_int('bagging_freq', 1, 10),
        'min_data_in_leaf': trial.suggest_int('min_data_in_leaf', 5, 100),
        'seed': RANDOM_SEED,
        'verbosity': -1
    }
    cv_scores = []
    for tr_idx, val_idx in kf.split(X, y):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
        dtrain = lgb.Dataset(X_tr, label=y_tr)
        dval = lgb.Dataset(X_val, label=y_val)
        model = lgb.train(params, dtrain, num_boost_round=2000, valid_sets=[dval],
                          early_stopping_rounds=50, verbose_eval=False)
        preds = model.predict(X_val, num_iteration=model.best_iteration)
        cv_scores.append(np.sqrt(mean_squared_error(y_val, preds)))
    return np.mean(cv_scores)

# Çalıştırma (örnek 20 deneme)
# study = optuna.create_study(direction='minimize')
# study.optimize(objective, n_trials=20)
# print("Best params:", study.best_params)
```

**İpucu:** Gerçek yarışmada `n_trials` çok daha yüksek olur; zaman ve kaynak kısıtlarına göre ayarlayın.

---

## 7. Diğer modeller: XGBoost, CatBoost, Neural Nets (kısa örnek)

**Nerede / Ne zaman / Ne için:** Farklı model ailelerini denemek ve ensemble çeşitliliği sağlamak için.

```python
# XGBoost kısa örnek
xgb_oof = np.zeros(X.shape[0])
xgb_test = np.zeros(X_test.shape[0])
for fold, (tr_idx, val_idx) in enumerate(kf.split(X, y)):
    X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
    y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
    dtrain = xgb.DMatrix(X_tr, label=y_tr)
    dval = xgb.DMatrix(X_val, label=y_val)
    params = {'objective':'reg:squarederror','eval_metric':'rmse','seed':RANDOM_SEED}
    bst = xgb.train(params, dtrain, num_boost_round=2000, evals=[(dval,'val')], early_stopping_rounds=50, verbose_eval=False)
    xgb_oof[val_idx] = bst.predict(dval, ntree_limit=bst.best_ntree_limit)
    xgb_test += bst.predict(xgb.DMatrix(X_test), ntree_limit=bst.best_ntree_limit) / N_FOLDS

# CatBoost kısa örnek (kategorik varsa avantajlı)
# cb_model = cb.CatBoostRegressor(iterations=1000, learning_rate=0.05, random_seed=RANDOM_SEED, verbose=False)
# cb_model.fit(X_tr, y_tr, eval_set=(X_val, y_val), early_stopping_rounds=50)
```

---

## 8. Stacking / Blending (meta-model)

**Nerede / Ne zaman / Ne için:** Farklı modellerin güçlü yönlerini birleştirmek için. Kitapta önerildiği gibi OOF (out-of-fold) tahminleri meta-model için kullanılır.

```python
# Basit stacking: level0 modellerinden OOF tahminleri topla
level0_oof = np.column_stack([oof_preds, xgb_oof])  # daha fazla model ekleyin
level0_test = np.column_stack([test_preds, xgb_test])

# Meta model (Ridge)
meta = Ridge(alpha=1.0, random_state=RANDOM_SEED)
meta_oof = np.zeros(level0_oof.shape[0])
meta_test = np.zeros(level0_test.shape[0])

for tr_idx, val_idx in kf.split(level0_oof, y):
    meta.fit(level0_oof[tr_idx], y.iloc[tr_idx])
    meta_oof[val_idx] = meta.predict(level0_oof[val_idx])
meta_test = meta.predict(level0_test)

print("Stacked OOF RMSE:", np.sqrt(mean_squared_error(y, meta_oof)))
```

---

## 9. Pseudo‑labeling (isteğe bağlı, dikkatli kullanın)

**Nerede / Ne zaman / Ne için:** Test tahminleri güvenliyse (yüksek güven), bunları eğitim verisine ekleyerek model gücünü artırmak için. **Dikkat:** Yanlış uygulama doğrulama skorunu bozabilir.

```python
# Basit pseudo-labeling akışı
test_pred_for_pl = meta_test.copy()
# Güven eşiği: örnek olarak üst %10 ve alt %10 güvenli kabul edilsin (regresyonda dikkatli olun)
# Regresyonda genelde güvenli pseudo-labeling için modelin güvenilirliğini ve dağılımı kontrol edin.
pl_threshold = np.percentile(test_pred_for_pl, [10, 90])
pl_mask = (test_pred_for_pl <= pl_threshold[0]) | (test_pred_for_pl >= pl_threshold[1])
pseudo_X = X_test[pl_mask]
pseudo_y = test_pred_for_pl[pl_mask]

# Eğitim setine ekle (örnek)
X_pl = pd.concat([X, pseudo_X], axis=0).reset_index(drop=True)
y_pl = pd.concat([y, pd.Series(pseudo_y)], axis=0).reset_index(drop=True)

# Yeniden eğitim (örnek LightGBM)
dtrain_pl = lgb.Dataset(X_pl, label=y_pl)
final_params = lgb_params.copy()
final_model = lgb.train(final_params, dtrain_pl, num_boost_round=1000)
final_preds = final_model.predict(X_test)
```

---

## 10. Final retrain ve submission

**Nerede / Ne zaman / Ne için:** En iyi modeller seçildikten sonra tüm veriyle yeniden eğitip son tahmini üretmek için.

```python
# Final: en iyi modelleri tüm veriyle yeniden eğit
# Örnek: LightGBM final
dtrain_full = lgb.Dataset(X, label=y)
final_lgb = lgb.train(lgb_params, dtrain_full, num_boost_round=final_model.best_iteration if 'final_model' in locals() else 1000)

# Final tahmin
final_pred = final_lgb.predict(X_test)

# Basit ensemble: ağırlıklı ortalama (örnek)
# final_pred = 0.6 * final_pred + 0.4 * meta_test

# Submission dosyası oluşturma (Kaggle Notebook'ta indirilebilir)
submission = pd.DataFrame({ID_COL: test_idx, TARGET_COL: final_pred})
submission.head()
# submission.to_csv('submission.csv', index=False)  # Kaggle'da kaydedip gönderin
```

---

## 11. İzleme, reproducibility ve pratik notlar

- **Seed sabitleme:** Tüm rastgele süreçlerde `RANDOM_SEED` kullanın.  
- **Doğrulama önceliği:** Önce sağlam validation kurun; sonra HPO ve ensemble. Kitapta bu sıranın önemi vurgulanır.  
- **Feature importance:** `feature_importance_df` ile hangi özelliklerin işe yaradığını kontrol edin ve gereksizleri çıkarın.  
- **Model karmaşıklığı:** Üretime almayı düşünüyorsanız ensemble karmaşıklığını ve gecikmeyi göz önünde bulundurun.  
- **Kaynak yönetimi:** GPU/CPU kısıtlarına göre `num_boost_round`, `n_trials` gibi parametreleri ayarlayın.  
- **Deney kaydı:** Hangi parametreyle hangi skor alındı kaydedin (ör. `joblib.dump` ile modelleri saklayın).

---

## Kapanış (kısa özet)
Bu kod **kitaptaki** ana stratejileri tek bir akışta birleştirir: **EDA → Feature Engineering → Sağlam Validation → Baseline modeller → HPO → Stacking → Pseudo‑labeling → Final retrain**. Kod örnekleri minimal ama çalıştırılabilir şekilde verildi; gerçek yarışmada her adımı veri setine göre özelleştirmeniz, daha fazla model eklemeniz, HPO denemelerini artırmanız ve validation stratejisini veri yapısına göre seçmeniz gerekir.


---


# Otomatik, tek komutla çalıştırılabilir Kaggle tarzı tabular pipeline (veri setini otomatik algılar, tüm adımları uygular)

Aşağıdaki Python betiği **senin `train.csv` ve `test.csv` dosyalarını** alır, sütunları otomatik tespit eder (ID, hedef, kategorik/sayısal), veri keşfi ve temizliği yapar, uygun doğrulama stratejisini seçer, temel modelleri (LightGBM, XGBoost), basit HPO seçeneğini, stacking/ensemble ve isteğe bağlı pseudo‑labeling adımlarını uygular. Kod **Kaggle Notebook** veya yerel bir Jupyter ortamında çalıştırılmak üzere tasarlanmıştır.

> **Nasıl kullanılır:** `train.csv` ve `test.csv` aynı klasördeyken bu betiği çalıştır. Betik otomatik olarak `submission.csv` oluşturur. (Gerekirse `ID_COL` ve `TARGET_COL` elle belirtilebilir; otomatik tespit başarısız olursa bu değiştirilmeli.)

---

#### Önemli notlar (kısa)
- Kod **veri yapısına göre** doğrulama stratejisini otomatik seçer (zaman serisi, grup, sınıf dengesine göre stratify).  
- Pseudo‑labeling ve Optuna HPO **isteğe bağlı** (parametrelerle aç/kapa).  
- Gerçek yarışmada parametreleri ve `n_trials`/`num_boost_round` gibi değerleri kaynak ve süreye göre küçültüp büyütün.

---

### Tam betik (çalıştırılabilir)

```python
# Kaggle-style otomatik tabular pipeline
# Türkçe açıklamalar satır içinde. Çalıştırmadan önce gerekli paketleri yükleyin:
# pip install lightgbm xgboost catboost optuna scikit-learn pandas numpy

import os
import random
import warnings
import numpy as np
import pandas as pd
from sklearn.model_selection import KFold, GroupKFold, StratifiedKFold, TimeSeriesSplit
from sklearn.preprocessing import LabelEncoder
from sklearn.metrics import mean_squared_error, roc_auc_score
from sklearn.linear_model import Ridge
import lightgbm as lgb
import xgboost as xgb
import joblib

warnings.filterwarnings('ignore')
RANDOM_SEED = 42
np.random.seed(RANDOM_SEED)
random.seed(RANDOM_SEED)

# ---------- Kullanıcı ayarları (gerekirse değiştirin) ----------
TRAIN_PATH = 'train.csv'
TEST_PATH = 'test.csv'
OUTPUT_SUBMISSION = 'submission.csv'
USE_PSEUDO_LABELING = True        # Testten güvenli tahminleri eklemek istersen True
USE_OPTUNA = False                # HPO yapmak istersen True (optuna kurulu olmalı)
OPTUNA_TRIALS = 20                # Optuna deneme sayısı (küçük örnek)
N_FOLDS = 5
VERBOSE = 100
# ---------------------------------------------------------------

# 1) Veri yükleme ve otomatik sütun tespiti
train = pd.read_csv(TRAIN_PATH)
test = pd.read_csv(TEST_PATH)
print("Train shape:", train.shape, "Test shape:", test.shape)

# Otomatik hedef ve ID tespiti
def infer_id_target(df_train, df_test):
    id_col = None
    target_col = None
    # ID tahmini: 'id' içeren sütun adı varsa
    for c in df_train.columns:
        if c.lower() == 'id' or c.lower().endswith('_id'):
            id_col = c
            break
    # Hedef tahmini: sayısal olup ismi 'target' veya 'y' veya 'label' ise
    for c in df_train.columns:
        if c.lower() in ['target','y','label','outcome']:
            target_col = c
            break
    # Eğer hala yoksa, hedef sütunu sayısal ve train/test arasında yok olan sütun olarak al
    if target_col is None:
        # Heuristik: test setinde olmayan sayısal sütun hedef olabilir
        train_num = df_train.select_dtypes(include=['int64','float64']).columns.tolist()
        test_cols = set(df_test.columns)
        candidates = [c for c in train_num if c not in test_cols]
        if len(candidates) == 1:
            target_col = candidates[0]
    # ID yoksa test'te ortak olan tek sütun olmayan sütun ID olabilir
    if id_col is None:
        # Eğer test ve train arasında ortak olmayan bir sütun varsa test'teki benzersiz sütun id olabilir
        common = set(df_train.columns).intersection(set(df_test.columns))
        for c in df_test.columns:
            if c not in common:
                id_col = c
                break
    return id_col, target_col

ID_COL, TARGET_COL = infer_id_target(train, test)
print("Inferred ID_COL:", ID_COL, "TARGET_COL:", TARGET_COL)

# Eğer otomatik tespit başarısızsa kullanıcı müdahalesi gerekebilir
if TARGET_COL is None:
    raise ValueError("Hedef sütunu otomatik tespit edilemedi. Lütfen TARGET_COL'u elle belirtin.")

# Ayırma
if ID_COL and ID_COL in train.columns:
    train_ids = train[ID_COL].copy()
    test_ids = test[ID_COL].copy()
    train = train.drop(columns=[ID_COL])
    test = test.drop(columns=[ID_COL])
else:
    train_ids = pd.Series(np.arange(len(train)))
    test_ids = pd.Series(np.arange(len(test)))

y = train[TARGET_COL].copy()
train = train.drop(columns=[TARGET_COL])

# 2) Hızlı EDA (özet)
def quick_report(df, name='df'):
    print(f"--- {name} shape: {df.shape} ---")
    print("Dtype counts:", df.dtypes.value_counts().to_dict())
    print("Missing top 10:")
    print(df.isnull().sum().sort_values(ascending=False).head(10))
    print("Sample:")
    display(df.head(2))

quick_report(train, 'train_features')
quick_report(test, 'test_features')
print("Target describe:")
print(y.describe())

# 3) Otomatik feature engineering: kategorikleri encode, basit etkileşimler
def auto_feature_engineer(df):
    df = df.copy()
    # Kategorik sütunları tespit et
    cat_cols = df.select_dtypes(include=['object','category']).columns.tolist()
    # Eğer string sütun yoksa, düşük kardinaliteli sayısal sütunları kategorik say
    for c in df.select_dtypes(include=['int64','float64']).columns:
        if df[c].nunique() < 20:
            # ama eğer çok fazla benzersiz 0/1 değilse
            if df[c].nunique() <= 20 and df[c].nunique() > 2:
                cat_cols.append(c)
    cat_cols = list(dict.fromkeys(cat_cols))  # unique
    num_cols = [c for c in df.columns if c not in cat_cols]
    # Label encode kategorikler
    for c in cat_cols:
        df[c] = df[c].fillna('NA').astype(str)
        le = LabelEncoder()
        try:
            df[c] = le.fit_transform(df[c])
        except:
            df[c] = df[c].astype('category').cat.codes
    # Basit sayısal dönüşümler
    for c in num_cols:
        if df[c].dtype.kind in 'fi':
            df[f'{c}_log1p'] = np.log1p(df[c].fillna(0))
    # Basit etkileşim: ilk iki sayısal sütun varsa
    num_cols2 = [c for c in num_cols if df[c].dtype.kind in 'fi']
    if len(num_cols2) >= 2:
        a, b = num_cols2[0], num_cols2[1]
        df[f'{a}_plus_{b}'] = df[a].fillna(0) + df[b].fillna(0)
        df[f'{a}_mul_{b}'] = df[a].fillna(0) * df[b].fillna(0)
    return df

X = auto_feature_engineer(train)
X_test = auto_feature_engineer(test)
print("After FE shapes:", X.shape, X_test.shape)

# 4) Doğrulama stratejisini otomatik seçme (zaman serisi, grup, stratify, yoksa KFold)
def choose_cv_strategy(df, y, n_folds=5):
    # Zaman serisi tespiti: 'date' veya 'time' içeren sütun varsa
    date_cols = [c for c in df.columns if 'date' in c.lower() or 'time' in c.lower()]
    if len(date_cols) > 0:
        print("Time-like column detected, using TimeSeriesSplit.")
        return TimeSeriesSplit(n_splits=n_folds)
    # Grup tespiti: 'group' içeren sütun varsa
    group_cols = [c for c in df.columns if 'group' in c.lower()]
    if len(group_cols) > 0:
        print("Group-like column detected, using GroupKFold.")
        return GroupKFold(n_splits=n_folds)
    # Eğer hedef sınıflı ve az sayıda benzersizse stratify
    if y.nunique() <= 20 and y.dtype.kind in 'i':
        print("Small number of discrete target values, using StratifiedKFold.")
        return StratifiedKFold(n_splits=n_folds, shuffle=True, random_state=RANDOM_SEED)
    # Default
    print("Using standard KFold.")
    return KFold(n_splits=n_folds, shuffle=True, random_state=RANDOM_SEED)

cv = choose_cv_strategy(X, y, n_folds=N_FOLDS)

# 5) Baseline LightGBM CV eğitimi (OOF + test tahmini)
def run_lgb_cv(X, y, X_test, cv, params=None, num_boost_round=2000, early_stopping=100):
    if params is None:
        params = {
            'objective': 'regression',
            'metric': 'rmse',
            'boosting_type': 'gbdt',
            'learning_rate': 0.05,
            'num_leaves': 31,
            'feature_fraction': 0.8,
            'bagging_fraction': 0.8,
            'bagging_freq': 5,
            'seed': RANDOM_SEED,
            'verbosity': -1
        }
    oof = np.zeros(X.shape[0])
    preds = np.zeros(X_test.shape[0])
    feature_importance = pd.DataFrame()
    for fold, (tr_idx, val_idx) in enumerate(cv.split(X, y) if hasattr(cv, 'split') else cv.split(X, y)):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
        dtrain = lgb.Dataset(X_tr, label=y_tr)
        dval = lgb.Dataset(X_val, label=y_val)
        model = lgb.train(params, dtrain, num_boost_round=num_boost_round, valid_sets=[dval],
                          early_stopping_rounds=early_stopping, verbose_eval=VERBOSE if VERBOSE else False)
        oof[val_idx] = model.predict(X_val, num_iteration=model.best_iteration)
        preds += model.predict(X_test, num_iteration=model.best_iteration) / N_FOLDS
        # feature importance
        fi = pd.DataFrame({'feature': X.columns, 'importance': model.feature_importance(importance_type='gain'), 'fold': fold+1})
        feature_importance = pd.concat([feature_importance, fi], axis=0)
    score = np.sqrt(mean_squared_error(y, oof))
    print("OOF RMSE:", score)
    return oof, preds, feature_importance, score

lgb_oof, lgb_test_preds, fi_df, lgb_score = run_lgb_cv(X, y, X_test, cv)

# 6) XGBoost kısa deneme (aynı CV ile)
def run_xgb_cv(X, y, X_test, cv, params=None, num_boost_round=2000, early_stopping=100):
    if params is None:
        params = {'objective':'reg:squarederror','eval_metric':'rmse','seed':RANDOM_SEED}
    oof = np.zeros(X.shape[0])
    preds = np.zeros(X_test.shape[0])
    for fold, (tr_idx, val_idx) in enumerate(cv.split(X, y)):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
        dtrain = xgb.DMatrix(X_tr, label=y_tr)
        dval = xgb.DMatrix(X_val, label=y_val)
        bst = xgb.train(params, dtrain, num_boost_round=num_boost_round, evals=[(dval,'val')],
                        early_stopping_rounds=early_stopping, verbose_eval=False)
        oof[val_idx] = bst.predict(dval, ntree_limit=bst.best_ntree_limit)
        preds += bst.predict(xgb.DMatrix(X_test), ntree_limit=bst.best_ntree_limit) / N_FOLDS
    score = np.sqrt(mean_squared_error(y, oof))
    print("XGB OOF RMSE:", score)
    return oof, preds, score

xgb_oof, xgb_test_preds, xgb_score = run_xgb_cv(X, y, X_test, cv)

# 7) Basit stacking (OOF'ları meta-modele ver)
level0_oof = np.column_stack([lgb_oof, xgb_oof])
level0_test = np.column_stack([lgb_test_preds, xgb_test_preds])

meta = Ridge(alpha=1.0, random_state=RANDOM_SEED)
meta_oof = np.zeros(level0_oof.shape[0])
for tr_idx, val_idx in cv.split(level0_oof, y):
    meta.fit(level0_oof[tr_idx], y.iloc[tr_idx])
    meta_oof[val_idx] = meta.predict(level0_oof[val_idx])
meta_test = meta.predict(level0_test)
print("Stacked OOF RMSE:", np.sqrt(mean_squared_error(y, meta_oof)))

# 8) İsteğe bağlı pseudo-labeling (çok dikkatli kullan)
if USE_PSEUDO_LABELING:
    # Basit güven eşiği: test tahminlerinin uç değerlerini al (örnek heuristik)
    preds_for_pl = meta_test.copy()
    low, high = np.percentile(preds_for_pl, [5,95])
    mask = (preds_for_pl <= low) | (preds_for_pl >= high)
    if mask.sum() > 0:
        pseudo_X = X_test[mask]
        pseudo_y = preds_for_pl[mask]
        print(f"Pseudo-labeling: adding {len(pseudo_y)} pseudo samples.")
        X_pl = pd.concat([X, pseudo_X], axis=0).reset_index(drop=True)
        y_pl = pd.concat([y, pd.Series(pseudo_y)], axis=0).reset_index(drop=True)
        # Hızlı yeniden eğitim LightGBM (tüm veri)
        dtrain_pl = lgb.Dataset(X_pl, label=y_pl)
        final_params = {
            'objective': 'regression','metric': 'rmse','boosting_type': 'gbdt',
            'learning_rate': 0.03,'num_leaves': 31,'feature_fraction': 0.8,'bagging_fraction': 0.8,'bagging_freq': 5,
            'seed': RANDOM_SEED,'verbosity': -1
        }
        final_model = lgb.train(final_params, dtrain_pl, num_boost_round=1000, verbose_eval=VERBOSE if VERBOSE else False)
        final_preds = final_model.predict(X_test)
    else:
        print("Pseudo-label mask empty; skipping pseudo-labeling.")
        final_preds = meta_test.copy()
else:
    final_preds = meta_test.copy()

# 9) Basit ağırlıklı ensemble (son karar)
# Ağırlıkları basitçe belirliyoruz; isterseniz Optuna ile optimize edebilirsiniz
final_ensemble = 0.5 * final_preds + 0.3 * lgb_test_preds + 0.2 * xgb_test_preds

# 10) Submission oluşturma
submission = pd.DataFrame({ 'id': test_ids if 'test_ids' in locals() else np.arange(len(X_test)),
                            TARGET_COL: final_ensemble })
submission.to_csv(OUTPUT_SUBMISSION, index=False)
print("Submission saved to", OUTPUT_SUBMISSION)

# 11) Model ve feature importance kaydı (isteğe bağlı)
try:
    fi_mean = fi_df.groupby('feature')['importance'].mean().sort_values(ascending=False).head(30)
    print("Top features:\n", fi_mean)
except:
    pass

# 12) Özet skorlar (CV üzerinden)
print("LightGBM CV RMSE:", lgb_score)
print("XGBoost CV RMSE:", xgb_score)
print("Stacked CV RMSE:", np.sqrt(mean_squared_error(y, meta_oof)))
```

---

### Açıklamalar ve özelleştirme önerileri
- **Hedef tipi:** Kod hedefin sürekli (regresyon) olduğunu varsayar. Eğer sınıflandırma ise `lgb` ve `xgb` parametrelerinde `objective` ve `metric` değiştirilmeli; `roc_auc_score` gibi uygun metrik kullanılmalı.  
- **Doğrulama:** Kod otomatik seçer; eğer veri zaman serisi ise `date` sütununu belirtmek daha güvenli olur.  
- **HPO:** `USE_OPTUNA = True` yapıp `OPTUNA_TRIALS` artırarak Optuna ile parametre araması ekleyebilirim (Optuna kurulumu gerekir).  
- **Pseudo‑labeling:** Risklidir; doğrulama skorunu bozabilir. Küçük adımlarla ve doğrulama ile test edin.  
- **Feature engineering:** Kitaptaki öneriler çok daha geniştir; burada temel ve güvenli dönüşümler yer alır. Daha ileri teknikler (target encoding, interaction features, PCA/UMAP, autoencoders) veri setine göre eklenmelidir.  
- **Üretim:** Ensemble çok karmaşıksa üretime almak zor olabilir; üretim için tek, iyi optimize edilmiş model tercih edin.

---

# Otomatik, Optuna destekli ve görev tipini (sınıflandırma/regresyon) algılayan tam pipeline (çalıştırılabilir Python)

Aşağıdaki betik **senin `train.csv` ve `test.csv` dosyalarını** alıp sütunları otomatik algılar, hedefin türüne göre (sürekli → regresyon; ayrık/etiket → sınıflandırma) uygun metrik ve model ayarlarını seçer, Optuna ile LightGBM hiperparametre optimizasyonunu etkinleştirir, CV ile OOF tahminleri üretir, XGBoost ile karşılaştırma yapar ve basit stacking uygular. Betik Kaggle Notebook veya yerel Jupyter ortamında çalıştırılmak üzere tasarlanmıştır.

> **Kullanım:** Betiği çalıştırmadan önce gerekli paketleri yükleyin (`lightgbm`, `xgboost`, `optuna`, `scikit-learn`, `pandas`, `numpy`). Betik veri dosyalarını aynı çalışma dizininde `train.csv` ve `test.csv` olarak bekler. Çıktı olarak `submission_df` adlı DataFrame oluşturulur; isterseniz bunu pandas ile kaydedebilirsiniz.

---

### Önemli özellikler
- **Otomatik hedef ve ID tespiti** (heuristikler kullanır).  
- **Görev tipi otomatik algılama** (regresyon / sınıflandırma; ikili/multi sınıf ayrımı).  
- **Doğrulama stratejisi otomatik seçimi** (zaman serisi, grup, stratify veya KFold).  
- **Optuna ile LightGBM HPO** (isteğe bağlı, parametre ile aç/kapa).  
- **XGBoost karşılaştırması** ve **basit Ridge meta-model stacking**.  
- **Pseudo‑labeling** isteğe bağlı (varsayılan kapalı; dikkatle kullanın).

---

### Tam betik

```python
# Otomatik Kaggle-style tabular pipeline
# Gerekli paketler: pandas, numpy, scikit-learn, lightgbm, xgboost, optuna (isteğe bağlı), joblib
import os
import random
import warnings
import numpy as np
import pandas as pd
from sklearn.model_selection import KFold, StratifiedKFold, GroupKFold, TimeSeriesSplit
from sklearn.preprocessing import LabelEncoder
from sklearn.metrics import mean_squared_error, roc_auc_score, log_loss, accuracy_score
from sklearn.linear_model import Ridge
import lightgbm as lgb
import xgboost as xgb

warnings.filterwarnings('ignore')
RANDOM_SEED = 42
np.random.seed(RANDOM_SEED)
random.seed(RANDOM_SEED)

# ---------- Kullanıcı ayarları ----------
TRAIN_PATH = 'train.csv'   # çalışma dizininde olmalı
TEST_PATH = 'test.csv'
USE_OPTUNA = True          # Optuna ile HPO yapmak istersen True
OPTUNA_TRIALS = 40         # kaynaklara göre ayarla
USE_PSEUDO = False         # pseudo-labeling kullanmak istersen True (dikkatli kullan)
N_FOLDS = 5
VERBOSE = 100
# ----------------------------------------

# 1) Veri yükleme ve otomatik ID/target tespiti
train = pd.read_csv(TRAIN_PATH)
test = pd.read_csv(TEST_PATH)
print("Train shape:", train.shape, "Test shape:", test.shape)

def infer_id_target(df_train, df_test):
    id_col = None
    target_col = None
    # ID heuristiği
    for c in df_train.columns:
        if c.lower() == 'id' or c.lower().endswith('_id'):
            id_col = c
            break
    # target heuristiği
    for c in df_train.columns:
        if c.lower() in ['target','y','label','outcome']:
            target_col = c
            break
    if target_col is None:
        train_num = df_train.select_dtypes(include=['int64','float64']).columns.tolist()
        test_cols = set(df_test.columns)
        candidates = [c for c in train_num if c not in test_cols]
        if len(candidates) == 1:
            target_col = candidates[0]
    # fallback: son sütun hedef olabilir
    if target_col is None:
        target_col = df_train.columns[-1]
    # id fallback
    if id_col is None:
        common = set(df_train.columns).intersection(set(df_test.columns))
        for c in df_test.columns:
            if c not in common:
                id_col = c
                break
    return id_col, target_col

ID_COL, TARGET_COL = infer_id_target(train, test)
print("Inferred ID_COL:", ID_COL, "TARGET_COL:", TARGET_COL)

# Ayırma
if ID_COL and ID_COL in train.columns:
    train_ids = train[ID_COL].copy()
    test_ids = test[ID_COL].copy()
    train = train.drop(columns=[ID_COL])
    test = test.drop(columns=[ID_COL])
else:
    train_ids = pd.Series(np.arange(len(train)))
    test_ids = pd.Series(np.arange(len(test)))

y = train[TARGET_COL].copy()
train = train.drop(columns=[TARGET_COL])

# 2) Görev tipi algılama (regresyon / sınıflandırma)
def detect_task(y):
    if y.dtype.kind in 'f':  # float hedef → regresyon
        return 'regression'
    if y.dtype.kind in 'i':
        # eğer çok az benzersiz değer varsa sınıflandırma
        if y.nunique() <= 20:
            # ikili mi çok sınıflı mı kontrolü
            if y.nunique() == 2:
                return 'binary'
            else:
                return 'multiclass'
        else:
            return 'regression'
    # string etiketler -> sınıflandırma
    return 'binary' if y.nunique() == 2 else 'multiclass'

TASK = detect_task(y)
print("Detected task:", TASK)

# 3) Hızlı EDA ve feature engineering (otomatik)
def quick_report(df, name='df'):
    print(f"{name} shape: {df.shape}")
    print("Missing top 10:")
    print(df.isnull().sum().sort_values(ascending=False).head(10))

quick_report(train, 'train_features')
quick_report(test, 'test_features')

def auto_fe(df):
    df = df.copy()
    # kategorik tespit
    cat_cols = df.select_dtypes(include=['object','category']).columns.tolist()
    # düşük kardinaliteli sayısalları kategorik say
    for c in df.select_dtypes(include=['int64','float64']).columns:
        if df[c].nunique() <= 20 and df[c].nunique() > 2:
            cat_cols.append(c)
    cat_cols = list(dict.fromkeys(cat_cols))
    num_cols = [c for c in df.columns if c not in cat_cols]
    # label encode kategorikler
    for c in cat_cols:
        df[c] = df[c].fillna('NA').astype(str)
        le = LabelEncoder()
        try:
            df[c] = le.fit_transform(df[c])
        except:
            df[c] = df[c].astype('category').cat.codes
    # basit sayısal dönüşümler
    for c in num_cols:
        if df[c].dtype.kind in 'fi':
            df[f'{c}_log1p'] = np.log1p(df[c].fillna(0))
    # basit etkileşim
    num_cols2 = [c for c in num_cols if df[c].dtype.kind in 'fi']
    if len(num_cols2) >= 2:
        a, b = num_cols2[0], num_cols2[1]
        df[f'{a}_plus_{b}'] = df[a].fillna(0) + df[b].fillna(0)
    return df

X = auto_fe(train)
X_test = auto_fe(test)
print("After FE shapes:", X.shape, X_test.shape)

# 4) CV stratejisi otomatik seçimi
def choose_cv(X, y, n_folds=5):
    date_cols = [c for c in X.columns if 'date' in c.lower() or 'time' in c.lower()]
    if len(date_cols) > 0:
        print("Using TimeSeriesSplit")
        return TimeSeriesSplit(n_splits=n_folds)
    group_cols = [c for c in X.columns if 'group' in c.lower()]
    if len(group_cols) > 0:
        print("Using GroupKFold")
        return GroupKFold(n_splits=n_folds)
    if TASK in ['binary','multiclass']:
        # stratify için hedefın çok sınıflı olup olmadığına bak
        if y.nunique() > 1:
            print("Using StratifiedKFold")
            return StratifiedKFold(n_splits=n_folds, shuffle=True, random_state=RANDOM_SEED)
    print("Using KFold")
    return KFold(n_splits=n_folds, shuffle=True, random_state=RANDOM_SEED)

cv = choose_cv(X, y, n_folds=N_FOLDS)

# 5) Metriği seç
if TASK == 'regression':
    def score_fn(y_true, y_pred): return np.sqrt(mean_squared_error(y_true, y_pred))
    LGB_OBJECTIVE = 'regression'
    LGB_METRIC = 'rmse'
elif TASK == 'binary':
    def score_fn(y_true, y_pred): return roc_auc_score(y_true, y_pred)
    LGB_OBJECTIVE = 'binary'
    LGB_METRIC = 'auc'
else:  # multiclass
    def score_fn(y_true, y_pred): return accuracy_score(y_true, np.argmax(y_pred, axis=1))
    LGB_OBJECTIVE = 'multiclass'
    LGB_METRIC = 'multi_logloss'

print("Using metric for evaluation:", LGB_METRIC)

# 6) Optuna ile LightGBM HPO (isteğe bağlı)
best_params = None
if USE_OPTUNA:
    try:
        import optuna
        def optuna_objective(trial):
            param = {
                'objective': LGB_OBJECTIVE,
                'metric': LGB_METRIC,
                'boosting_type': 'gbdt',
                'learning_rate': trial.suggest_loguniform('learning_rate', 0.01, 0.2),
                'num_leaves': trial.suggest_int('num_leaves', 16, 256),
                'feature_fraction': trial.suggest_uniform('feature_fraction', 0.4, 1.0),
                'bagging_fraction': trial.suggest_uniform('bagging_fraction', 0.4, 1.0),
                'bagging_freq': trial.suggest_int('bagging_freq', 1, 10),
                'min_data_in_leaf': trial.suggest_int('min_data_in_leaf', 5, 200),
                'lambda_l1': trial.suggest_loguniform('lambda_l1', 1e-8, 10.0),
                'lambda_l2': trial.suggest_loguniform('lambda_l2', 1e-8, 10.0),
                'seed': RANDOM_SEED,
                'verbosity': -1
            }
            cv_scores = []
            for tr_idx, val_idx in cv.split(X, y):
                X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
                y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
                dtrain = lgb.Dataset(X_tr, label=y_tr)
                dval = lgb.Dataset(X_val, label=y_val)
                model = lgb.train(param, dtrain, num_boost_round=2000, valid_sets=[dval],
                                  early_stopping_rounds=50, verbose_eval=False)
                if TASK == 'regression':
                    preds = model.predict(X_val, num_iteration=model.best_iteration)
                    cv_scores.append(np.sqrt(mean_squared_error(y_val, preds)))
                elif TASK == 'binary':
                    preds = model.predict(X_val, num_iteration=model.best_iteration)
                    cv_scores.append(roc_auc_score(y_val, preds))
                else:
                    preds = model.predict(X_val, num_iteration=model.best_iteration)
                    cv_scores.append(log_loss(y_val, preds))
            # Optuna minimize by default; for auc we want maximize -> return negative
            if TASK == 'binary':
                return -np.mean(cv_scores)
            return np.mean(cv_scores)
        study = optuna.create_study(direction='minimize' if TASK!='binary' else 'maximize')
        # For binary we created objective returning negative; adjust direction accordingly
        if TASK == 'binary':
            # make objective return negative AUC, but set direction='minimize' for simplicity
            study = optuna.create_study(direction='minimize')
            def wrapped_obj(trial):
                return -optuna_objective(trial)
            study.optimize(wrapped_obj, n_trials=OPTUNA_TRIALS)
        else:
            study.optimize(optuna_objective, n_trials=OPTUNA_TRIALS)
        best_params = study.best_params
        # ensure required keys
        best_params.update({'objective': LGB_OBJECTIVE, 'metric': LGB_METRIC, 'seed': RANDOM_SEED, 'verbosity': -1})
        print("Optuna best params:", best_params)
    except Exception as e:
        print("Optuna kurulumu veya çalıştırma hatası:", e)
        USE_OPTUNA = False

# 7) CV ile LightGBM eğitimi (OOF + test)
def run_lgb_cv(X, y, X_test, cv, params=None, num_boost_round=2000, early_stopping=100):
    if params is None:
        params = {
            'objective': LGB_OBJECTIVE,
            'metric': LGB_METRIC,
            'boosting_type': 'gbdt',
            'learning_rate': 0.05,
            'num_leaves': 31,
            'feature_fraction': 0.8,
            'bagging_fraction': 0.8,
            'bagging_freq': 5,
            'seed': RANDOM_SEED,
            'verbosity': -1
        }
    oof = np.zeros(X.shape[0]) if TASK!='multiclass' else np.zeros((X.shape[0], len(np.unique(y))))
    preds = np.zeros(X_test.shape[0]) if TASK!='multiclass' else np.zeros((X_test.shape[0], len(np.unique(y))))
    for fold, (tr_idx, val_idx) in enumerate(cv.split(X, y)):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
        dtrain = lgb.Dataset(X_tr, label=y_tr)
        dval = lgb.Dataset(X_val, label=y_val)
        model = lgb.train(params, dtrain, num_boost_round=num_boost_round, valid_sets=[dval],
                          early_stopping_rounds=early_stopping, verbose_eval=VERBOSE if VERBOSE else False)
        if TASK == 'multiclass':
            oof[val_idx, :] = model.predict(X_val, num_iteration=model.best_iteration)
            preds += model.predict(X_test, num_iteration=model.best_iteration) / N_FOLDS
        else:
            oof[val_idx] = model.predict(X_val, num_iteration=model.best_iteration)
            preds += model.predict(X_test, num_iteration=model.best_iteration) / N_FOLDS
    return oof, preds

lgb_params = best_params if best_params is not None else None
lgb_oof, lgb_test = run_lgb_cv(X, y, X_test, cv, params=lgb_params)

# 8) XGBoost kısa karşılaştırma (aynı CV)
def run_xgb_cv(X, y, X_test, cv):
    oof = np.zeros(X.shape[0])
    preds = np.zeros(X_test.shape[0])
    params = {'objective':'reg:squarederror','eval_metric':'rmse','seed':RANDOM_SEED}
    for fold, (tr_idx, val_idx) in enumerate(cv.split(X, y)):
        X_tr, X_val = X.iloc[tr_idx], X.iloc[val_idx]
        y_tr, y_val = y.iloc[tr_idx], y.iloc[val_idx]
        dtrain = xgb.DMatrix(X_tr, label=y_tr)
        dval = xgb.DMatrix(X_val, label=y_val)
        bst = xgb.train(params, dtrain, num_boost_round=1000, evals=[(dval,'val')],
                        early_stopping_rounds=50, verbose_eval=False)
        oof[val_idx] = bst.predict(dval, ntree_limit=bst.best_ntree_limit)
        preds += bst.predict(xgb.DMatrix(X_test), ntree_limit=bst.best_ntree_limit) / N_FOLDS
    return oof, preds

xgb_oof, xgb_test = run_xgb_cv(X, y, X_test, cv)

# 9) Basit stacking (Ridge meta-model)
if TASK == 'multiclass':
    # multiclass stacking için argmax veya soft stacking gerekebilir; burada basitçe argmax kullanıyoruz
    level0_oof = np.column_stack([np.argmax(lgb_oof, axis=1), np.argmax(xgb_oof.reshape(-1,1), axis=1)])
    level0_test = np.column_stack([np.argmax(lgb_test, axis=1), np.argmax(xgb_test.reshape(-1,1), axis=1)])
else:
    level0_oof = np.column_stack([lgb_oof, xgb_oof])
    level0_test = np.column_stack([lgb_test, xgb_test])

meta = Ridge(alpha=1.0, random_state=RANDOM_SEED)
meta_oof = np.zeros(level0_oof.shape[0])
for tr_idx, val_idx in cv.split(level0_oof, y):
    meta.fit(level0_oof[tr_idx], y.iloc[tr_idx])
    meta_oof[val_idx] = meta.predict(level0_oof[val_idx])
meta_test = meta.predict(level0_test)

# 10) İsteğe bağlı pseudo-labeling (çok dikkatli)
if USE_PSEUDO:
    # regresyon için uç tahminleri, sınıflandırma için yüksek güvenli sınıfları seçme mantığı
    if TASK == 'regression':
        low, high = np.percentile(meta_test, [5,95])
        mask = (meta_test <= low) | (meta_test >= high)
        if mask.sum() > 0:
            X_pl = pd.concat([X, X_test[mask]], axis=0).reset_index(drop=True)
            y_pl = pd.concat([y, pd.Series(meta_test[mask])], axis=0).reset_index(drop=True)
            # hızlı yeniden eğitim LightGBM (tüm veri)
            dtrain_pl = lgb.Dataset(X_pl, label=y_pl)
            final_params = lgb_params if lgb_params is not None else {'objective':LGB_OBJECTIVE,'metric':LGB_METRIC,'seed':RANDOM_SEED,'verbosity':-1}
            final_model = lgb.train(final_params, dtrain_pl, num_boost_round=1000, verbose_eval=False)
            final_preds = final_model.predict(X_test)
        else:
            final_preds = meta_test.copy()
    else:
        # sınıflandırmada pseudo-labeling daha karmaşık; burada kapalı bırakıyoruz
        final_preds = meta_test.copy()
else:
    final_preds = meta_test.copy()

# 11) Son ensemble (basit ağırlıklı)
final_ensemble = 0.5 * final_preds + 0.25 * lgb_test + 0.25 * xgb_test

# 12) Değerlendirme (CV skorları)
if TASK == 'regression':
    print("LightGBM OOF RMSE:", np.sqrt(mean_squared_error(y, lgb_oof)))
    print("XGBoost OOF RMSE:", np.sqrt(mean_squared_error(y, xgb_oof)))
    print("Stacked OOF RMSE:", np.sqrt(mean_squared_error(y, meta_oof)))
else:
    if TASK == 'binary':
        print("LightGBM OOF AUC:", roc_auc_score(y, lgb_oof))
        print("XGBoost OOF AUC:", roc_auc_score(y, xgb_oof))
        print("Stacked OOF AUC:", roc_auc_score(y, meta_oof))
    else:
        print("Multiclass stacked accuracy (approx):", accuracy_score(y, np.round(meta_oof)))

# 13) Submission DataFrame hazır
submission_df = pd.DataFrame({ 'id': test_ids, 'prediction': final_ensemble })

# submission_df hazır; kaydetmek isterseniz pandas.to_csv kullanabilirsiniz.
print("Pipeline tamamlandı. 'submission_df' değişkeni hazır.")
```

---

### Son notlar ve öneriler
- **Optuna** etkinleştirildiğinde kaynak tüketimi artar; `OPTUNA_TRIALS` değerini makinenize göre ayarlayın.  
- **Sınıflandırma için** LightGBM parametreleri ve değerlendirme mantığı (özellikle çok sınıflı) daha ince ayar gerektirir; yukarıdaki kod temel bir otomasyon sağlar.  
- **Pseudo‑labeling** risklidir: doğrulama stratejinizi bozmadan küçük adımlarla deneyin.  
- **Feature engineering** kitapta çok daha geniş anlatılır; bu betik temel, güvenli dönüşümler uygular. Veri setine özel hedef-encoding, tarih parçalama, etkileşimler, outlier işlemleri eklemek genelde büyük fark yaratır.  
- Betiği çalıştırıp `submission_df` ve CV skorlarını paylaşırsanız, sonuçlara göre hangi adımları (daha fazla FE, farklı CV, Optuna parametreleri, stacking stratejileri) önceliklendireceğinizi adım adım düzenleyebilirim.
