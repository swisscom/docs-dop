+++
date = "2017-04-18T14:48:44+02:00"
title = "Pre-processor"
toc = true
weight = 10

+++

The pre-processor is something which only applies if you load plans from a file and
not if you use dop as a library and feed the plan as a hash. The pre-processor renders
the plan before it gets loaded by the parser.

The only feature implemented at the moment is includes, which allows you to split your
plans into multiple files and possible reuse parts between different plans.

In the future this may also be used to do templating, but this is not implemented yet.

## Includes

From DOPi >= 0.16, DOPv >= 0.8 on the DOP plan format supports includes. Before
DOP will parse the plan there is now a preprocessor step which will replace all
"include: <file>" statements with the content of the file.

Example:

    name: 'my_plan'
    include: '/etc/dop_deployments/credentials.yaml'
    include: '/etc/dop_deployments/infrastructure.yaml'
    include: 'nodes.yaml'

    steps:
      default:
        include: 'openstack_deploy_steps.yaml'
      patch:
        include: '/etc/dop_deployments/generic_patch_maintenance_steps.yaml'

The file path can either be a relative or an absolute path. Relative paths mean
that the preprocessor will look for the file relative to the path of the file
which is including it. Included files can itself include files.

The content of the included file will be placed at the position where the include
statement begins with the exact same identation for every line.

It is important to note, that since the pre-processor is running before the YAML
parser, that in case of DOPc all the files must be on the client side. It is not
possible to reference files on the DOPc server side. The DOPc client always has
to send a completely rendered plan to the server.

In order to test the result localy you can show the result of the pre-processor
with the following command:

    dopc-client render path/to/my/plan.yaml
