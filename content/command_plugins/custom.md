+++
date = "2017-04-26T12:09:16+02:00"
title = "custom"
toc = true
weight = 1
+++

The custom command plugin executes commands localy on the node DOPi is running on.

| Plugin Name | Since Version |
|-------------|---------------|
| custom      | 0.1           |

## Plugin Parameters

{{< command_partial "exec" >}}
{{< command_partial "arguments" >}}
{{< command_partial "env" >}}
{{< command_partial "exit_codes" >}}
{{< command_partial "output" >}}

## Examples:

### Simple Example

```YAML
    - name "Say Hello World on the machine DOPi is running on"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'custom'
        exec: 'echo'
        arguments: "Hello World from ${DOP_NODE_FQDN}"
```

### Complete Example

```YAML
    - name "Install an RPM on the machine DOPi is running on"
      nodes:
        - 'web01.example.com'
      command:
        plugin: 'custom'
        exec: 'yum'
        arguments: 'install -y puppet'
        expect_exit_codes: 0
        fail_on_warning: False
        parse_output:
          error:
            - '^No package puppet available'
            - 'Some other Error pattern'
          warning:
            - '^Warning:'
```

