---
title: '[COLAB] how to upload csv file to colab'
date: 2019-04-18
---

## Local

```python
from google.colab import files
uploaded = files.upload()
```

chose file from local

```python
# Read file as panda dataframe
import pandas as pd
urls = pd.read_csv('youtube_urls.csv')
```

## Git

file size should be under 25MB

```python
url = 'copied_raw_GH_link'
df = pd.read_csv(url)
# Dataset is now stored in a Pandas Dataframe
```

## don't use "From Google Drive via PyDrive" 
