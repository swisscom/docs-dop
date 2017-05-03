+++
date = "2017-05-03T15:42:43+02:00"
title = "About"
toc = true
weight = 5

+++

## What is DOP? 

DOP is a collection of ruby gems and rails applications which each do it's part to
automate your deployment. Each component solves a separate problem and can be used on
it's own. But they can all be combinet to completely automate the deployment of your
infrastructure over a single API.

## What it isn't?

DOP is not another configuration management tool. The goal is not to replace Puppet but to
bridge the gap from describing your infrastructure to deploying them in your IAAS platform.

## How does it work?

DOP has it's own plan format with which you describe your infrastructure endpoints, nodes you
want to depoly, credentials, configuration data and additional steps required to deploy your
applications.

There are four main components in the DOP collection:

### DOPv

DOPv is the virtualization orchestrator. It is based on fog and will connect to your IAAS
providers to deploy the nodes you described in your plan.

### DOPi

DOPi is the inner orchestrator. It will connect to your machine over ssh, winrm or mcollective
and execute basic commands, run scripts or puppet in a defined order on subsets of nodes. It can
be used to enforce puppet runs in a fixed order among nodes or for tasks like upgrading your
complex multinode application in a automated way.

### DOPc

DOPc is a Rails based application which combines DOPv and DOPi in a simple API which you can use
to manage and run your deployments.

#### DOPc-client

DOPc-client is a ruby client for the API ready to be integrated into whatever other automation
gear you have ready to template the DOP plans and reach new levels of godlike automation powers.

### Common

The fourth component is a library where all the common bits and pieces are, like the plan parser
or the plan store code.

It also has some smaller components which are currently mainained as part of the dop_common code
base

#### Hiera Plugin

DOP comes with its own hiera plugin so you can put your hiera configuration for your nodes where
it belongs, in the plan.

#### Autosign Helper

This is a simple script for the puppetmaster to check if a node is in a plan and can be automatically
signed.
