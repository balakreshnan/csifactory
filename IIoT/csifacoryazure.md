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

## Big Data

- Enables Spark based Big data processing
- Common data model for manufacturing to land the Assembly data

## Visualization