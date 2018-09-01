# Technical Writer Code Challenge
bold reference terms
## Environment Setup - steps to reproduce
  1. To install the Datadog Agent to a VM, first [install *Vagrant*](https://www.vagrantup.com/intro/getting-started/) and [*Virtual Box*](https://www.virtualbox.org/). Use the [Vagrant getting started guide](https://www.vagrantup.com/intro/getting-started/) to initialize, start, and SSH into an Ubuntu 12.04 VM.
  2. Use Datadog's [one-step Ubuntu Agent installation](https://app.datadoghq.com/account/settings#agent/ubuntu) to add the agent to the Ubuntu VM.

## Adding Tags to Datadog Agent - steps to reproduce
  1. To add tags to the agent after installation, navigate to the `datadog-agent` directory (`/etc/datadog-agent`). Edit the `datadog.yaml` file in `/etc/datadog-agent` to configure custom tags.

  ```
  tags:
    - this-tag
    - this-other-tag
  ```
  2. Navigate to the [Host Map](https://app.datadoghq.com/infrastructure/map) on the Datadog dashboard to see the Agent with its associated tags.
  IMAGE  

## Install MongoDB and Datadog MongoDB Integration - steps to reproduce
  - config file
  - MongoDB setup and integration

## Custom Agent Check
  - my_metric

##  

## Reference
- *Vagrant*: open-source software used for maintaining virtual environments, such as Virtual Box.
- *Virtual Box*: open-source product used to create and run a virtual machine.
