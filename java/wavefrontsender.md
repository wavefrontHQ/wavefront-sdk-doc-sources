# WavefrontSender

## Table of Content

* [Overview](#Overview)
* [Option 1: Sending Data via the Wavefront Proxy](#Option-1-Sending-Data-via-the-Wavefront-Proxy)
* [Option 2: Sending Data via Direct ingestion](#Option-2-Sending-Data-via-Direct-ingestion)
* [Send Data to Multiple Wavefront Services](#Send-Data-to-Multiple-Wavefront-Services)
* [Share a WavefrontSender](#Share-a-WavefrontSender)
* [Where to Go Next](#where-to-go-next)

## Overview

You can send metrics, histograms, or trace data from your application to the Wavefront service using a Wavefront proxy or direct ingestions. 

* Use a [**Wavefront proxy**](https://docs.wavefront.com/proxies.html), which then forwards the data to the Wavefront service. This is the recommended choice for a large-scale deployment that needs resilience to internet outages, control over data queuing and filtering, and more.
* Use [**direct ingestion**](https://docs.wavefront.com/direct_ingestion.html) to send the data directly to the Wavefront service. This is the simplest way to get up and running quickly.

Let's create a `WavefrontClient` to send data to Wavefront either via Wavefront proxy or directly over HTTP.

> **Deprecated implementations**: *`WavefrontDirectIngestionClient` and `WavefrontProxyClient` are deprecated from proxy version 7.0 onwards. We recommend all new applications to use `WavefrontClient`.*

Use `WavefrontClientFactory` to create a `WavefrontClient` instance, which can send data directly to a Wavefront service or send data using a Wavefront Proxy.
The `WavefrontClientFactory` supports multiple client bindings. If more than one client configuration is specified, you can create a `WavefrontMultiClient` to send multiple Wavefront services.

## Option 1: Sending Data via the Wavefront Proxy

### Prerequisites

* Before your application can use a `WavefrontProxyClient`, you must [set up and start a Wavefront proxy](https://docs.wavefront.com/proxies_installing.html).

### Initialize the WavefrontClient

You initialize a `WavefrontClient` as shown in the examples below.

Optionally, you can call factory methods to tune the following ingestion properties:

* Max queue size - Internal buffer capacity of the `WavefrontSender`. Data that exceeds this size is dropped.
* Flush interval - Interval for flushing data from the `WavefrontSender` directly to Wavefront.
* Batch size - Amount of data to send to Wavefront in each flush interval.

Together, the batch size and flush interval control the maximum theoretical throughput of the `WavefrontSender`. Override the defaults _only_ to set higher values.

**Example**: Use a factory class to create a WavefrontClient and send data to Wavefront via Wavefront Proxy. 

```java
// Add a client with the following URL format: "proxy://<your.proxy.load.balancer.com>:<port>" or "http://<host>:<port>/"
// to send data to proxies
WavefrontClientFactory wavefrontClientFactory = new WavefrontClientFactory();
wavefrontClientFactory.addClient(wavefrontURL);

WavefrontSender wavefrontSender = wavefrontClientFactory.getClient();
```
**Example**: Use a builder to create a WavefrontClient and send data to Wavefront via Wavefront Proxy. 

```java
// Using the WavefrontClient.Builder directly with a url in the form of "http://<your.proxy.load.blanacer>:<port>"
// to send data to proxies.
WavefrontClient.Builder wfClientBuilder = new WavefrontClient.Builder(proxyURL);

//The maximum message size in bytes that is pushed with on each flush interval 
wfClientBuilder.messageSizeBytes(120);

// This is the size of internal buffer beyond which data is dropped
// Optional: Set this to override the default max queue size of 50,000
wfClientBuilder.maxQueueSize(100_000);

// This is the max batch of data sent per flush interval
// Optional: Set this to override the default batch size of 10,000
wfClientBuilder.batchSize(20_000);

// Together with batch size controls the max theoretical throughput of the sender
// Optional: Set this to override the default flush interval value of 1 second
wfClientBuilder.flushIntervalSeconds(2);

WavefrontSender wavefrontSender = wfClientBuilder.build();
``` 

## Option 2: Sending Data via Direct Ingestion

### Prerequisites

* Verify that you have the Direct Data Ingestion permission. For details, see [Examine Groups, Roles, and Permissions](https://docs.wavefront.com/users_account_managing.html#examine-groups-roles-and-permissions).
* The HTTP URL of your Wavefront instance. This is the URL you connect to when you log in to Wavefront, typically something like `http://<domain>.wavefront.com`.
* [Obtain the API token](http://docs.wavefront.com/wavefront_api.html#generating-an-api-token).

### Initialize the WavefrontClient

You initialize a `WavefrontClient` by building it with the information you obtained in the Prerequisites section.

Optionally, you can call factory methods to tune the following ingestion properties:

* Max queue size - Internal buffer capacity of the `WavefrontSender`. Data that exceeds this size is dropped.
* Flush interval - Interval for flushing data from the `WavefrontSender` directly to Wavefront.
* Batch size - Amount of data to send to Wavefront in each flush interval.

Together, the batch size and flush interval control the maximum theoretical throughput of the `WavefrontSender`. Override the defaults _only_ to set higher values.

**Example**: Use a factory class to create a WavefrontClient and send  data to Wavefront via direct ingestion.

```java
// Create a factory and add a client with the following URL format: "http://TOKEN@DOMAIN.wavefront.com"
// and a Wavefront API token with direct ingestion permission
WavefrontClientFactory wavefrontClientFactory = new WavefrontClientFactory();

// Add a new client that sends data directly to Wavefront services 
wavefrontClientFactory.addClient(wavefrontURL,
  20_000,           // This is the max batch of data sent per flush interval
  100_000,          // This is the size of internal buffer beyond which data is dropped
  2,                // Together with the batch size controls the max theoretical throughput of the sender
  Integer.MAX_VALUE // The maximum message size in bytes we will push with on each flush interval 
);

WavefrontSender wavefrontSender = wavefrontClientFactory.getClient();
```

**Example**: Use a builder to create a WavefrontClient and send  data to Wavefront via direct ingestion.

```java
// Using the WavefrontClient.Builder directly with a url in the form of "https://DOMAIN.wavefront.com"
// and a Wavefront API token with direct ingestion permission
WavefrontClient.Builder wfClientBuilder = new WavefrontClient.Builder(wavefrontURL, token);

//The maximum message size in bytes that is pushed with on each flush interval 
wfClientBuilder.messageSizeBytes(120);

// This is the size of internal buffer beyond which data is dropped
// Optional: Set this to override the default max queue size of 50,000
wfClientBuilder.maxQueueSize(100_000);

// This is the max batch of data sent per flush interval
// Optional: Set this to override the default batch size of 10,000
wfClientBuilder.batchSize(20_000);

// Together with batch size controls the max theoretical throughput of the sender
// Optional: Set this to override the default flush interval value of 1 second
wfClientBuilder.flushIntervalSeconds(2);

WavefrontSender wavefrontSender = wfClientBuilder.build();
``` 

## Send Data to Multiple Wavefront Services

Use `WavefrontMultiClient` to send data to multiple Wavefront services so you handle the data traffic.
The `addClient()` supports null for batch size, queue size, and push interval. The defaults values are used if nothing is specified.

**Example**: Creating a `WavefrontMultiClient` to send data to multiple Wavefront services.
```java
// Add multiple URLs to the Factory to obtain a multi-sender
WavefrontClientFactory wavefrontClientFactory = new WavefrontClientFactory();
wavefrontClientFactory.addClient("https://someToken@DOMAIN.wavefront.com");
wavefrontClientFactory.addClient("proxy://our.proxy.lb.com:2878");
// Send traces and spans to the tracing port. If you are directly using the sender SDK to send spans without using any other SDK, use the same port as the customTracingListenerPorts configured in the wavefront proxy. Assume you have installed and started the proxy on <proxyHostname>.
wavefrontClientFactory.addClient("http://<proxy_hostname>:30000/");

WavefrontSender wavefrontSender = wavefrontClientFactory.getClient();
```

## Share a WavefrontSender

If you are using multiple Wavefront Java SDKs within the same JVM process, you can instantiate the `WavefrontSender` just once and share it among the SDKs. 
 
For example, the following snippet shows how to use the same `WavefrontSender` when setting up the [wavefront-opentracing-sdk-java](https://github.com/wavefrontHQ/wavefront-opentracing-sdk-java) and  [wavefront-dropwizard-metrics-sdk-java](https://github.com/wavefrontHQ/wavefront-dropwizard-metrics-sdk-java) SDKs.

```java

// Create a WavefrontSender
WavefrontSender wavefrontSender = buildProxyOrDirectSender(); // pseudocode

// Create a WavefrontSpanReporter for the OpenTracing SDK
Reporter spanReporter = new WavefrontSpanReporter.Builder().
   withSource("wavefront-tracing-example").
   build(wavefrontSender);

// Create a Wavefront reporter for the Dropwizard Metrics SDK
MetricRegistry registry = new MetricRegistry();
DropwizardMetricsReporter.Builder builder =   
DropwizardMetricsReporter metricsReporter =
   DropwizardMetricsReporter.forRegistry(registry).
   build(wavefrontSender);
...
```

>**Note:** If you use SDKs in different JVM processes, you must instantiate one `WavefrontSender` instance per JVM process.

## Where to Go Next

To continue, select one of the Wavefront Java SDK links in the table below.

<table id="SDKlevels" style="width: 100%">
<tr>
  <th width="10%">SDK Type</th>
  <th width="60%">SDK Description</th>
  <th width="30%">Java SDKs</th>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-trace-data">OpenTracing SDK</a></td>
  <td align="justify">Implements the OpenTracing specification. Lets you define, collect, and report custom trace data from any part of your application code. <br>Automatically derives RED metrics from the reported spans. </td> 
  <td>
    <ul>
      <li>
        <a href ="https://github.com/wavefrontHQ/wavefront-opentracing-sdk-java">OpenTracing SDK</a>
      </li>
      <li>
        <a href ="https://github.com/wavefrontHQ/wavefront-opentracing-bundle-java">Tracing Agent</a>
      </li>
    </ul>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-metrics-and-histograms">Metrics SDK</a></td>
  <td align="justify">Implements a standard metrics library. Lets you define, collect, and report custom business metrics and histograms from any part of your application code.   </td> 
  <td>
    <ul>
      <li>
        <a href ="https://github.com/wavefrontHQ/wavefront-dropwizard-metrics-sdk-java">Dropwizard</a>
      </li>
      <li>
        <a href ="https://github.com/wavefrontHQ/wavefront-runtime-sdk-jvm">JVM</a>
      </li>
    <ul>

  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-that-instrument-frameworks">Framework SDK</a></td>
  <td align="justify">Reports predefined traces, metrics, and histograms from the APIs of a supported app framework. Lets you get started quickly with minimal code changes.</td>
  <td>
    <ul>
      <li>
        <a href="https://github.com/wavefrontHQ/wavefront-dropwizard-sdk-java">Dropwizard</a>
      </li>
      <li>
        <a href="https://github.com/wavefrontHQ/wavefront-gRPC-sdk-java">gRPC</a>
      </li>
      <li>
        <a href="https://github.com/wavefrontHQ/wavefront-jaxrs-sdk-java">JAX-RS</a>
      </li>
      <li>
      <a href="https://github.com/wavefrontHQ/wavefront-jersey-sdk-java">Jersey</a>
      </li>
    <ul>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-sending-raw-data-to-wavefront">Sender SDK</a></td>
  <td align="justify">Lets you send raw values to Wavefront for storage as metrics, histograms, or traces, e.g., to import CSV data into Wavefront. 
  </td>
  <td>
    <ul>
      <li>
        <a href ="https://github.com/wavefrontHQ/wavefront-sdk-java"> Java Sender SDK</a>
      </li>
    <ul>
  </td>
</tr>

</tbody>
</table> 
 
