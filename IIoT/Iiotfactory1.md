# CSI Test bed for IIoT

## Processing Industrial Data from CSI test bed

### Introduction

- CSI test bed data

## Code processing

- Let include the libraries

```
from pyspark.sql.functions import from_json, col
from pyspark.sql.types import StructType, StructField, StringType, DoubleType
from pyspark.sql.functions import split, explode
```

- Set the path

```
jsonpath = "abfss://container@storageaccount.dfs.core.windows.net/vialtest/*.json"
```

- Read the frame

```
df = spark.read.json(jsonpath)
```

- Print Schema

```
df.printSchema
```

- explode the data
- create a new column

```
from pyspark.sql import functions as f

df2 = df.withColumn("gatewayDataexp",f.explode('gatewayData'))
df3 = df2.withColumn("vqtsexp",f.explode('gatewayDataexp.vqts'))
```

- display dataframe

```
display(df3.limit(10))
```

- save the dataframe

```
df_final = df3.selectExpr("ConnectionDeviceId","EventProcessedUtcTime","EventEnqueuedUtcTime","ConnectionDeviceId","gatewayDataexp.model_id as model_id","gatewayDataexp.tag_id as tag_id"
,"vqtsexp.v as v","vqtsexp.q as q","vqtsexp.t as t"
)
```

- display dataframe

```
display(df_final.limit(10))
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi13.jpg "Architecture")

- Create tag id formatted

```
from pyspark.sql.functions import *

df_final = df_final.withColumn('tagidformatted', regexp_replace('tag_id', 'ra-cip-value://driver-cip/192.168.1.11/Production_History', ''))
```

```
df_final = df_final.withColumn('tagidformatted', regexp_replace('tagidformatted', "[^0-9A-Za-z]", ''))
df_final = df_final.withColumn('tagidformatted', regexp_replace('tagidformatted', "0", ''))
```

```
display(df_final.limit(5))
```

- Display and list only DryFillFinishTimeYear

```
display(df_final.where(df_final.tagidformatted == 'DryFillFinishTimeYear'))
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi15.jpg "Architecture")

- pivot the data

```
from pyspark.sql.functions import *
import pyspark.sql.functions as F

pivotDF = df1.groupBy('t', 'ConnectionDeviceId','tagidformatted').pivot("tagidformatted").agg(F.first('v')).fillna(0)
pivotDF.show()
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi14.jpg "Architecture")

```
display(pivotDF.where(pivotDF.DryFillFinishTimeYear != '0'))
```

- save the pivot

```
pivotDF.repartition(1).write.mode("overwrite").parquet('abfss://container@storagename.dfs.core.windows.net/pivotdata/')
```

- now read the pivot data stored

```
%%pyspark
df = spark.read.load('abfss://container@storagename.dfs.core.windows.net/pivotdata/*.snappy.parquet', format='parquet')
display(df.limit(10))
```

```
display(df.where(df.DryFillFinishTimeYear != '0'))
```