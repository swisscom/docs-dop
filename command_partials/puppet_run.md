
### rerun_on_change (optional)

| Key             | Allowed Values | Default | Since Version |
|-----------------|----------------|---------|---------------|
| rerun_on_change | true,false     | false   | 0.18.0        |

If the return code of puppet indicates that there where changes the plugin
will do a rerun.

The purpose of this setting is to allow an easy workaround if the puppet catalog
needs to run multiple times to fully reach the desired state. Usually this should
be fixed in the puppet code and DOPi will display a warning if multiple reruns are
required. Make sure you also set the max_rerun to allow for the desired amount of
reruns.

### rerun_on_error (optional)

| Key            | Allowed Values | Default | Since Version |
|----------------|----------------|---------|---------------|
| rerun_on_error | true,false     | false   | 0.18.0        |

Similar to rerun_on_change, this setting allows for automatic reruns of puppet
if the return value of the agent indicates that there where errors in the Puppet
run or if there where error messages during the run.

The purpose of this setting is to allow an easy workaround if there are for example
still ordering errors on the catalog and a rerun will reach the desired state.
DOPi will in all cases display a warning to indicate that a rerun happened.

### max_rerun (optional)

| Key       | Allowed Values | Default | Since Version |
|-----------|----------------|---------|---------------|
| max_rerun | > 0            | 1       | 0.18.0        |

The maximum amount of puppet reruns the plugin will do if either 'rerun_on_error' or
'rerun_on_change' is true.

### wait_if_already_running (optional)

| Key                     | Allowed Values | Default | Since Version |
|-------------------------|----------------|---------|---------------|
| wait_if_already_running | true,false     | true    | 0.18.0        |

If true, the plugin will wait if puppet is already running on the instance. This is
often the case if puppet runs as part of the deployment or if the agent is running
in daemon mode.

