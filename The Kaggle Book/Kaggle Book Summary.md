Yıllara göre kullanılan metriklerin ve sayılarını tablo hâline getirmek için gerekli örnek kod;

```python
import numpy as np
import pandas as pd

# Competitions CSV tablosunu oku
comps = pd.read_csv("/kaggle/input/meta-kaggle/Competitions.csv")

# İlgilenilen sütunlar
evaluation = ['EvaluationAlgorithmAbbreviation',
              'EvaluationAlgorithmName',
              'EvaluationAlgorithmDescription',]

compt = ['Title', 'EnabledDate', 'HostSegmentTitle']

# Analiz için kopya DataFrame oluştur
df = comps[compt + evaluation].copy()
df['year'] = pd.to_datetime(df.EnabledDate).dt.year.values
df['comps'] = 1

# 2015 ve sonrası yarışmaları seç
time_select = df.year >= 2015

# Featured ve Research türündeki yarışmalar
competition_type_select = df.HostSegmentTitle.isin(['Featured', 'Research'])

# Pivot tablo oluştur ve yıllara göre metrik sayısını hesapla
pd.pivot_table(df[time_select & competition_type_select],
               values='comps',
               index=['EvaluationAlgorithmAbbreviation'],
               columns=['year'],
               fill_value=0.0,
               aggfunc=np.sum,
               margins=True
              ).sort_values(by=('All'), ascending=False).iloc[1:, :].head(20)
```
