
### env (optional)

| Key       | Allowed Values | Default                           | Since Version |
|-----------|----------------|-----------------------------------|---------------|
| arguments | Hash           | { DOP_NODE_FQDN => fqdn_of_node } | 0.1           |

The environment variables that should be set in the context of the command. Additional
variables can be defined in a hash which will be merged with the default.

Example:

    env:
      PATH: '/usr/bin:/bin'
      FOO:  'bar'

