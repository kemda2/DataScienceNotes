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
