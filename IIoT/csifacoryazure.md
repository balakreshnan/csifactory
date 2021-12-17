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

- Next configuring 5480 to send data to cloud

## Configure Rockwell Linx Gateway to collect and send data from assembly line using Compactlogix 5480 to Azure

- Installed a VM in the Industrical data center
- Then get Rockwell Linx Gateway license and download and install the software
- Connect the gateway to Azure IoT hub

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway1.png "Architecture")

- Configure data source to send data to Azure IoT Hub
- Configure data source to PLC to get the data from assembly line
- Select the tags/sensor and what frequency to send data to Azure IoT Hub

![alt text](https://github.com/balakreshnan/csifactory/blob/main/IIoT/images/linegateway2.png "Architecture")

- Once the communication is established, the data will be sent to Azure IoT Hub
- Communication is secured using SSL and also key based.
- We can also controll the data collection frequency and volume in the gateway

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
- load the sql driver or later use

```
Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver")
```

- import the libraries

```
import org.apache.spark.sql.types._                         // include the Spark Types to define our schema
import org.apache.spark.sql.functions._   
```

- Load the storage key from keyvault

```
val accbbstorekey = dbutils.secrets.get(scope = "scopename", key = "keyname")
```

- Now configure the storage to access

```
spark.conf.set(
  "fs.azure.account.key.accountname.blob.core.windows.net",
  accbbstorekey)
```

- Specify the path to read JSON files

```
val jsonpath = "wasbs://containername@accountname.blob.core.windows.net/2020/11/23/0_e7d51dfce4664331bdd4d984b91bdaed_1.json"
```

- Create the schema

```
import org.apache.spark.sql.types._

val schema = new StructType()
  .add("ConnectionDeviceId", StringType)                               // data center where data was posted to Kafka cluster
  .add("gatewayData",                                          // info about the source of alarm
    ArrayType(                                              // define this as a Map(Key->value)
      new StructType()
      .add("mimeType", StringType)
      .add("tag_id", StringType)
      .add("vqts", 
           ArrayType(
                new StructType()
                .add("q", DoubleType)
                .add("t", StringType)
                .add("v", DoubleType)
           )
        )
      )
    )
```

- now read into dataframe

```
val df = spark                  // spark session 
.read                           // get DataFrameReader
.schema(schema)                 // use the defined schema above and read format as JSON
.json(jsonpath)
```

- Display Device information, tags and corresponding array of values

```
display(df.select("ConnectionDeviceId", "gatewayData.tag_id", "gatewayData.vqts"))
```

- sample processed data

```
ConnectionDeviceId,tag_id,vqts
null,null,null
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1""]","[[{""q"":192,""t"":""2020-11-23T21:34:59.048Z"",""v"":705},{""q"":192,""t"":""2020-11-23T21:35:01.047Z"",""v"":709}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1""]","[[{""q"":192,""t"":""2020-11-23T21:35:03.047Z"",""v"":713}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:05.097Z"",""v"":717},{""q"":192,""t"":""2020-11-23T21:35:07.110Z"",""v"":721},{""q"":192,""t"":""2020-11-23T21:35:09.124Z"",""v"":725},{""q"":192,""t"":""2020-11-23T21:35:11.124Z"",""v"":729},{""q"":192,""t"":""2020-11-23T21:35:13.125Z"",""v"":733}],[{""q"":192,""t"":""2020-11-23T21:35:05.097Z"",""v"":69},{""q"":192,""t"":""2020-11-23T21:35:07.110Z"",""v"":83},{""q"":192,""t"":""2020-11-23T21:35:09.124Z"",""v"":97},{""q"":192,""t"":""2020-11-23T21:35:13.125Z"",""v"":98}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:15.149Z"",""v"":737},{""q"":192,""t"":""2020-11-23T21:35:17.159Z"",""v"":741},{""q"":192,""t"":""2020-11-23T21:35:19.172Z"",""v"":745},{""q"":192,""t"":""2020-11-23T21:35:21.176Z"",""v"":749},{""q"":192,""t"":""2020-11-23T21:35:23.185Z"",""v"":753}],[{""q"":192,""t"":""2020-11-23T21:35:15.149Z"",""v"":122},{""q"":192,""t"":""2020-11-23T21:35:17.159Z"",""v"":132},{""q"":192,""t"":""2020-11-23T21:35:19.172Z"",""v"":144},{""q"":192,""t"":""2020-11-23T21:35:21.176Z"",""v"":149},{""q"":192,""t"":""2020-11-23T21:35:23.185Z"",""v"":158}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:25.185Z"",""v"":757},{""q"":192,""t"":""2020-11-23T21:35:27.187Z"",""v"":761},{""q"":192,""t"":""2020-11-23T21:35:29.186Z"",""v"":765},{""q"":192,""t"":""2020-11-23T21:35:31.200Z"",""v"":769},{""q"":192,""t"":""2020-11-23T21:35:33.200Z"",""v"":773}],[{""q"":192,""t"":""2020-11-23T21:35:27.187Z"",""v"":159},{""q"":192,""t"":""2020-11-23T21:35:31.200Z"",""v"":173},{""q"":192,""t"":""2020-11-23T21:35:33.200Z"",""v"":172}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:35.209Z"",""v"":777},{""q"":192,""t"":""2020-11-23T21:35:37.215Z"",""v"":781},{""q"":192,""t"":""2020-11-23T21:35:39.215Z"",""v"":785},{""q"":192,""t"":""2020-11-23T21:35:41.216Z"",""v"":789},{""q"":192,""t"":""2020-11-23T21:35:43.231Z"",""v"":793}],[{""q"":192,""t"":""2020-11-23T21:35:35.209Z"",""v"":182},{""q"":192,""t"":""2020-11-23T21:35:37.215Z"",""v"":188},{""q"":192,""t"":""2020-11-23T21:35:39.215Z"",""v"":187},{""q"":192,""t"":""2020-11-23T21:35:41.216Z"",""v"":188},{""q"":192,""t"":""2020-11-23T21:35:43.231Z"",""v"":204}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:45.247Z"",""v"":797}],[{""q"":192,""t"":""2020-11-23T21:35:45.247Z"",""v"":219}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T21:35:47.245Z"",""v"":801},{""q"":192,""t"":""2020-11-23T21:35:49.246Z"",""v"":805},{""q"":192,""t"":""2020-11-23T21:35:51.262Z"",""v"":809},{""q"":192,""t"":""2020-11-23T21:35:53.277Z"",""v"":813},{""q"":192,""t"":""2020-11-23T21:35:55.291Z"",""v"":817},{""q"":192,""t"":""2020-11-23T21:35:57.291Z"",""v"":821},{""q"":192,""t"":""2020-11-23T21:35:59.291Z"",""v"":825}],[{""q"":192,""t"":""2020-11-23T21:35:47.245Z"",""v"":218},{""q"":192,""t"":""2020-11-23T21:35:49.246Z"",""v"":219},{""q"":192,""t"":""2020-11-23T21:35:51.262Z"",""v"":235},{""q"":192,""t"":""2020-11-23T21:35:53.277Z"",""v"":250},{""q"":192,""t"":""2020-11-23T21:35:55.291Z"",""v"":264},{""q"":192,""t"":""2020-11-23T21:35:59.291Z"",""v"":263}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:50:15.711Z"",""v"":4737},{""q"":192,""t"":""2020-11-23T22:50:17.712Z"",""v"":4741},{""q"":192,""t"":""2020-11-23T22:50:19.711Z"",""v"":4745},{""q"":192,""t"":""2020-11-23T22:50:21.711Z"",""v"":4749},{""q"":192,""t"":""2020-11-23T22:50:23.710Z"",""v"":4753}],[{""q"":192,""t"":""2020-11-23T22:50:21.711Z"",""v"":399}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:50:25.710Z"",""v"":4757},{""q"":192,""t"":""2020-11-23T22:50:27.710Z"",""v"":4761},{""q"":192,""t"":""2020-11-23T22:50:29.709Z"",""v"":4765},{""q"":192,""t"":""2020-11-23T22:50:31.709Z"",""v"":4769},{""q"":192,""t"":""2020-11-23T22:50:33.709Z"",""v"":4773}],[{""q"":192,""t"":""2020-11-23T22:50:29.709Z"",""v"":398},{""q"":192,""t"":""2020-11-23T22:50:31.709Z"",""v"":399},{""q"":192,""t"":""2020-11-23T22:50:33.709Z"",""v"":398}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:50:35.708Z"",""v"":4777},{""q"":192,""t"":""2020-11-23T22:50:37.708Z"",""v"":4781},{""q"":192,""t"":""2020-11-23T22:50:39.708Z"",""v"":4785},{""q"":192,""t"":""2020-11-23T22:50:41.707Z"",""v"":4789},{""q"":192,""t"":""2020-11-23T22:50:43.707Z"",""v"":4793}],[{""q"":192,""t"":""2020-11-23T22:50:39.708Z"",""v"":397}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:50:45.707Z"",""v"":4797},{""q"":192,""t"":""2020-11-23T22:50:47.707Z"",""v"":4801},{""q"":192,""t"":""2020-11-23T22:50:49.706Z"",""v"":4805},{""q"":192,""t"":""2020-11-23T22:50:51.706Z"",""v"":4809},{""q"":192,""t"":""2020-11-23T22:50:53.706Z"",""v"":4813}],[{""q"":192,""t"":""2020-11-23T22:50:49.706Z"",""v"":396}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:50:55.705Z"",""v"":4817},{""q"":192,""t"":""2020-11-23T22:50:57.705Z"",""v"":4821},{""q"":192,""t"":""2020-11-23T22:50:59.705Z"",""v"":4825},{""q"":192,""t"":""2020-11-23T22:51:01.704Z"",""v"":4829},{""q"":192,""t"":""2020-11-23T22:51:03.704Z"",""v"":4833}],[{""q"":192,""t"":""2020-11-23T22:50:57.705Z"",""v"":395}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:51:05.704Z"",""v"":4837},{""q"":192,""t"":""2020-11-23T22:51:07.704Z"",""v"":4841},{""q"":192,""t"":""2020-11-23T22:51:09.703Z"",""v"":4845},{""q"":192,""t"":""2020-11-23T22:51:11.703Z"",""v"":4849},{""q"":192,""t"":""2020-11-23T22:51:13.703Z"",""v"":4853}],[{""q"":192,""t"":""2020-11-23T22:51:07.704Z"",""v"":394}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:51:15.702Z"",""v"":4857},{""q"":192,""t"":""2020-11-23T22:51:17.702Z"",""v"":4861},{""q"":192,""t"":""2020-11-23T22:51:19.702Z"",""v"":4865},{""q"":192,""t"":""2020-11-23T22:51:21.702Z"",""v"":4869},{""q"":192,""t"":""2020-11-23T22:51:23.701Z"",""v"":4873}],[{""q"":192,""t"":""2020-11-23T22:51:17.702Z"",""v"":393}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:51:25.701Z"",""v"":4877},{""q"":192,""t"":""2020-11-23T22:51:27.700Z"",""v"":4881},{""q"":192,""t"":""2020-11-23T22:51:29.700Z"",""v"":4885},{""q"":192,""t"":""2020-11-23T22:51:31.700Z"",""v"":4889},{""q"":192,""t"":""2020-11-23T22:51:33.700Z"",""v"":4893}],[{""q"":192,""t"":""2020-11-23T22:51:27.700Z"",""v"":392}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:51:35.700Z"",""v"":4897},{""q"":192,""t"":""2020-11-23T22:51:37.699Z"",""v"":4901},{""q"":192,""t"":""2020-11-23T22:51:39.699Z"",""v"":4905},{""q"":192,""t"":""2020-11-23T22:51:41.698Z"",""v"":4909},{""q"":192,""t"":""2020-11-23T22:51:43.698Z"",""v"":4913}],[{""q"":192,""t"":""2020-11-23T22:51:37.699Z"",""v"":391}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1"",""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.Counting1.ACC""]","[[{""q"":192,""t"":""2020-11-23T22:51:45.698Z"",""v"":4917},{""q"":192,""t"":""2020-11-23T22:51:47.697Z"",""v"":4921},{""q"":192,""t"":""2020-11-23T22:51:49.698Z"",""v"":4925},{""q"":192,""t"":""2020-11-23T22:51:51.697Z"",""v"":4929},{""q"":192,""t"":""2020-11-23T22:51:53.697Z"",""v"":4933}],[{""q"":192,""t"":""2020-11-23T22:51:45.698Z"",""v"":390},{""q"":192,""t"":""2020-11-23T22:51:49.698Z"",""v"":391},{""q"":192,""t"":""2020-11-23T22:51:51.697Z"",""v"":390},{""q"":192,""t"":""2020-11-23T22:51:53.697Z"",""v"":389}]]"
joezlogix,"[""ra-cip-value://driver-cip/10.97.1.68/Program:MainProgram.counter1""]","[[{""q"":192,""t"":""2020-11-23T22:51:55.696Z"",""v"":4937},{""q"":192,""t"":""2020-11-23T22:51:57.696Z"",""v"":4941},{""q"":192,""t"":""2020-11-23T22:51:59.696Z"",""v"":4945},{""q"":192,""t"":""2020-11-23T22:52:01.695Z"",""v"":4949},{""q"":192,""t"":""2020-11-23T22:52:03.696Z"",""v"":4953}]]"
```

![alt text](https://github.com/balakreshnan/IIoT-AI/blob/master/IIoT/images/adb1.jpg "Architecture")

## Visualization