# Internal Diagnostic Metrics

## Table of Content

* [Overview](#Overview)
* [Metrics Collected When Using the Wavefront Proxy](#Metrics-Collected-When-Using-the-Wavefront-Proxy)
* [Metrics Collected When Using Direct Ingestion](#Metrics-Collected-When-Using-Direct-Ingestion)
* [Where to Go Next](#Where-to-Go-Next)

## Overview
Wavefront Java SDKs automatically collect a suite of diagnostic metrics that allow you to monitor the performance of your `WavefrontSender` instance. These metrics are collected once every minute and are reported to Wavefront using your `WavefrontSender` instance. How you send data to Wavefront determines the metrics that are collected

## Metrics Collected When Using the Wavefront Proxy
If you are sending data to Wavefront via the Wavefront proxy using a `WavefrontProxyClient` instance, the following diagnostic metrics are reported:

* [Metric Points Handler](#Metric-Points-Handler)
* [Histogram Distributions Handler](#Histogram-Distributions-Handler)
* [Tracing Handler](#Tracing-Handler)

### Metrics for Metric Point Handler
| Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.proxy.points.discarded.count                     |Counter    |Points discarded because port(s) were not configured|
|~sdk.java.core.sender.proxy.points.valid.count                         |Counter    |Valid points received.|
|~sdk.java.core.sender.proxy.points.invalid.count                       |Counter    |Invalid points received.|
|~sdk.java.core.sender.proxy.points.dropped.count                       |Counter    |Number of points dropped due to errors thrown when sending data to socket.|
|~sdk.java.core.sender.proxy.metricHandler.errors.count                 |Counter    |Errors encountered, excluding connection errors. |
|~sdk.java.core.sender.proxy.metricHandler.connect.errors.count         |Counter    |Errors encountered connecting to remote socket.|
|~sdk.java.core.sender.proxy.metricHandler.socket.write.success.count   |Counter    |Successful writes to socket.|
|~sdk.java.core.sender.proxy.metricHandler.socket.write.errors.count    |Counter    |Unsuccessful writes to socket.|
|~sdk.java.core.sender.proxy.metricHandler.socket.flush.success.count   |Counter    |Successful socket flushes.|
|~sdk.java.core.sender.proxy.metricHandler.socket.flush.errors.count    |Counter    |Unsuccessful socket flushes.|
|~sdk.java.core.sender.proxy.metricHandler.socket.reset.success.count   |Counter    |Successful connection resets.|
|~sdk.java.core.sender.proxy.metricHandler.socket.reset.errors.count    |Counter    |Unsuccessful connection resets.|

### Metrics for Histogram Distribution Handler
|Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.proxy.histograms.discarded.count                     |Counter    |Distributions discarded because port(s) were not configured.|
|~sdk.java.core.sender.proxy.histograms.valid.count                         |Counter    |Valid distributions received.|
|~sdk.java.core.sender.proxy.histograms.invalid.count                       |Counter    |Invalid distributions received.|
|~sdk.java.core.sender.proxy.histograms.dropped.count                       |Counter    |Number of distributions dropped due to errors thrown when sending data to socket.|
|~sdk.java.core.sender.proxy.histogramHandler.errors.count                  |Counter    |Errors encountered, excluding connection errors. |
|~sdk.java.core.sender.proxy.histogramHandler.connect.errors.count          |Counter    |Errors encountered connecting to remote socket.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.write.success.count    |Counter    |Successful writes to socket.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.write.errors.count     |Counter    |Unsuccessful writes to socket.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.flush.success.count    |Counter    |Successful socket flushes.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.flush.errors.count     |Counter    |Unsuccessful socket flushes.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.reset.success.count    |Counter    |Successful connection resets.|
|~sdk.java.core.sender.proxy.histogramHandler.socket.reset.errors.count     |Counter    |Unsuccessful connection resets.|

### Metrics for Tracing Handler
|Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.proxy.spans.discarded.count                      |Counter    |Spans discarded because port(s) were not configured.|
|~sdk.java.core.sender.proxy.spans.valid.count                          |Counter    |Valid spans received.|
|~sdk.java.core.sender.proxy.spans.invalid.count                        |Counter    |Invalid spans received.|
|~sdk.java.core.sender.proxy.spans.dropped.count                        |Counter    |Number of spans dropped due to errors thrown when sending data to socket.|
|~sdk.java.core.sender.proxy.tracingHandler.errors.count                |Counter    |Errors encountered, excluding connection errors. |
|~sdk.java.core.sender.proxy.tracingHandler.connect.errors.count        |Counter    |Errors encountered connecting to remote socket.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.write.success.count  |Counter    |Successful writes to socket.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.write.errors.count   |Counter    |Unsuccessful writes to socket.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.flush.success.count  |Counter    |Successful socket flushes.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.flush.errors.count   |Counter    |Unsuccessful socket flushes.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.reset.success.count  |Counter    |Successful connection resets.|
|~sdk.java.core.sender.proxy.tracingHandler.socket.reset.errors.count   |Counter    |Unsuccessful connection resets.|

## Metrics Collected When Using Direct Ingestion
If you directly sending data to Wavefront using a `WavefrontDirectIngestionClient` instance, the following diagnostic metrics are reported:

* [Metric Points Ingestion](#Metric-Points-Ingestion)
* [Histogram Distributions Ingestion](#Histogram-Distributions-Ingestion)
* [Tracing Spans Ingestion](#Tracing-Spans-Ingestion)

### Metrics for Metric Point Ingestion
|Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.direct.points.queue.size                 |Gauge      |Points in ingestion buffer.|
|~sdk.java.core.sender.direct.points.queue.remaining_capacity   |Gauge      |Remaining capacity of ingestion buffer.|
|~sdk.java.core.sender.direct.points.valid.count                |Counter    |Valid points received.|
|~sdk.java.core.sender.direct.points.invalid.count              |Counter    |Invalid points received.|
|~sdk.java.core.sender.direct.points.dropped.count              |Counter    |Valid points that are dropped during ingestion.|
|~sdk.java.core.sender.direct.points.report.errors.count        |Counter    |Exceptions encountered while reporting points.|
|~sdk.java.core.sender.direct.points.report.202.count           |Counter    |Report requests by response status code.|

### Metrics for Histogram Distribution Ingestion
|Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.direct.histograms.queue.size                 |Gauge      |Distributions in ingestion buffer.|
|~sdk.java.core.sender.direct.histograms.queue.remaining_capacity   |Gauge      |Remaining capacity of ingestion buffer.|
|~sdk.java.core.sender.direct.histograms.valid.count                |Counter    |Valid distributions received.|
|~sdk.java.core.sender.direct.histograms.invalid.count              |Counter    |Invalid distributions received.|
|~sdk.java.core.sender.direct.histograms.dropped.count              |Counter    |Valid distributions that are dropped during ingestion.|
|~sdk.java.core.sender.direct.histograms.report.errors.count        |Counter    |Exceptions encountered while reporting distributions.|
|~sdk.java.core.sender.direct.histograms.report.202.count           |Counter    |Report requests by response status code.|

### Metrics for Tracing Span Ingestion
|Name|Type|Description|
|:---|:---:|:---|
|~sdk.java.core.sender.direct.spans.queue.size                  |Gauge      |Spans in ingestion buffer.|
|~sdk.java.core.sender.direct.spans.queue.remaining_capacity    |Gauge      |Remaining capacity of ingestion buffer.|
|~sdk.java.core.sender.direct.spans.valid.count                 |Counter    |Valid spans received.|
|~sdk.java.core.sender.direct.spans.invalid.count               |Counter    |Invalid spans received.|
|~sdk.java.core.sender.direct.spans.dropped.count               |Counter    |Valid spans that are dropped during ingestion.|
|~sdk.java.core.sender.direct.spans.report.errors.count         |Counter    |Exceptions encountered while reporting spans.|
|~sdk.java.core.sender.direct.spans.report.202.count            |Counter    |Report requests by response status code.|

## Where to Go Next

To continue, select one of the Wavefront Java SDK links in the table below.

<table id="SDKlevels" style="width: 100%">
<tr>
  <th width="10%">SDK Type</th>
  <th width="60%">SDK Description</th>
  <th width="30%">Java SDKs</th>
</tr>

<tr>
  <td><a href="https://docs.wavefront.com/wavefront_sdks.html#sdks-that-instrument-frameworks">OpenTracing SDK</a></td>
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
