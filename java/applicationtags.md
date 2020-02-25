# Application Tags

## Table of Content

* [Overview](#Overview)
* [Required Tags](#Required-Tags)
* [Optional Tags](#Optional-Tags)
* [Example](#Example)
* [Where to Go Next](#Where-to-Go-Next)

## Overview
Many Wavefront SDKs require you to specify _application tags_ that describe the architecture of your application as it is deployed. These tags are associated with the metrics and trace data sent from the instrumented microservices in your application. You specify a separate set of application tags for each microservice you instrument. Wavefront uses these tags to aggregate and filter data at different levels of granularity.

Application tags and their values are encapsulated in an `ApplicationTags` object in your microservice’s code. Because the tags describe the application’s architecture as it is deployed, your code typically obtains values for the tags from a YAML configuration file, either provided by the SDK or through a custom mechanism implemented by your application.


## Required Tags
**Required tags** enable you to drill down into the data for a particular service:

| Tag    | Description    |
| :------------- | :------------- |
| `application`     | Name that identifies your Java application, for example: `OrderingApp`. All microservices in an application should share the same application name.      |
| `service`      | Name that identifies the microservice within your application, for example: `inventory`. Each microservice should have its own service name.       |


## Optional Tags
**Optional tags** enable you to use the physical topology of your application to further filter your data:

|  Tag      | Description     |
| :------------- | :------------- |
| `cluster`      | Name of a group of related hosts that serves as a cluster or region in which the application runs, for example: `us-west-2`. The default value is `none`.       |
| `shard`      | Name of a subgroup of hosts within a cluster that serve as a partition, replica, shard, or mirror, for example: `secondary`. The default value is `none`.       |

Optionally, you can add custom tags specific to your application in the form of a `Map` (see example below).

## Example

Creating an `ApplicationTags` instance:
```java
String application = "OrderingApp";
String service = "inventory";
String cluster = "us-west-2";
String shard = "secondary";

Map<String, String> customTags = new HashMap<String, String>() {{
  put("location", "Oregon");
  put("env", "Staging");
}};

ApplicationTags applicationTags = new ApplicationTags.Builder(application, service).
    cluster(cluster).       // optional
    shard(shard).           // optional
    customTags(customTags). // optional
    build();
```

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
 
