
### port (optional)

| Key  | Allowed Values | Default | Since Version |
|------|----------------|---------|---------------|
| port | 1 - 65536      | 5985    | 0.4.0         |

The winrm port on the target machine

### ssl (optional)

| Key | Allowed Values | Default | Since Version |
|-----|----------------|---------|---------------|
| ssl | true, false    | true    | 0.4.0         |

Use ssl for transport encryption

### ca_trust_path (optional)

| Key           | Allowed Values | Default | Since Version |
|---------------|----------------|---------|---------------|
| ca_trust_path | String         | nil     | 0.4.0         |

The path to the ca used for the ssl certificates

### disable_sspi (optional)

| Key          | Allowed Values | Default | Since Version |
|--------------|----------------|---------|---------------|
| disable_sspi | true,false     | false   | 0.4.0         |

Disable SSPI

### basic_auth_only (optional)

| Key             | Allowed Values | Default | Since Version |
|-----------------|----------------|---------|---------------|
| basic_auth_only | true,false     | false   | 0.4.0         |

Use only basic auth

### operation_timeout (optional)

| Key               | Allowed Values | Default           | Since Version |
|-------------------|----------------|-------------------|---------------|
| operation_timeout | > 0            | plugin_timeout -5 | 0.13.0        |

Timeout for the command until it trows an error if it does not return

