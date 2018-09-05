# Technical Writer Code Challenge

## Contents
 - [Environment](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#environment)
 - [Add Tags to the Datadog Agent](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#add-tags-to-the-datadog-agent)
 - [Install MongoDB and the Datadog Integration](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#install-mongodb-and-the-datadog-integration)
 - [Collect Custom Agent Metrics](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#collect-custom-agent-metrics)
 - [Create a Timeboard](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#create-a-timeboard)
 - [Blog](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#blog)

## Environment
  Ubuntu 12.04 VM, set up using [Vagrant](https://www.vagrantup.com/intro/getting-started/) and [Virtual Box](https://www.virtualbox.org/).

## Add Tags to the Datadog Agent
### steps to reproduce
  1. To add tags to the agent after installation, navigate to the `datadog-agent` directory (`/etc/datadog-agent`). Edit the `datadog.yaml` file in `/etc/datadog-agent` to configure custom tags.
  ```
  tags:
    - this-tag
    - this-other-tag
  ```
  2. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent.
  3. Navigate to the [Host Map](https://app.datadoghq.com/infrastructure/map) on the Datadog dashboard to see the agent with its associated tags.
  ![agent with tags](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/vm-tag.png)

## Install MongoDB and the Datadog Integration
### steps to reproduce
  1. Install [MongoDB for Ubuntu](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/). Run `sudo service mongodb start` to start the service and `mongo` to open a Mongo shell.
  2. In the shell, switch to admin (`use admin`) and create the read-only Datatdog user per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb).
  3. In `/etc/datadog-agent/conf.d/mongo.d/mongo.yaml` set the configurations per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb).
  4. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent.

## Collect Custom Agent Metrics
### steps to reproduce
  1. In `/etc/datadog-agent/checks.d/my-metric.py` add the implementation for a metric to be generated. In this case, the metric generated is just a random number between 0 and 1000.
  ```
  from checks import AgentCheck
  import random

  class MyMetric(AgentCheck):
      def check(self, instance):
          self.gauge('my_metric', random.randint(0, 1000))
  ```
The custom check inherits from Datadog's AgentCheck, which requires the `check()` method. The AgentCheck's `gauge()` method is used to record the current value of a metric.

  2. In the `/etc/datadog-agent/conf.d/my_metric.yaml` file, add the following configuration:
  ```
  init_config:

  instances:
    - min_collection_interval: 45
  ```
  Here, one instance is set, meaning that the `check()` in `my-metric.py` will run once per collection.

  **Bonus -** The instances section sets the collection interval to 45 seconds. This means that each time the agent's collector runs, it will check to see if 45 seconds or more have passed since this metric was last checked. If so, the custom check will be run.

  3. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent. Run `sudo -u dd-agent -- datadog-agent check my_metric` to confirm `my_metric` has been added to the collector's checks.
  4. See `my_metric` in the Datadog metric explorer, shown below.
  ![my-metric in metric explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/my_metric_explorer.png)

## Create a Timeboard
### steps to reproduce
  1. Run the following curl request, which includes API and application keys.
```
curl  -X POST -H "Content-type: application/json" -d '{
      "graphs" : [{
          "title": "My Metrics + MongoDatasize Anomoly Metric",
          "definition": {
              "events": [],
              "requests": [
                  {"q": "avg:my_metric{host:precise64}"},
                  {"q": "anomalies(avg:mongodb.stats.datasize{host:precise64},\"basic\",2)"}
              ]
          },
          "viz": "timeseries"
      }],
      "title" : "My Timeboard",
      "description" : "Two fun metrics in one timeboard",
      "read_only": "True"
}' "https://api.datadoghq.com/api/v1/dash?api_key=MY_API_KEY&application_key=MY_KEY"
```
The request includes two queries: one for `my_metric` and one showing the database datasize with the anomaly function applied. **Bonus -** The anomaly function uses time series data to detect results that fall outside of normal range (in this case, the function is set to two standard deviations.) The 'basic' argument in the anomaly function indicates that only short term behavior will be tracked (not historical trends).

 This means that if data is added to the database, the next size query will fall outside of normal range. The image below shows anomaly detection of the database data size change:
![my-metric in metric explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/my_timeseries.png)
  2. With the Timeboard displayed in the Datadog UI, select a five minute range on the timeline. Click the snapshot button and include an @user comment to send a snapshot notification.
  ![my-metric in metric explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/timeboard_email.png)

# Blog
## A Quickstart for Metrics Collection: The datadog-metrics Node Package
Created by Datadog community member Daniel Bader the [datadog-metrics package](https://www.npmjs.com/package/datadog-metrics) provides quick and easy setup for reporting metrics through a Node application to Datadog's API.

The datadog-metrics package provides:
 - A Node.js interface for reporting metrics
 - Easy setup - no need to install a Datadog agent
 - Aggregation - run multiple metrics, and have datadog-metrics aggregate by key and tag
 - Buffering - no need to send a request for each reported metric
 - Histogram metrics calculation and reporting - call `histogram()` with a key and value, and have datadog-metrics calculate histogram metrics to send to Datadog
 - Gauge and counter metrics reporting

### Install and Config
To try out datadog-metrics, start by installing the Node package `npm i datadog-metrics`.

Next, create a JavaScript file to configure the metric collection.

```
const metrics = require('datadog-metrics');
metrics.init({ host:'precise64', prefix: 'myapp.' });

function collectStats() {
    const memUsage = process.memoryUsage();
    metrics.gauge('just.five', 5);
    metrics.gauge('memory.heapTotal', memUsage.heapTotal);
}

setInterval(collectStats, 5000);
```
Optionally, start by initializing the metrics collection, as shown above. Initialization properties, such as host and prefix, are described in the [package README](https://github.com/dbader/node-datadog-metrics).

After intialization, a set interval can be used to report metrics to Datadog. In the example below, the 'just.five' and 'memory.healTotal' gauge metrics are run at five second intervals. Aside from `gauge()`, which reports a metric's current value, datadog-metrics, also supports counter (increment by a given value) and histogram metrics. Histogram calculates average, count, minimum, maximum, and percentile values. See how the histogram calculation works in the [source code](https://github.com/dbader/node-datadog-metrics/blob/master/lib/metrics.js), and read about `gauge()`, `count()`, and `histogram()` in the [package README](https://github.com/dbader/node-datadog-metrics).

Run the following command to start the metrics collection. Note that your API key can be found in `Integrations >> APIs` when you log on to Datadog. Also note that DEBUG enables logging.

`DATADOG_API_KEY=<YOUR_KEY> DEBUG=metrics node <FILE_NAME>.js`

If the metric collection begins successfully, logging will begin, as shown below.

![datadog-metrics logging](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/blog_metrics.png)

After a few minutes, the [Datadog Metrics Explorer](https://app.datadoghq.com/metric/summary) should display the new metrics being reported.

![metrics explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/just.five.png)

### Additional Features
 - The datadog-metrics package also flushes, or sends buffered metrics to Datadog, using the `metrics.flush([onSuccess[, onError]])` function. By default, the metric collection will be flushed every 15 seconds, though `flush()` can also be invoked any time you need metrics to be sent.
 - When each metric is run, datadog-metrics initializes a new instance of `BufferedMetricsLogger` with default or null properties if none are set. For maximum customization, you can create your own instances of `BufferedMetricsLogger` and set properties (apiKey, appKey, host, prefix, defaultTags, flushIntervalSeconds) as needed.
 - Looking to see more specifics about how datadog-metrics works? [Test files](https://github.com/dbader/node-datadog-metrics/tree/master/test) in the package source code are a great way to find out more about expected inputs and outputs for the metrics, logger, and aggregator functions.

 With commented source code, tests, and detailed documentation, datadog-metrics is a Node-user-friendly quickstart to metrics reporting!
