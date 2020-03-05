# Set Up an IWavefrontSender Instance

## Table of Content

* [Overview](#Overview)
* [Option 1: Sending Data via the Wavefront Proxy](#Option-1-Sending-Data-via-the-Wavefront-Proxy)
* [Option 2: Sending Data via Direct ingestion](#Option-2-Sending-Data-via-Direct-ingestion)
* [Share an IWavefrontSender Instance](#Share-an-IWavefrontSender-Instance)
* [Where to Go Next](#where-to-go-next)

## Overview

You can send metrics, histograms, or trace data from your application to the Wavefront service using a Wavefront proxy or direct ingestions.

* Use a [**Wavefront proxy**](https://docs.wavefront.com/proxies.html), which then forwards the data to the Wavefront service. This is the recommended choice for a large-scale deployment that needs resilience to internet outages, control over data queuing and filtering, and more.
* Use [**direct ingestion**](https://docs.wavefront.com/direct_ingestion.html) to send the data directly to the Wavefront service. This is the simplest way to get up and running quickly. 

The `IWavefrontSender` interface has two implementations:

* Option 1: [Create a `WavefrontProxyClient`](#Option-1-Sending-Data-via-the-Wavefront-Proxy) to send data to a Wavefront proxy.
* Option 2: [Create a `WavefrontDirectIngestionClient`](#Option-2-Sending-Data-via-Direct-ingestion) to send data directly to a Wavefront service.

## Option 1: Sending Data via the Wavefront Proxy

>**Prerequisite**
>Before your application can use a `WavefrontProxyClient`, you must [set up and start a Wavefront proxy](https://docs.wavefront.com/proxies_installing.html).

When sending data via the Wavefront proxy, you need to create a `WavefrontProxyClient`. Include the following information.

* The name of the host that will run the Wavefront proxy.
* One or more proxy listening ports to send data to. The ports you specify depend on the kinds of data you want to send (metrics, histograms, and/or trace data). You must specify at least one listener port. 
* Optional settings for tuning communication with the proxy.

> **Note**: See [Advanced Proxy Configuration and Installation](https://docs.wavefront.com/proxies_configuring.html) for details.

```csharp
// Create the builder with the proxy hostname or address
WavefrontProxyClient.Builder wfProxyClientBuilder = new WavefrontProxyClient.Builder(proxyHostName);

// Set the proxy port to send metrics to. Default: 2878
wfProxyClientBuilder.MetricsPort(2878);

// Set a proxy port to send histograms to.  Recommended: 2878
wfProxyClientBuilder.DistributionPort(2878);

// Set a proxy port to send trace data to. Recommended: 30000
wfProxyClientBuilder.TracingPort(30_000);

// Optional: Set a nondefault interval (in seconds) for flushing data from the sender to the proxy. Default: 5 seconds
wfProxyClientBuilder.FlushIntervalSeconds(2);

// Create the WavefrontProxyClient
IWavefrontSender wavefrontSender = wfProxyClientBuilder.Build();
 ```
> **Note:** When you set up a Wavefront proxy on the specified proxy host, you specify the port it will listen to for each type of data to be sent. The `WavefrontProxyClient` must send data to the same ports that the Wavefront proxy listens to. Consequently, the port-related builder methods must specify the same port numbers as the corresponding proxy configuration properties. See the following table: 

| `WavefrontProxyClient` builder method | Corresponding property in `wavefront.conf` |
| ----- | -------- |
| `MetricsPort()` | `pushListenerPorts=` |
| `DistributionPort()` | `histogramDistListenerPorts=` |
| `TracingPort()` | `traceListenerPorts=` |

## Option 2: Sending Data via Direct Ingestion

When sending data via direct ingestion, you need to create a `WavefrontDirectIngestionClient`, and build it with the Wavefront URL and API token to send data directly to Wavefront.

>**Prerequisite**
> * Verify that you have the Direct Data Ingestion permission. For details, see [Examine Groups and Permissions](https://docs.wavefront.com/users_account_managing.html#examine-groups-and-permissions).
> * The URL of your Wavefront instance. This is the URL you connect to when you log in to Wavefront, typically something like `https://<domain>.wavefront.com`.
> * [Obtain the API token](http://docs.wavefront.com/wavefront_api.html#generating-an-api-token).

### Initialize the WavefrontDirectIngestionClient
You initialize a `WavefrontDirectIngestionClient` by building it with the access information you obtained in the Prerequisite section.

You can optionally call builder methods to tune the following ingestion properties:

* Max queue size - Internal buffer capacity of the Wavefront sender. Any data in excess of this size is dropped.
* Flush interval - Interval for flushing data from the Wavefront sender directly to Wavefront.
* Batch size - Amount of data to send to Wavefront in each flush interval.

Together, the batch size and flush interval control the maximum theoretical throughput of the Wavefront sender. Override the defaults _only_ to set higher values.

```csharp
// Create a builder with the Wavefront URL and a Wavefront API token
// that was created with direct ingestion permission.
WavefrontDirectIngestionClient.Builder wfDirectIngestionClientBuilder =
  new WavefrontDirectIngestionClient.Builder(wavefrontURL, token);

// Optional: Override the max queue size (in data points). Default: 50,000
wfDirectIngestionClientBuilder.MaxQueueSize(100_000);

// Optional: Override the batch size (in data points). Default: 10,000
wfDirectIngestionClientBuilder.BatchSize(20_000);

// Optional: Override the flush interval (in seconds). Default: 1 second
wfDirectIngestionClientBuilder.FlushIntervalSeconds(2);

// Create a WavefrontDirectIngestionClient.
IWavefrontSender wavefrontSender = wfDirectIngestionClientBuilder.Build();
 ```
 
# Share an IWavefrontSender Instance

If you are using multiple Wavefront C# SDKs within the same process, you can instantiate the `IWavefrontSender` just once and share it among the SDKs. 

For example, the following snippet shows how to use the same Wavefront sender when setting up the [wavefront-appmetrics-sdk-csharp](https://github.com/wavefrontHQ/wavefront-appmetrics-sdk-csharp) and  [wavefront-opentracing-sdk-csharp](https://github.com/wavefrontHQ/wavefront-opentracing-sdk-csharp) SDKs.

```csharp
// Create a Wavefront sender, assuming you have a configuration file
IWavefrontSender wavefrontSender = BuildProxyOrDirectSender(config);

// Create a WavefrontSpanReporter for the OpenTracing SDK
IReporter spanReporter = new WavefrontSpanReporter.Builder()
  .WithSource("wavefront-tracing-example")
  .Build(wavefrontSender);

// Create a Wavefront reporter for the App Metrics SDK
IMetricsRoot metrics = new MetricsBuilder()
  .Report.ToWavefront(wavefrontSender)
  .Build();
...
```

> **Note:** If you use the SDKs in different processes, you must instantiate one `IWavefrontSender` instance per process.

## Where to Go Next

To continue, select one of the Wavefront C# SDK links in the table below.

<table id="SDKlevels" style="width: 100%">
<tr>
  <th width="10%">SDK Type</th>
  <th width="45%">SDK Description</th>
  <th width="45%">.Net/C# SDKs</th>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-trace-data">OpenTracing SDK</a></td>
  <td align="justify">Implements the OpenTracing specification. Lets you define, collect, and report custom trace data from any part of your application code. <br>Automatically derives RED metrics from the reported spans. </td> 
  <td>
    <a href ="https://github.com/wavefrontHQ/wavefront-opentracing-sdk-csharp"> C# OpenTracing SDK</a>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-metrics-and-histograms">Metrics SDK</a></td>
  <td align="justify">Implements a standard metrics library. Lets you define, collect, and report custom business metrics and histograms from any part of your application code.   </td> 
  <td>
    <a href ="https://github.com/wavefrontHQ/wavefront-appmetrics-sdk-csharp">App Metrics SDK</a>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-that-instrument-frameworks">Framework SDK</a></td>
  <td align="justify">Reports predefined traces, metrics, and histograms from the APIs of a supported app framework. Lets you get started quickly with minimal code changes.</td>
  <td>
    <a href="https://github.com/wavefrontHQ/wavefront-aspnetcore-sdk-csharp">ASP.Net core</a> </li>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-sending-raw-data-to-wavefront">Sender SDK</a></td>
  <td align="justify">Lets you send raw values to Wavefront for storage as metrics, histograms, or traces, e.g., to import CSV data into Wavefront. 
  </td>
  <td>
    <a href ="https://github.com/wavefrontHQ/wavefront-sdk-csharp">C# Sender SDK</a>
  </td>
</tr>

</tbody>
</table> 
