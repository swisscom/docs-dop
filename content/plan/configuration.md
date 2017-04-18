+++
date = "2017-04-18T13:37:15+02:00"
title = "Configuration"
toc = true
weight = 18

+++

The configurations hash contains the hiera configuration for the deployment. The sub-hashes map the hierarchy
defined in the hiera.conf.

Example:

    hosts/foo/mynode.example.com

    Maps to:

    configuration:
      hosts:
        foo:
          mynode.example.com

You can use all the hierarchy levels you like, but be aware that the hiera plugin will only look them up if
the node it is looking the key up for is in the plan. Defaults which are relevant to your deployment should
therefor be set inside the plan, while global defaults should go into your global hiera data.

## Examples

### Nodes

Usually there is a nodes or hosts layer in the hierarchy and it can be used to assigns variables to an individual node or to a selected set of nodes. Here are some examples:

    configuration:
      nodes:
        mysql01.example.com:
          role: mysql
          my_var:  my_value
        web04.example.com:
          role: httpd_special

The only relevant setting here for DOP is the role variable. The name of the role variable can be configured but defaults to 'role'. If hiera is configured and activated, then dop will take the role specified in hiera if found.

During validation the plan is not in the plan cache the role can not be resolved by hiera directly. For this case, DOP has an internal resolution mechanism in which it parses the configuration hash to get the role variable.

1. Hiera
2. Internal Lookup (for validation)
4. Default

A node always needs a role. If the parser finds no value for one of the specified nodes in the plan file and if no default is set, then DOP will throw an error.

