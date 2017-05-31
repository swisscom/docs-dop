+++
date = "2017-05-31T12:41:27+02:00"
title = "winrm/puppet_agent_run"
toc = true
weight = 13

+++

This plugin runs puppet on a node and features some important workarounds for common
problems encountered when doing so, like waiting if a run is already in progress or
doing reruns on errors if configured. 

| Plugin Name            | Since Version |
|------------------------|---------------|
| winrm/puppet_agent_run | 0.13.0        |

## Plugin Parameters

{{< command_partial "puppet_run" >}}
{{< command_partial "arguments" >}}
{{< command_partial "env" >}}
{{< command_partial "output" >}}

## Connector Parameters

{{< connector_partial "winrm" >}}

## Examples:

### Simple Example

    - name "Run puppet in noop mode"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'winrm/puppet_agent_run'
        arguments: '--noop'

### Complete Example

    - name "Run Puppet until there are no errors and changes anymore"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'winrm/puppet_agent_run'
        rerun_on_change: true
        rerun_on_error: true
        max_rerun: 10

