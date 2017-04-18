+++
date = "2017-04-18T13:41:46+02:00"
title = "Global Settings"
toc = true
weight = 11

+++

Global settings are on the top level of a plan. This are settings which affect the whole plan or represent
default values which can be changed in other places.

## name (optional)

| Key  | Allowed Values | Default           | Since Version |
|------|----------------|-------------------|---------------|
| name | /[\w-]+/       | (sha1 of content) | 0.1           |

The name of the plan is the handle under which dop will store it in the plan cache. This is important so it can later
identify the right plan if you want to update the plan or run it from the plan store.

If you don't specify a plan name dop will calculate the sha1 of the plan content and use that instead. This also means
you will not be able to update the plan.


## Max in flight (optional)

| Key           | Allowed Values | Default           | Since Version |
|---------------|----------------|-------------------|---------------|
| max_in_flight | -1..inf        | 3                 | 0.1           |

The amount of nodes DOPi will be executing commands on and DOPv will depoly in parallel.

There are also two special values:

- The value "0" will disable thread spawning for debug purposes.
- The value "-1" will spawn as many threads as there are nodes.

This option can be overwritten on step level for DOPi and on infrastructure level for DOPv

## Max per role (optional)

| Key          | Allowed Values | Default           | Since Version |
|--------------|----------------|-------------------|---------------|
| max_per_role | -1..inf        | -1                | 0.11          |

The amount of nodes per role DOPi will execute in parallel.

There are also two special values:

- The values "0" and "-1" will make DOPi ignore the roles.

This option can be overwritten on step level

## Canary host (optional)

| Key         | Allowed Values | Default           | Since Version |
|-------------|----------------|-------------------|---------------|
| canary_host | true,false     | false             | 0.3           |

If this flag is set to true DOPi will randomly choose one host and apply the step in a first round only to this host and only run the others in parallel, once this step succeeded.

This option can be overwritten on step level

