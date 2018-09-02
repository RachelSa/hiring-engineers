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
  2. Navigate to the [Host Map](https://app.datadoghq.com/infrastructure/map) on the Datadog dashboard to see the Agent with its associated tags.
  ![agent with tags](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/vm-tag.png)

## Install MongoDB and Datadog Integration
### steps to reproduce
  1. Install [MongoDB for Ubuntu](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/). Run `sudo service mongodb start` to start the service and `mongo` to open a Mongo shell.
  2. In the shell, switch to admin `use admin` and create the read-only Datatdog user per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb). Close the shell.
  3. In `/etc/datadog-agent/conf.d/mongo.d/mongo.yaml` set the configurations per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb).
  4. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent.

## Collect Custom Agent Metrics
### steps to reproduce
  1. In '/etc/datadog-agent/checks.d/my-metric.py`, add the implementation for a metric to be generated. In this case, the metric to be collected is simply a random number.
  ```
  from checks import AgentCheck
  import random

  class MyMetric(AgentCheck):
      def check(self, instance):
          self.gauge('my_metric', random.randint(0, 1000))
  ```

  2. In the `/etc/datadog-agent/conf.d/my_metric.yaml` file, add the following configuration:
  ```
  init_config:

  instances:
    - min_collection_interval: 45
  ```
  Here, one instance is set, meaning that the check() in `my-metric.py` will run once.

  **Bonus - ** The instances section sets the collection interval to 45 seconds. This means that each time the Agent's collector runs, it will check to see if 45 seconds or more have passed since this metric was last checked. If so, the custom check will be run.

  3. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent. Run `sudo -u dd-agent -- datadog-agent check my_metric` to confirm `my_metric` has been added to the collector's checks.
  4. See my_metric in the Datadog metric explorer, shown below.
  ![my-metric in metric explorer](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/my-metric-explorer.png)

## Create Timeboard
### steps to reproduce
include script

## Access Timeboard from Datadog UI
### steps to reproduce

**Bonus - What is the Anomaly graph displaying?**

# Blog
##

## Reference
- **Vagrant**: open-source software used for maintaining virtual environments, such as Virtual Box.
- **Virtual Box**: open-source product used to create and run a virtual machine.
