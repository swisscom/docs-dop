+++
date = "2017-05-03T16:01:12+02:00"
title = "Overview"
toc = true
weight = 0

+++

Each plugin uses a specific connector to connect to a node and execute the commands. This is specified by the fist part
of the command plugin name, the second part is the name of the function: connector/function

DOP does not aspire to be a configuration management tool on its own and therefor the amount of plugins and possibilities
are somewhat limited. The idea is to give you the tools to run puppet in the right order and maybe place or change some
needed files to make sure the node can connect to the master.

There are currently three supported connectors: winrm, ssh and mcollective.

Winrm and ssh both use the credentials hash for authentication. For Mcollective you need to point to the mco configuration
file in your dop configuration file.

## credentials for login

The plugins which use the ssh and winrm connector can use credentials from the credentials hash in the plan to login.
More information about the credentials hash itself can be found in the plan chapter under credentials.

Here is a simple example of a ssh username/password login

    credentials:
      'linux_staging_login':
        type: :username_password
        username: 'root'
        password: 'foo'

    steps:
      - name: 'set ssh login credentials'
        command:
          plugin: 'ssh/custom'
          credentials: 'linux_staging_login'
          exec: 'env'

You can also specify multiple credentials and the connectors will try each one
of them in turn to login to the node.

    credentials:
      'linux_staging_login':
        type: :username_password
        username: 'root'
        password: 'foo'
      'linux_prod_login':
        type: :ssh_key
        private_key: '/home/root/.ssh/id_dsa'

    steps:
      - name: 'set ssh login credentials'
        command:
          plugin: 'ssh/custom'
          credentials:
            - 'linux_staging_login'
            - 'linux_prod_login'
          exec: 'env'

This can be set in each plugin (don't forget the validator plugins) or via
the set_plugin_defaults hash. The plugin defaults will be preserved for
subsequent steps until it is altered or deleted. More about this feature in the next section.

    credentials:
      'linux_staging_login':
        type: :username_password
        username: 'root'
        password: 'foo'
      'linux_prod_login':
        type: :ssh_key
        private_key: '/home/root/.ssh/id_dsa'

    steps:
      - name: 'set ssh login credentials'
        set_plugin_defaults:
          - plugins: '/^ssh/'
            :credentials:
              - 'linux_staging_login'
              - 'linux_prod_login'
        command:
          plugin: 'ssh/custom'
          exec: 'env'

The login via password for the ssh connector will need the program sshpass to be installed on the
machine DOPi is running on. DOPi will display a warning if you specify a username password credential
but sshpass is not installed.

## Plugin Defaults

Sometimes you have some settings, like the credentials settings for connectors for example, which you
have to specify on every single plugin. To make this easier you can set plugin defaults.

The defaults will be active from the step where you specified for all following steps. They can then be
overwritten in later steps by explicitely defining the setting or by defining new defaults. It is also
possible to remove the defaults again.

### set_plugin_defaults (optional)

| Key                 | Allowed Values    | Default | Since Version |
|---------------------|-------------------|---------|---------------|
| set_plugin_defaults | Array             | nil     | 0.4           |

With "set_plugin_defaults" it is possible to specify some default values for plugin configuration which will persist
over subsequent runs.

The settings are node specific, so they will only be set for the nodes in your step. You can select the plugins for which
this applies with a list or with regular expressions.

Direct settings in the plugins will always overwrite the defaults.

Example:

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

{{% notice info %}}
The keys you want to set have to be ruby symbols. This is a current limitation of the way the parser is
implemented and may change in the future
{{% /notice %}}


### delete_plugin_defaults (optional)

| Key                    | Allowed Values    | Default | Since Version |
|------------------------|-------------------|---------|---------------|
| delete_plugin_defaults | Array             | nil     | 0.4           |

There are several possibilities how you can remove plugin settings with "delete_plugin_defaults"

Example:

    steps:
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

{{% notice info %}}
The keys you want to set have to be ruby symbols. This is a current limitation of the way the parser is
implemented and may change in the future
{{% /notice %}}


## Verify Commands (optional)

| Key             | Allowed Values    | Default | Since Version |
|-----------------|-------------------|---------|---------------|
| verify_commands | String,Hash,Array | []      | 0.4           |

The purpose of a verify command is to check if the real command has to be ran. This makes it possible
to write idempotent step sets which can be executet multiple times to make sure all nodes are deployed
correctly.

You can define them exactly like you would define a normal command. It accepts a simple string for plugins
without parameters, a command hash or an array of simple strings and command hashes.

In order to skip the step, all the verify commands have to return in the state "done".

### Verify after run (optional)

| Key              | Allowed Values    | Default | Since Version |
|------------------|-------------------|---------|---------------|
| verify_after_run | true,false        | false   | 0.2           |

To make sure the command did all you expected it to do you can also enable this option. If enabled
it will run the verify commands after the real command again and mark the command as failed if
they are not successful.

This is disabled by default.

## Plugin Timeout (optional)

| Key            | Allowed Values    | Default | Since Version |
|----------------|-------------------|---------|---------------|
| plugin_timeout | Numeric           | 300     | 0.1           |

Dopi will about a command automatically if the plugin timeout is reached. This default may vary for
different plugins. If you have a long running command you may increase this. The numeric value is the
amount of seconds it will wait.

## Title (optional)

| Key   | Allowed Values    | Default     | Since Version |
|-------|-------------------|-------------|---------------|
| title | String            | plugin name | 0.11          |

A more telling title for the command which is shown in the status output. This is useful if you
have multiple commands in a step.
