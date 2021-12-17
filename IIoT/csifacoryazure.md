# CSI Factory Azure Setup

## Requirements

- Azure Account
- Resource Group

## End to End Data flow and processing

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/flowetoe.jpg "Architecture")

## Architect

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi7.jpg "Architecture")

## Setup

- Create a resource group
- Create a storage account

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi5.jpg "Architecture")

- Create a iot hub

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi1.jpg "Architecture")

- Create Custom vision Cognitive services

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi2.jpg "Architecture")

- List resource

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi3.jpg "Architecture")

- Create Stream Analytics

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csi4.jpg "Architecture")

- Next configuring PLC processor or data source(5800 series)

## Configure Rockwell Linx Gateway to collect and send data from assembly line using PLC processor or data source(5800 series) to Azure

- Installed a VM in the Industrical data center
- Then get Rockwell Linx Gateway license and download and install the software
- Connect to PLC in our case it was Allenbradley 1756-L8-5580

- Open Gateway

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway3-1.png "Architecture")

- Connect the gateway to Azure IoT hub

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway1.png "Architecture")

- Configure data source to send data to Azure IoT Hub
- Configure data source to PLC to get the data from assembly line

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway3-3.png "Architecture")

- Select the tags/sensor and what frequency to send data to Azure IoT Hub

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway3-2.png "Architecture")

- Once the communication is established, the data will be sent to Azure IoT Hub
- Communication is secured using SSL and also key based.
- We can also controll the data collection frequency and volume in the gateway

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway2.png "Architecture")

- Create Synapse Analytics workspace
- Write Spark code to process

- Detail instructions available in the link below

https://github.com/balakreshnan/AzurePercept/blob/main/Analytics/synapseint.md

## Stream Analytics

- Create a stream analytics job
- Sink is going to be ADLS gen2
- Store as parquet to further processing
- Configure input as iot hub
- Configure output as blob storage for ADLS gen2 and save as JSON format
- Store as JSON because, we need to have flexibility on adding any tags to the learning factory.

```
select 
gatewayData ,
EventProcessedUtcTime as EventProcessedUtcTime,
EventEnqueuedUtcTime as EventEnqueuedUtcTime,
IoTHub.ConnectionDeviceId as ConnectionDeviceId,
i.IoTHub.EnqueuedTime as EnqueuedTime
into outputblob from input
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway3.png "Architecture")

- Test Query and see data

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway3-4.png "Architecture")

- sample output

```
{
    {"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:44.048Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:47.2580895Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:47.0520000Z","IoTHub":{"MessageId":"cgp-iot-0.9011245877487695","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:47.0540000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:46.053Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:49.2316964Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:49.0520000Z","IoTHub":{"MessageId":"cgp-iot-0.28399297032553794","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:49.0540000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:48.061Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:51.0910865Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:51.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.15337795449939562","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:51.0540000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:48.373Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:51.4207813Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:51.3810000Z","IoTHub":{"MessageId":"cgp-iot-0.6453424444282096","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:51.3670000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:50.066Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:53.2037631Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:53.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.14634700445515736","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:53.0700000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:52.071Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:55.1736999Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:55.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.04549784022340386","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:55.0700000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:54.071Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:57.2683077Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:57.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.5343068419515651","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:57.0700000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:56.070Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:59.1477744Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:59.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.11963994698648817","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:59.0700000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output","vqts":[{"v":true,"q":192,"t":"2021-12-17T17:54:56.382Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:52:59.4743771Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:52:59.3810000Z","IoTHub":{"MessageId":"cgp-iot-0.4065047495388876","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:52:59.3820000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:54:58.071Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:53:01.1149682Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:53:01.0680000Z","IoTHub":{"MessageId":"cgp-iot-0.1373291414205593","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:53:01.0700000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:55:00.083Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:53:03.3031748Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:53:03.0840000Z","IoTHub":{"MessageId":"cgp-iot-0.5131646135820724","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:53:03.0860000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output","model_id":"test-model.New node.Slow_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:55:02.087Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:53:05.3034594Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:53:05.0930000Z","IoTHub":{"MessageId":"cgp-iot-0.1086983362424001","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:53:05.0860000Z"}}
{"gatewayData":[{"tag_id":"ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output","vqts":[{"v":false,"q":192,"t":"2021-12-17T17:55:02.397Z"}],"mimeType":"x-ra/cip/bool"}],"EventProcessedUtcTime":"2021-12-17T17:53:05.5238923Z","PartitionId":0,"EventEnqueuedUtcTime":"2021-12-17T17:53:05.4050000Z","IoTHub":{"MessageId":"cgp-iot-0.7606060925067912","CorrelationId":null,"ConnectionDeviceId":"ftedge_gateway_01","ConnectionDeviceGenerationId":"637680989196195857","EnqueuedTime":"2021-12-17T17:53:05.3980000Z"}
}
```

## Big Data

- Enables Spark based Big data processing
- Common data model for manufacturing to land the Assembly data

## Next Steps

## Parse the output JSON using Spark

- Here we are using Azure Databricks to parse the JSON output
- First create Azure databricks workspace
- Create a key vault to store the key for storage
- create a secret and store the key
- Create a cluster in databricks

## Code to load the data now

- the format is shown above.
- import the libraries

```
from pyspark.sql.types import *  
```

- Load the storage key from keyvault

```
%%pyspark
df = spark.read.load('abfss://containername@storageaccountname.dfs.core.windows.net/csitestbed/0_9b9e9f7d06e544529c1f26ad14cd8fdb_1.json', format='json')
display(df.limit(10))
```

- Explode the gatewayData

```
from pyspark.sql import functions as F

df = df.withColumn("gatewaydataexp", F.explode("gatewayData"))
```

```
display(df)
```

- eplode the values

```
display(df.select('gatewaydataexp.*'))
```

- output

```
mimeType,model_id,tag_id,vqts
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:03.137Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:05.140Z"",""v"":true}]"
x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:05.452Z"",""v"":false}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:07.140Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:09.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:11.143Z"",""v"":true}]"
x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:11.455Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:13.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:15.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:17.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:19.143Z"",""v"":true}]"
x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:19.455Z"",""v"":false}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:21.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:23.143Z"",""v"":true}]"
x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:25.143Z"",""v"":true}]"
x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,"[{""q"":192,""t"":""2021-12-17T17:46:25.455Z"",""v"":true}]"
```

- create a temp table

```
df.createOrReplaceTempView("csitestbed")
```

- run queries

```
%%sql
select IoTHub.ConnectionDeviceId, gatewaydataexp from csitestbed limit 20
```

- sample processed data

```
ConnectionDeviceId,gatewaydataexp
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:03.137Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:05.14Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",null,""ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:05.452Z"",false]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:07.14Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:09.143Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:11.143Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",null,""ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:11.455Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:13.143Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:15.143Z"",true]}]]}"
ftedge_gateway_01,"{""schema"":[{""name"":""mimeType"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""model_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""tag_id"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""vqts"",""dataType"":{""elementType"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""containsNull"":true},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""x-ra/cip/bool"",""test-model.New node.Slow_Flash.Flash_Output"",""ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output"",[{""schema"":[{""name"":""q"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""t"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}},{""name"":""v"",""dataType"":{},""nullable"":true,""metadata"":{""map"":{}}}],""values"":[""192"",""2021-12-17T17:46:17.143Z"",true]}]]}"
```

- Now expand and get only values

```
%%sql
select IoTHub.ConnectionDeviceId, csitestbed.gatewayData[0].mimeType, 
csitestbed.gatewayData[0].model_id as tagname
,csitestbed.gatewayData[0].tag_id as tagid
--,csitestbed.gatewayData[0].vqts[0]
,csitestbed.gatewayData[0].vqts[0].q as tagquality
,csitestbed.gatewayData[0].vqts[0].t as tagtime
,csitestbed.gatewayData[0].vqts[0].v as tagvalue
 from csitestbed limit 20
```

```
ConnectionDeviceId,gatewayData[0].mimeType,tagname,tagid,tagquality,tagtime,tagvalue
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:03.137Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:05.14Z,1
ftedge_gateway_01,x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,192,2021-12-17T17:46:05.452Z,
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:07.14Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:09.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:11.143Z,1
ftedge_gateway_01,x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,192,2021-12-17T17:46:11.455Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:13.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:15.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:17.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:19.143Z,1
ftedge_gateway_01,x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,192,2021-12-17T17:46:19.455Z,
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:21.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:23.143Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:25.143Z,1
ftedge_gateway_01,x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,192,2021-12-17T17:46:25.455Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:27.151Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:29.158Z,1
ftedge_gateway_01,x-ra/cip/bool,test-model.New node.Slow_Flash.Flash_Output,ra-cip-value://driver-cip/192.168.1.10/Slow_Flash.Flash_Output,192,2021-12-17T17:46:31.158Z,1
ftedge_gateway_01,x-ra/cip/bool,,ra-cip-value://driver-cip/192.168.1.10/SuperFast_Flash.Flash_Output,192,2021-12-17T17:46:39.013Z,
```

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/csitestbedsql1.jpg "Architecture")

- lets write back to store the data

```
df1 = spark.sql("select IoTHub.ConnectionDeviceId, csitestbed.gatewayData[0].mimeType, csitestbed.gatewayData[0].model_id as tagname,csitestbed.gatewayData[0].tag_id as tagid,csitestbed.gatewayData[0].vqts[0].q as tagquality,csitestbed.gatewayData[0].vqts[0].t as tagtime,csitestbed.gatewayData[0].vqts[0].v as tagvalue from csitestbed")
```

- now write

```
df1.repartition(1).write.mode("overwrite").parquet('abfss://containername@storagename.dfs.core.windows.net/csitestbed/output/')
```

## Visualization

- Create a view from above saved parquet file
- Use Power BI and connect to Synapse workspace using serverless sql
- Create a dashboard with the above view