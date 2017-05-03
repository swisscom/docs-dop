+++
date = "2017-04-18T13:37:23+02:00"
title = "Steps"
toc = true
weight = 19

+++

The steps array is a list of commands that have to be executed in the correct order. Each element in the
array contains a hash of settings which describe the step, the nodes involved and the command to execute.

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

### Name

| Key  | Allowed Values | Default | Since Version |
|------|----------------|---------|---------------|
| name | String         | nil     | 0.1           |

The name is just an identifier for the step. You should chose a name that best describes what you are doing in this step.

### Node selectors

A step always runs for a collection of nodes. This can be a single node, an array of hand picked nodes, all
nodes in a role or various other possibilities. To make it easy to select the right nodes, DOP provides a couple of
node selectors.

#### nodes (optional)

| Key   | Allowed Values   | Default | Since Version |
|-------|------------------|---------|---------------|
| nodes | Array of Strings | nil     | 0.1           |

The nodes node-selector directly selects nodes based on their names. This can be a simple list of names but it may also contain Regular expression patterns to select nodes. In addition the keywor "all" may be used to select all of the nodes in the plan.

If an entry starts and ends with a '/' DOPi will interpret the string as a regular expression.

Include a simple list of nodes:

    steps:
      - name: "exec on some nodes"
        nodes:
          - linux01.example.com
          - linux02.example.com

Include nodes based on a regular expression:

    steps:
      - name: "exec on some nodes"
        nodes:
          - /linux\d+.example.com/

#### roles (optional)

| Key   | Allowed Values   | Default | Since Version |
|-------|------------------|---------|---------------|
| roles | Array of Strings | nil     | 0.1           |

This will include all the nodes with a certain role to a step.

roles and nodes can be mixed, dop_common will simply merge the list of nodes. However there has to be at least
one node in the resulting list in every step.

As in the node selector, if an entry starts and ends with a '/' DOPi will interpret the string as a regular expression.

roles is basically just a special case for nodes_by_config with the roles variable. But it will do some additional
checks and you can also set a default value for the role.

#### nodes_by_config (optional)

| Key             | Allowed Values   | Default | Since Version |
|-----------------|------------------|---------|---------------|
| nodes_by_config | Array of Strings | nil     | 0.3           |

Include nodes to a step by specific configuration values which are resolved over hiera.

Example:

    configuration:
      nodes:
        'mysql01.example.com':
          'my_alias': 'database_01'

    steps:
      - name: 'include by config'
        nodes_by_config:
          'my_alias': 'database_01'
        command: ssh_run_puppet

If the value of the config variable is an array it will check each value in that array. You can also use patterns the way you can use them in the other selectors.

Example:

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

#### exclude_nodes, exclude_roles, exclude_nodes_by_config (all optional)

| Key                     | Allowed Values   | Default | Since Version |
|-------------------------|------------------|---------|---------------|
| exclude_nodes           | Array of Strings | nil     | 0.3           |
| exclude_roles           | Array of Strings | nil     | 0.3           |
| exclude_nodes_by_config | Array of Strings | nil     | 0.3           |

This selectors work exactly like the normal ones as they will assemble a list of nodes from the given criteria.
Thiose nodes will then be subtracted from the results of the other selectors.

This is usefull if you for example want to include all nodes of a role, but exclude one or a couple of nodes:

    steps:
      - name: "run on all puppetmasters but master02"
        roles:
          - puppetmaster
        exclude_nodes:
          - master02.example.com

### Commands

| Key      | Allowed Values    | Default | Since Version |
|----------|-------------------|---------|---------------|
| commands | String,Hash,Array | nil     | 0.1           |

The commands can either be directly a plugin name if a single plugin and no parameters, a single command
hash which will be passed to the plugin or an array of command hashes if multiple commands have to be
executed in a single step. The only fixed variable here is the **plugin** variable. The rest of the variables
in the command hash depends on the plugin in use and how it will parse the hash.

Simple command without parameters:

    steps:
      - name: 'simple puppet run'
        nodes: 'all'
        commands: 'ssh/puppet_run'

Command with parameters:

    steps:
      - name: 'simple puppet run'
        nodes: 'all'
        commands:
          plugin: 'ssh/puppet_run'
          arguments: '--noop'

Multiple commands in an array:

    steps:
      - name: 'simple puppet run'
        nodes: 'all'
        commands:
          - plugin: 'ssh/custom'
            exec: 'yum -y update'
          - 'ssh/reboot'
          - 'ssh/puppet_run'

Parameters for the various plugins are documented in the command plugin section.
