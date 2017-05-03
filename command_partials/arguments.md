
### arguments (optional)

| Key       | Allowed Values    | Default | Since Version |
|-----------|-------------------|---------|---------------|
| arguments | String,Hash,Array | ""      | 0.1           |

The arguments for the command. This can be set by a string as an array or
as a hash. All the elements of the hash and the array will be flattened
and joined with a space.

The following examples will result all in the same arguments string:

    arguments: "--name foo -s somearg"

    arguments:
      '--name': 'foo'
      '-s':
      'somearg':

    arguments:
      - '--name foo'
      - '-s'
      - 'somearg'

