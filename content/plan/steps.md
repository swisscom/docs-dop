+++
date = "2017-04-18T13:37:23+02:00"
title = "Steps"
toc = true
weight = 19

+++

The steps array is a list of commands that have to be executed in the correct order. Each element in the array contains a hash of settings which describe the step, the nodes involved and the command to execute.

Example:
```yaml
steps:
  - name: run_puppet_on_mysql
    nodes:
      - mysql01.example.com
    command: ssh_run_puppet

  - name: run_puppet_on_webserver
    roles:
      - httpd_basic
      - https_special
    command: ssh_run_puppet

  - name: reboot_all_nodes
    nodes: all
    command: ssh_reboot

  - name: run_puppet_in_noop_on_proxies
    roles:
      - haproxy
    command:
      plugin: ssh_puppet_run
      arguments:
        '--noop':
```

You can define multiple sets of steps which can be executed independently of each other.

Example:
```yaml
steps:
  'default':
    - name: run_puppet_on_mysql
      nodes:
        - mysql01.example.com
      command: ssh_run_puppet

    - name: run_puppet_on_webserver
      roles:
        - httpd_basic
        - https_special
      command: ssh_run_puppet

  'maintenance':
    - name: reboot_all_nodes
      nodes: all
      command: ssh_reboot

    - name: run_puppet_in_noop_on_proxies
      roles:
        - haproxy
      command:
        plugin: ssh_puppet_run
        arguments:
          '--noop':
```

The run command will always execute the 'default' step set if nothing else is specified. If only one set is
specified in the array form without a name this set will have the name 'default'.

### name

The name is just an identifier for the step. You should chose a name that best describes what you are doing in this step.

### nodes

This can either be one or a list of nodes and/or Regex patterns or the keyword "all" which will include all nodes for the step.

If an entry starts and ends with a '/' DOPi will interpret the string as a regular expression.

### set_plugin_defaults

(This will be in DOPi >= 0.4)

With "set_plugin_defaults" it is possible to specify some default values for plugin configuration which will persist over subsequent runs.

The settings are node specific, so they will only be set for the nodes in your step. You can select the plugins for which this applies with
a list or with regular expressions.

Direct settings in the plugins will always overwrite the defaults.

IMPORTANT: The keys you want to set have to be ruby symbols. This is a current limitation of the way the parser is implemented and may change in the future

Example:
```yaml

steps:
  - name: "Set default passwords for Plugins"
    nodes: all
    set_plugin_defaults:
      - plugins: '/^ssh/'
        :credentials: 'linux_staging_password'
      - plugins: '/^winrm/'
        :credentials: 'windows_staging_password'
      - plugins:
        - 'ssh/custom'
        :quiet: false

```

### delete_plugin_defaults

(This will be in DOPi >= 0.4)

There are several possibilities how you can remove plugin settings with "delete_plugin_defaults"

IMPORTANT: The keys you want to set have to be ruby symbols. This is a current limitation of the way the parser is implemented and may change in the future

Example:
```yaml
  - name: "Remove some specific defaults for all nodes"
    nodes: all
    delete_plugin_defaults:
      - plugins: '/^ssh/'
        delete_keys:
          - :credentials
          - :timeout

  - name: "Remove all the defaults for the ssh plugins for all nodes in role 'foo'"
    roles:
      - foo
    delete_plugin_defaults:
      - plugins: '/^ssh/'
        delete_keys: all

  - name: "Remove all the defaults for all plugins for all nodes"
    nodes: all
    delete_plugin_defaults: all

```

### nodes_by_config

Include nodes to a step by specific configuration values which are resolved over hiera.

Example:
```yaml

configuration:
  nodes:
    'mysql01.example.com':
      'my_alias': 'database_01'

steps:
  - name: 'include by config'
    nodes_by_config:
      'my_alias': 'database_01'
    command: ssh_run_puppet

```

If the value of the config variable is an array it will check each value in that array. You can also use pattern here like with node

Example:
```yaml

configuration:
  nodes:
    'mysql01.example.com':
      'my_alias':
        - 'database_01'
        - 'some_other_alias'

steps:
  - name: 'include by config'
    nodes_by_config:
      my_alias:
        - '/^linux/'
        - 'database_01'
    command: ssh_run_puppet

```

### roles

This will include all the nodes with a certain role to a step.

roles and nodes can be mixed, dop_common will simply merge the list of nodes. However there has to be at least one node in every step.

If an entry starts and ends with a '/' DOPi will interpret the string as a regular expression.

roles is basically just a special case for nodes_by_config with the roles variable. But it will do some additional checks and you can also set
a default value for the role on DOPi.

### exclude_nodes

A list of nodes to exclude from the list that gets assembled from nodes and roles. This can also contain Regex patterns like nodes and roles.

### exclude_nodes_by_config

Exclude nodes based on config values and matching patterns.

### exclude_roles

Works exactly like exclude_nodes but excludes roles.

### commands

The commands can either be directly a plugin name if a simgle plugin and no parameters, a single command hash which will be passed to the plugin or an array of command hashes if multiple commands have to be executed in a single step. The only fixed variable here is the **plugin** variable. The rest of the variables in the command hash depends on the plugin in use and how it will parse the hash.

For more documentation about the plugins and the variables available for configuring them, check the DOPi documentation.


