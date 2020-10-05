# Application Tags

## Table of Content

* [Overview](#Overview)
* [Required Tags](#Required-Tags)
* [Optional Tags](#Optional-Tags)
* [Example](#Example)
* [Where to Go Next](#Where-to-Go-Next)

## Overview

Many Wavefront SDKs require you to specify _application tags_ that describe the architecture of your application as it is deployed. These tags are associated with the metrics and trace data sent from the instrumented microservices in your application. You specify a separate set of application tags for each microservice you instrument. Wavefront uses these tags to aggregate and filter data at different levels of granularity.

Application tags and their values are encapsulated in a `Tags` object in your microservice’s code. Because the tags describe the application’s architecture as it is deployed, your code typically obtains values for the tags from a YAML configuration file, either provided by the SDK or through a custom mechanism implemented by your application.

## Required Tags
**Required tags** enable you to drill down into the data for a particular service:

| Tag    | Description    |
| :------------- | :------------- |
|  `application`  | The name of your C# application, for example: `OrderingApp`. All microservices in the same application should share the same application name.  |
|  `service`  | The name of the microservice within your application, for example: `inventory`. Each microservice should have its own service name.  |

## Optional Tags
**Optional tags** enable you to use the physical topology of your application to further filter your data:

| Tag    | Description    |
| :------------- | :------------- |
|  `cluster` |  Name of a group of related hosts that serves as a cluster or region in which the application runs, for example: `us-west-2`. The default value is `none`. |
|  `shard` |  Name of a subgroup of hosts within a cluster that serve as a partition, replica, shard, or mirror, for example: `secondary`. The default value is `none`. |

Optionally, you can add custom tags specific to your application in the form of an `IDictionary` (see example below).
You can also add environment variables as custom tags (see example below).

## Example

Creating an `ApplicationTags` instance:

```csharp
string application = "OrderingApp";
string service = "inventory";
string cluster = "us-west-2";
string shard = "secondary";

IDictionary<string, string> customTags = new Dictionary<string, string>
{
  { "location", "Oregon" },
  { "env", "Staging" }
};

ApplicationTags applicationTags = new ApplicationTags.Builder(application, service)
  .Cluster(cluster)       // optional
  .Shard(shard)           // optional
  .CustomTags(customTags) // optional
  .tagsFromEnv("^MY_.*$") // optional, add all environment variables with names starting with MY_ as custom tags.
  .tagFromEnv("POD_NAME", "pod_name") // optional, add the environment variable POD_NAME as the custom tag with the tag name pod_name. 
  .Build();
```

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
