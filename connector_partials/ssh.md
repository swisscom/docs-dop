

### check_host_key (optional)

| Key             | Allowed Values    | Default | Since Version |
|-----------------|-------------------|---------|---------------|
| check_host_key  | true,false        | false   | 0.12.0        |

By default the ssh connector  will skip the host key checks. This
means you will be potentially vulnerable to man in the middle attacks. Since
DOPi is designed for the provisioning phase you are working with new nodes and
this will usually not be an issue, however changing keys because nodes get
new provisioned will be. 

### quiet (optional)

| Key   | Allowed Values    | Default | Since Version |
|-------|-------------------|---------|---------------|
| quiet | true,false        | true    | 0.1           |

By default ssh will run in quiet mode and stuff like login banners will not
appear in the output. See the documentation about the "-q" flag in the ssh
man page for more information about this.

### port (optional)

| Key  | Allowed Values    | Default | Since Version |
|------|-------------------|---------|---------------|
| port | Numeric           | 22      | 0.10          |

The port to which ssh should connect

### base64 (optional)

| Key    | Allowed Values    | Default | Since Version |
|--------|-------------------|---------|---------------|
| base64 | true,false        | true    | 0.12          |

This will encode the command with base64 before it is added to the commandline
for the ssh call. This allows to pass complex multiline scripts to the node.

This option is only here for legacy reasons and has usually no use.

