
### parse_output (optional)

| Key          | Allowed Values | Default | Since Version |
|--------------|----------------|---------|---------------|
| parse_output | Hash           | {}      | 0.1           |

Here you can define patterns that match against the output of the command plugin
and flag certain lines as errors or warnings. The parse_output key should contain
a hash with two keys, 'error' and 'warning' which each can contain an array of
regular expressions.

To for example flag all lines starting with "Error:" as errors define:

    parse_output:
      error:
        - "^Error"

### fail_on_warning (optional)

| Key             | Allowed Values | Default | Since Version |
|-----------------|----------------|---------|---------------|
| fail_on_warning | true,false     | false   | 0.1           |

Set this to true if you want to threat warnings in the output as errors. This is
useful if the plugin already specifies the parsing patterns but your need to stop
the run on warnings.

