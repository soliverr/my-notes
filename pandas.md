---
id: 3saj5m3bjkgscgs1fesjucf
title: Pandas
desc: ''
updated: 1686751496089
created: 1649404345205
---

## Преобразование типов в Pandas

```python
import pandas as pd
import numpy as np

# Все типы object
df = pd.DataFrame([[np.nan, 2, np.nan, 0], [3, 4, np.nan, 1], [np.nan, np.nan, np.nan, np.nan], [np.nan, 3, np.nan, 4]], columns=list("ABCD"))

# Преобразование типов

df['A'] = df['A'].astype("Int64")
df["B"] = df["B"].astype("float64")
df["D"] = df["D"].astype("string")

type(df['A'])
Out[76]: pandas.core.series.Series
type(df['A'][0])
Out[77]: pandas._libs.missing.NAType
type(pd.NaT)
Out[78]: pandas._libs.tslibs.nattype.NaTType
```

То есть, для типа `Int64` и `string` значение `NaN` кодируется типом `pandas._libs.missing.NAType`, отличным от типа `pd.NaT`

При этом преобразование `nan` в `None` для этих типов сбрасывают тип поля в `object`:

```python

df.replace([np.nan], [None])
df2 = df.applymap(lambda x: x if not pd.isnull(x) else None).replace([np.nan], [None])

records = df2.to_dict("records")
```

`to_dict` пользуется внутренними типами:

```python
for k, v in record.items():
                if pd.isna(v):
                    record[k] = None
```

# Иерархия

* https://stackoverflow.com/questions/46722740/hierarchical-data-efficiently-build-a-list-of-every-descendant-for-each-node