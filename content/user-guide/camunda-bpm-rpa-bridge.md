---

title: 'Camunda BPM RPA Bridge'
weight: 300

menu:
  main:
    identifier: "camunda-bpm-rpa-bridge"
    parent: "user-guide"

---

The Camunda RPA bridge is a standalone application that allows to call RPA (robotic process automation) bots from BPMN models deployed to a Camunda engine. RPA bots can be orchestrated as External Tasks using the Camunda Modeler and Cawemo.

For detailed instructions on how to connect your first RPA bot to a BPMN model (using Cawemo and the Camunda Modeler) and execute it from a running process instnace using the Camunda engine head over to our [installation guide]({{< ref "/installation/camunda-bpm-rpa-bridge.md" >}})

{{< enterprise >}}
  Please note that the RPA bridge is only available as enterprise edition.
{{< /enterprise >}}

# The Concepts

The Camunda RPA bridge serves a connector between Camunda (BPMN) on the one side and UIPath (RPA) on the other side. Processes running inside the Camunda engine can define external tasks that are marked as RPA tasks.

The bridge extends the regular [Java External Task Client](https://github.com/camunda/camunda-external-task-client-java), fetches and locks the RPA tasks and starts a job in UIPath. Once the job is done, UIPath will notify a webhook in the bridge about the job result and state (Success/Failure). The bridge will complete the previously locked external task and pass any result variables received from UIPath along to the Camunda engine.

# Prerequisites and Supported Environments
The Camunda RPA Bridge supports the on-premise version of the [UIPath Orchestrator](https://www.uipath.com/product/orchestrator). To execute RPA bots from Camunda you will need:

* An on-premise installation of [UIPath Orchestrator](https://www.uipath.com/product/orchestrator).
* Camunda BPM 7.14 Enterprise Edition or later
* Java 8 or later installed on the machine that runs the Camunda RPA bridge

To design a BPMN model that connects to one or more RPA bots through the bridge, the following tools are very helpful:

* Cawemo 1.4 or later (to create and distribute worker catalogs)
* Camunda Modeler 4.2 or later (to apply the worker catalog to your process model)

# Camunda BPM RPA Bridge configuration
The bridge is configurable through the `application.yml` file that is included in the provided archive.

## Configure Access to the Camunda API

<table class="table desc-table">
  <tr>
      <th>Prefix</th>
      <th>Property name</th>
      <th>Description</th>
      <th>Default value</th>
  </tr>
  <tr>
      <td rowspan="15"><code>org.camunda.bpm.rpa.camunda-api</code></td>
      <td><code>url</code></td>
      <td>The URL to the Camunda REST API (e.g. `http://localhost:8080/engine-rest`)</td>
      <td>-</td>
  </tr>
  <tr>
      <td><code>authentication.type</code></td>
      <td>The type of authentication to access the REST API (e.g. `basic`)</td>
      <td>-</td>
  </tr>
  <tr>
      <td><code>authentication.username</code></td>
      <td>The username to authenticate against the REST API.</td>
      <td>-</td>
  </tr>
  <tr>
      <td><code>authentication.password</code></td>
      <td>The password to authenticate against the REST API.</td>
      <td>-</td>
  </tr>
</table>

## Configure Access to UIPath Orchestrator

## API

<table class="table desc-table">
  <tr>
      <th>Prefix</th>
      <th>Property name</th>
      <th>Description</th>
      <th>Default value</th>
  </tr>
  <tr>
      <td rowspan="15"><code>org.camunda.bpm.rpa.uipath-api</code></td>
      <td><code>topics</code></td>
      <td>The topics on which the bridge will listen for RPA tasks.</td>
      <td>uipath, RPA</td>
  </tr>
  <tr>
      <td><code>url</code></td>
      <td>The URL to your UIPath Orchestrator instance.</td>
      <td>https://platform.uipath.com/</td>
  </tr>
  <tr>
      <td><code>account-name</code></td>
      <td>The name of the UIPath account. This is only used for authentication.</td>
      <td>-</td>
  </tr>
  <tr>
      <td><code>tenant-name</code></td>
      <td>The name of the UIPath tenant. This is only used for authentication.</td>
      <td>-</td>
  </tr>
</table>

## Authentication

<table  class="table desc-table">
  <tr>
      <td rowspan="15"><code>org.camunda.bpm.rpa.uipath-api.authentication</code></td>
      <td><code>auth-url</code></td>
      <td>The URL used for authentication against the UIPath API.</td>
      <td>https://account.uipath.com/oauth/token/</td>
  </tr>
  <tr>
      <td><code>client-id</code></td>
      <td>The client ID as issued by UIPath. This is only used for authentication.</td>
      <td>-</td>
  </tr>
  <tr>
      <td><code>refresh-token</code></td>
      <td>An API access token issued by UIPath. This is used to refresh the authentication token when it expires.</td>
      <td>-</td>
  </tr>
</table>

## Webhook

<table class="table desc-table">
  <tr>
    <td rowspan="15"><code>org.camunda.bpm.rpa.uipath-api.webhook</code></td>
      <td><code>secret</code></td>
      <td>secret used in your UiPath webhook configuration</td>
      <td>-</td>
  </tr>
  <tr>
    <td>path</td>
    <td>Relative path of the webhook endpoint in your application.</td>
    <td>/webhook/event</td>
  </tr>
</table>