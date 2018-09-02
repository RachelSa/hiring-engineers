# Technical Writer Code Challenge

## Contents
 - [Environment Setup](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/answers.md#environment-setup)
 - [Add Tags to the Datadog Agent]()
 - [Install MongoDB and Datadog MongoDB Integration]()
 - [Collect Custom Agent Metrics]()
 - [Create Timeboard]()
 - [Access Timeboard from Datadog Dashboard]()
 - [Blog]()
 - [Reference]()

## Environment Setup 
### steps to reproduce
  1. To install the Datadog Agent to a VM, first [install **Vagrant**](https://www.vagrantup.com/intro/getting-started/) and [**Virtual Box**](https://www.virtualbox.org/). Use the [Vagrant getting started guide](https://www.vagrantup.com/intro/getting-started/) to initialize, start, and SSH into an Ubuntu 12.04 VM.
  2. Use Datadog's [one-step Ubuntu Agent installation](https://app.datadoghq.com/account/settings#agent/ubuntu) to add the agent to the Ubuntu VM.

## Add Tags to the Datadog Agent - steps to reproduce
  1. To add tags to the agent after installation, navigate to the `datadog-agent` directory (`/etc/datadog-agent`). Edit the `datadog.yaml` file in `/etc/datadog-agent` to configure custom tags.
  ```
  tags:
    - this-tag
    - this-other-tag
  ```
  2. Navigate to the [Host Map](https://app.datadoghq.com/infrastructure/map) on the Datadog dashboard to see the Agent with its associated tags.
  ![agent with tags](https://github.com/RachelSa/hiring-engineers/blob/tech-writer/images/vm-tag.png)

## Install MongoDB and Datadog MongoDB Integration - steps to reproduce
  1. Install [MongoDB for Ubuntu](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/). Run `sudo service mongodb start` to start the service and `mongo` to open a Mongo shell.
  2. In the shell, switch to admin `use admin` and create the read-only Datatdog user per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb). Close the shell.
  3. In `/etc/datadog-agent/conf.d/mongo.d/mongo.yaml` set the configurations per Datadog [integrations instructions](https://app.datadoghq.com/account/settings#integrations/mongodb).
  4. Stop (`sudo service datadog-agent stop`) and restart (`sudo service datadog-agent start`) the Datadog agent.

## Collect Custom Agent Metrics - steps to reproduce
  - my_metric

  **Bonus - Set the Collection Interval to 45 seconds.**

## Create Timeboard
include script

## Access Timeboard from Datadog Dashboard

**Bonus - What is the Anomaly graph displaying?**

# Blog
##

## Reference
- **Vagrant**: open-source software used for maintaining virtual environments, such as Virtual Box.
- **Virtual Box**: open-source product used to create and run a virtual machine.
