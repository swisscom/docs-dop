+++
date = "2017-04-18T15:32:26+02:00"
title = "Hooks"
toc = true
weight = 13

+++

Hooks can be defined as a hash of programs that will be called before and/or
after a node is created and/or updated and/or destroyed.

A hook consists of an array of strings that represent paths to programs to be
be executed at particular deployment stage.

Following is a list of supported hooks:
 - `pre_create_vm` - a set of programs executed before node creation.
 - `post_create_vm` - a set of programs executed after node creation.
 - `pre_update_vm` - a set programs executed before node update.
 - `post_update_vm` - a set of programs executed after node is update.
 - `pre_destroy_vm` - a set of programs executed before node removal.
 - `post_destroy_vm` - a set of programs executed after node removal.

__IMPORTANT__:
 - FQDN of the given node is passed to program(s) as the first argument.
 - `0` or `1` is passed to program(s) as the second argument. More
   specifically, `0` is passed if there were no changes of the node during
   deployment, while `1` indicates that there were some changes.
 - Programs are executed in the order of their definition in the deployment
   plan.
 - Each hook type definition is optional, however, `hooks` hash must contain
   at least one hook type.

Following is an example of hooks definition

    hooks:
      pre_create_vm:
        - /path/to/pre_create_program_1
        - /path/to/pre_create_program_2
      post_create_vm:
        - /path/to/post_create_program_1
      pre_update_vm:
        - /path/to/pre_update_program_1
      post_update_vm:
        - /path/to/post_update_program_2
      pre_destroy_vm:
        - /path/to/pre_destroy_program_1
        - /path/to/pre_destroy_program_2
        - /path/to/pre_destroy_program_3
      post_destroy_vm:
        - /path/to/post_destroy_program_1

