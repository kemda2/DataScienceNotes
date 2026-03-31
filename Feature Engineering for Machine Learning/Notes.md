# Dinleme Sayısını Binleme

```py
import pandas as pd
listen_count = pd.read_csv('millionsong/train_triplets.txt.zip', header=None, delimiter='\t')
# The table contains user-song-count triplets. Only nonzero counts are
# included. Hence, to binarize the count, we just need to set the entire
# count column to 1.
listen_count[2] = 1
```

# Yelp Veri Kümesindeki İşletme Yorum Sayılarının Görselleştirilmesi
```py
import pandas as pd
import json
# Load the data about businesses
biz_file = open('yelp_academic_dataset_business.json')
biz_df = pd.DataFrame([json.loads(x) for x in biz_file.readlines()])
biz_file.close()
import matplotlib.pyplot as plt
import seaborn as sns
# Plot the histogram of the review counts
sns.set_style('whitegrid')
fig, ax = plt.subplots()
biz_df['review_count'].hist(ax=ax, bins=100)
ax.set_yscale('log')
ax.tick_params(labelsize=14)
ax.set_xlabel('Review Count', fontsize=14)
ax.set_ylabel('Occurrence', fontsize=14)
```