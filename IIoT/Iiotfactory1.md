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

- Set the schema

```
from pyspark.sql.types import *

schema = StructType([
  StructField("ConnectionDeviceId", StringType()),
  StructField("EnqueuedTime", StringType()),
  StructField("EventEnqueuedUtcTime", StringType()),
  StructField("EventProcessedUtcTime", StringType()),
  StructField("gatewayData", ArrayType(
      StructType([
          StructField("mimeType", StringType()),
          StructField("model_id", StringType()),
          StructField("tag_id", StringType()),
          StructField("vqts", ArrayType(
              StructType([
                      StructField('q', StringType()),
                      StructField('t', StringType()),
                      StructField('v', StringType())
                  ])
                )
            )
        ])
    )
)
])
```

- Read the frame

```
df = spark.read.schema(schema).json(jsonpath)
```

- Print Schema

```
df.printSchema
```

- explode the data
- create a new column

```
import pyspark.sql.functions as f
from pyspark.sql.types import *

df = df.withColumn('tag_id', f.explode('gatewayData.tag_id'))
df = df.withColumn('vqts', f.explode('gatewayData.vqts'))
```

- display dataframe

```
display(df)
```

- split sensor data

```
df = df.withColumn('vqts1', f.explode('vqts'))
```

- display dataframe

```
display(df.select("ConnectionDeviceId", "tag_id", "vqts1.q", "vqts1.t", "vqts1.v"))
```

- Write back dataframe

```
df.select("ConnectionDeviceId", "tag_id", "vqts1.q", "vqts1.t", "vqts1.v").repartition(1).write.mode("overwrite").parquet('abfss://container@storageaccount.dfs.core.windows.net/rawdata/')
```

- Create a new data frame with the new schema

```
df1 = df.select("ConnectionDeviceId", "tag_id", "vqts1.q", "vqts1.t", "vqts1.v")
```

- Let's create column which is readable
- Create a column with actual name of the sensor

```
from pyspark.sql.functions import *

df1 = df1.withColumn('tagidformatted', regexp_replace('tag_id', 'ra-cip-value://driver-cip/192.168.1.11/Production_History', ''))
```

```
df1 = df1.withColumn('tagidformatted', regexp_replace('tagidformatted', "[^0-9A-Za-z]", ''))
df1 = df1.withColumn('tagidformatted', regexp_replace('tagidformatted', "0", ''))
```

- display dataframe

```
display(df1.limit(10))
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi13.jpg "Architecture")

- Display and list only DryFillFinishTimeYear

```
display(df1.where(df1.tagidformatted == 'DryFillFinishTimeYear'))
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