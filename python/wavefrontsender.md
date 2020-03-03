# Set Up a Wavefront Sender

## Table of Content

* [Overview](#Overview)
* [Option 1: Sending Data via the Wavefront Proxy](#Option-1-Sending-Data-via-the-Wavefront-Proxy)
* [Option 2: Sending Data via Direct ingestion](#Option-2-Sending-Data-via-Direct-ingestion)
* [Share a WavefrontSender](#Share-a-WavefrontSender)
* [Where to Go Next](#where-to-go-next)

## Overview
You can send metrics, histograms, or trace data from your application to the Wavefront service using a Wavefront proxy or direct ingestions.

* Use a [**Wavefront proxy**](https://docs.wavefront.com/proxies.html), which then forwards the data to the Wavefront service. This is the recommended choice for a large-scale deployment that needs resilience to internet outages, control over data queuing and filtering, and more.
* Use [**direct ingestion**](https://docs.wavefront.com/direct_ingestion.html) to send the data directly to the Wavefront service. This is the simplest way to get up and running quickly. 

The `WavefrontSender` interface has two implementations:

* Option 1: [Create a `WavefrontProxyClient`](#Option-1-Sending-Data-via-the-Wavefront-Proxy) to send data to a Wavefront proxy.
* Option 2: [Create a `WavefrontDirectIngestionClient`](#Option-2-Sending-Data-via-Direct-ingestion) to send data directly to a Wavefront service.

## Option 1: Sending Data via the Wavefront Proxy

>**Prerequisite**
>Before your application can use a `WavefrontProxyClient`, you must [set up and start a Wavefront proxy](https://docs.wavefront.com/proxies_installing.html).

When sending data via the Wavefront proxy, you need to create a `WavefrontProxyClient`. Include the following information.

* The name of the host that will run the Wavefront proxy.
* One or more proxy listening ports to send data to. The ports you specify depend on the kinds of data you want to send (metrics, histograms, and/or trace data). You must specify at least one listener port. 
* Optional settings for tuning communication with the proxy.

> **Note**: See [Advanced Proxy Configuration and Installation](https://docs.wavefront.com/proxies_configuring.html) for details:

```python
from wavefront_sdk import WavefrontProxyClient

# Create a sender with:
   # the proxy hostname or address
   # the recommended listener port (30000) for sending trace data 
   # the recommended listener port (2878) for sending histograms
   # the default listener port (2878) for sending metrics  
   # a nondefault interval (2 seconds) for flushing data from the sender to the proxy. Default: 5 seconds
wavefront_sender = WavefrontProxyClient(
   host="<PROXY_HOST>",
   tracing_port=30000,
   distribution_port=2878,
   metrics_port=2878,
   internal_flush=2
)
```
 
> **Note:** When you set up a Wavefront proxy on the specified proxy host, you specify the port it will listen to for each type of data to be sent. The `WavefrontProxyClient` must send data to the same ports that the Wavefront proxy listens to. Consequently, the port-related builder methods must specify the same port numbers as the corresponding proxy configuration properties. See the following table:  

| `WavefrontProxyClient()` parameter | Corresponding property in `wavefront.conf` |
| ----- | -------- |
| `metrics_port` | `pushListenerPorts=` |
| `distribution_port` | `histogramDistListenerPorts=` |
| `tracing_port` | `traceListenerPorts=` |

## Option 2: Sending Data via Direct Ingestion

When sending data via direct ingestion, you need to create a `WavefrontDirectIngestionClient`, and build it with the Wavefront URL and API token to send data directly to Wavefront.

>**Prerequisite**
> * Verify that you have the Direct Data Ingestion permission. For details, see [Examine Groups and Permissions](https://docs.wavefront.com/users_account_managing.html#examine-groups-and-permissions).
> * The URL of your Wavefront instance. This is the URL you connect to when you log in to Wavefront, typically something like `https://<domain>.wavefront.com`.
> * [Obtain the API token](http://docs.wavefront.com/wavefront_api.html#generating-an-api-token).

### Initialize the WavefrontDirectIngestionClient
You initialize a `WavefrontDirectClient` by building it with the access information you obtained in the Prerequisite section.

```python
from wavefront_sdk import WavefrontDirectClient

# Create a sender with:
   # the Wavefront URL 
   # a Wavefront API token that was created with direct ingestion permission
wavefront_sender = WavefrontDirectClient(
    server="<SERVER_ADDR>",
    token="<TOKEN>"
)
```
 
# Share a Wavefront Sender

If you are using multiple Wavefront Python SDKs within the same process, you can create a single Wavefront sender, and share it among the SDKs. 

**Note:** If you use SDKs in different processes, you must create one `Sender` instance per process. 
<!--- 
For example, the following snippet shows how to use the same `WavefrontSender` when setting up the 
[wavefront-opentracing-sdk-python](https://github.com/wavefrontHQ/wavefront-opentracing-sdk-python) and 
XXX SDKs.
--->

## Where to Go Next

To continue, select one of the Wavefront Python SDK links in the table below.

<table id="SDKlevels" style="width: 100%">
<tr>
  <th width="10%">SDK Type</th>
  <th width="60%">SDK Description</th>
  <th width="30%">Python SDKs</th>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-trace-data">OpenTracing SDK</a></td>
  <td align="justify">Implements the OpenTracing specification. Lets you define, collect, and report custom trace data from any part of your application code. <br>Automatically derives RED metrics from the reported spans. </td> 
  <td>
    <a href ="https://github.com/wavefrontHQ/wavefront-opentracing-sdk-python">Python OpenTracing SDK</a>  
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-collecting-metrics-and-histograms">Metrics SDK</a></td>
  <td align="justify">Implements a standard metrics library. Lets you define, collect, and report custom business metrics and histograms from any part of your application code.   </td> 
  <td>
    <a href ="https://github.com/wavefrontHQ/wavefront-pyformance">Pyformance SDK</a>
  </td>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-for-sending-raw-data-to-wavefront">Sender SDK</a></td>
  <td align="justify">Lets you send raw values to Wavefront for storage as metrics, histograms, or traces, e.g., to import CSV data into Wavefront. 
  </td>
  <td>
      <a href ="https://github.com/wavefrontHQ/wavefront-sdk-python">Python Sender SDK</a>
  </td>
</tr>

</tbody>
</table> 
