+++
date = "2017-04-26T15:37:53+02:00"
title = "ssh/custom"
toc = true
weight = 10

+++

This plugin executes custom commands on every node in the step over ssh.

| Plugin Name | Since Version |
|-------------|---------------|
| ssh/custom  | 0.1           |

## Plugin Parameters

{{< command_partial "exec" >}}
{{< command_partial "arguments" >}}
{{< command_partial "env" >}}
{{< command_partial "exit_codes" >}}
{{< command_partial "output" >}}

## Connector Parameters

{{< connector_partial "ssh" >}}

## Examples:

### Simple Example

    - name "Say Hello World on a node"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'ssh/custom'
        exec: 'echo'
        arguments: "Hello World"

### Complete Example

    - name "Install an RPM on the node"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'ssh/custom'
        exec: 'yum'
        arguments: 'install -y puppet'
        expect_exit_codes: 0
        fail_on_warning: False
        parse_output:
          error:
            - '^No package puppet available'
