# OD Alerting CLI

Alerting CLI enables to manage and organize Elasticsearch Alerting monitors through YAML file.

## Why would I want to manage my monitors in YAML files?

Excellent question. So many reasons:

* You can store your monitors in source control so you can track changes (who changed this monitor?), and perform code reviews.
* Enable through automated pipelines.
* It is very easy to accidentally make changes to monitors on UI or through API. You can always refer back on this and update existing monitors.
* You may have multiple stages and would like have monitors running on each stage.

## Installation

## Getting Started

Currently, this CLI doesn't support how the destinations are managed. This will be supported in up-coming versions.
For now, after installing you can run the commands to sync your destinations.

```
alerting-config-cli sync --destinations
```

Above command will create auto-generated destinations file with names and destinationId , so that they're easy to refer inside monitors.

### Create new monitor

```
alerting-config-cli init --new-monitor --name MonitorName

```

This will create new monitor in new file, you can always add multiple monitors in same file ensure it is list.

### Monitors diff

```
alerting-config-cli diff

```

This command will compare your local monitors with remote monitors.

### publish monitors to remote ES

Publish local monitors to remote elasticsearch cluster:

- will create new monitors
- will update existing monitors (so it could override what you were doing if
  you edit an existing monitor in Kibana alerting or by any other way)
- This cli will not delete anything from remote you will need to add `--delete` flag to delete untracked monitors

Sample monitor

```
- name: 'Sample Alerting monitor'
  type: 'monitor'
  schedule:
    period:
      interval: 10
      unit: MINUTES
  enabled: true
  inputs:
    - search:
        indices: ["log*"]
        query: # This is valid Elasticsearch query
          size: 0
          query:
            match_all: {
              boost: 1.0
            }
  triggers:
    - name: '500'
      severity: '2'
      condition: "return true"
      actions:
        - name: Sample Action
          destinationId: test_my_destination
          subject:'Error'
          message: 'There is an error'
```