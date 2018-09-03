# Technical Writer Code Challenge

## Contents
 - [Environment Setup](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#environment-setup)
 - [Add Tags to the Datadog Agent](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#add-tags-to-the-datadog-agent)
 - [Install MongoDB and Datadog Integration Integration](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#install-mongodb-and-datadog-integration)
 - [Collect Custom Agent Metrics](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#collect-custom-agent-metrics)
 - [Create Timeboard](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#create-timeboard)
 - [Access Timeboard from Datadog UI](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#access-timeboard-from-datadog-ui)
 - [Blog](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#blog)
 - [Reference](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#reference)

## Environment Setup
### steps to reproduce
  1. To install the Datadog Agent to a VM, first [install **Vagrant**](https://www.vagrantup.com/intro/getting-started/) and [**Virtual Box**](https://www.virtualbox.org/). Use the [Vagrant getting started guide](https://www.vagrantup.com/intro/getting-started/) to initialize, start, and SSH into an Ubuntu 12.04 VM.
  2. Use Datadog's [one-step Ubuntu Agent installation](https://app.datadoghq.com/account/settings#agent/ubuntu) to add the agent to the Ubuntu VM.

## Add Tags to the Datadog Agent
### steps to reproduce
  1. To add tags to the agent after installation, navigate to the `datadog-agent` directory (`/etc/datadog-agent`). Edit the `datadog.yaml` file in `/etc/datadog-agent` to configure custom tags.
  ```
  tags:
    - this-tag
    - this-other-tag
  ```
  2. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent.
  3. Navigate to the [Host Map](https://app.datadoghq.com/infrastructure/map) on the Datadog dashboard to see the Agent with its associated tags.
  ![agent with tags](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/vm-tag.png)

## Install MongoDB and Datadog Integration
### steps to reproduce
  1. Install [MongoDB for Ubuntu](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/). Run `sudo service mongodb start` to start the service and `mongo` to open a Mongo shell.
  2. In the shell, switch to admin `use admin` and create the read-only Datatdog user per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb).
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
The custom check inherits from Datadog's AgentCheck, which requires the `check()` method. The AgentCheck's `.gauge` method is used to record the current value of a metric.

  2. In the `/etc/datadog-agent/conf.d/my_metric.yaml` file, add the following configuration:
  ```
  init_config:

  instances:
    - min_collection_interval: 45
  ```
  Here, one instance is set, meaning that the check() in `my-metric.py` will run once per collection.

  **Bonus -** The instances section sets the collection interval to 45 seconds. This means that each time the Agent's collector runs, it will check to see if 45 seconds or more have passed since this metric was last checked. If so, the custom check will be run.

  3. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent. Run `sudo -u dd-agent -- datadog-agent check my_metric` to confirm `my_metric` has been added to the collector's checks.
  4. See my_metric in the Datadog metric explorer, shown below.
  ![my-metric in metric explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/my_metric_explorer.png)

## Create Timeboard
### steps to reproduce
include script
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

## Access Timeboard from Datadog UI
### steps to reproduce

**Bonus - What is the Anomaly graph displaying?**

# Blog
## A Quickstart for Metrics Collection: The datadog-metrics Node Package
Created by Datadog community member Daniel Bader the [datadog-metrics package](https://www.npmjs.com/package/datadog-metrics) provides a quick and easy setup for reporting metrics through a Node application to Datadog's API.

The datadoog-metrics package provides:
 - A Node.js interface for reporting Metrics
 -

There's no need to set up a Datadog agent to get started. Datadog users can simply install the Node package `npm i datadog-metrics` and create a JavaScript file to configure the metric collection.

```
add example
```
Run the following command will start the metrics collection:
`DATADOG_API_KEY=YOUR_KEY DEBUG=metrics node example_app.js`

Include your API key, which is generated when you create a Datadog account and can be found in Integrations >> APIs.

Check the Datadog Metrics Explorer to see the new metrics being reported.

## Reference
- **Vagrant**: open-source software used for maintaining virtual environments, such as Virtual Box.
- **Virtual Box**: open-source product used to create and run a virtual machine.
