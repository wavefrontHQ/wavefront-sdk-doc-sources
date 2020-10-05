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

|  Tag  |  Description  |
|:------|:--------------|
| `application`   |  Name that identifies your Java application, for example: `OrderingApp`. All microservices in the same application should share the same application name.  |
|  `service`  |  Name that identifies the microservice within your application, for example: `inventory`. Each microservice should have its own service name.  |

## Optional Tags

**Optional tags** enable you to use the physical topology of your application to further filter your data:

|  Tag  |  Description  |
|:------|:--------------|
| `cluster`   | Name of a group of related hosts that serves as a cluster or region in which the application will run, for example: `us-west-2`.   |
| `shard`   | Name of a subgroup of hosts within a cluster that serves as a partition, replica, shard, or mirror, for example: `secondary`.   |


Optionally, you can add custom tags specific to your application or add environment variables as custom tags.

## Example

Creating an `ApplicationTags` instance:

```python
from wavefront_sdk.common import ApplicationTags

application_tags = ApplicationTags(application="OrderingApp",
                                   service="inventory",
                                   cluster="us-west-2",
                                   shard="secondary",
                                   custom_tags=[("location", "Oregon")])
# Add all environment variables with names starting with MY_ as custom tags.
application_tags.add_custom_tags_from_env("^MY_.*$")
# Add the environment variable POD_NAME as the custom tag with the tag name pod_name.
application_tags.add_custom_tag_from_env("pod_name", "POD_NAME")
```

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
