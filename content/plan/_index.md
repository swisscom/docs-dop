+++
date = "2017-04-18T11:47:41+02:00"
icon = "<b>2. </b>"
title = "Plan"
weight = 0

+++

# DOP Plan Format Overview

A DOP plan is a description of a deployment of multiple a multi-node application. It contains all the information
needed to deploy the nodes and run puppet in the right order on all the different nodes/roles.

Each plan has some global settings and a couple of sections which each describe a different aspect of the plan. The
sections are all specified in their own topic in this chapter. Here is a short overview.

## Credentials

The credentials hash specifies all the credentials needed to login to API endpoints, the nodes and also to configure
during deployment. You can for example define a root credential and use it in the node section to configure the password
during staging of the node (if the provider supports that) and use the same credential later in the steps to login via ssh
or winrm.

## Hooks

TBD

## Infrasturctures

The infrastructure hash defines all your cloud infrastructure endpoints and some configuration needed in the deployment,
like networks, afinity_groups, etc.

## Nodes

The nodes hash describes all the nodes in your deployment. It references a infrastructure on which you want to deploy
your node and gives details about the size, image etc of the virtual machine which will be created by DOPv.

## Configuration

Here you can place hiera data for your nodes. If you use the DOP hiera plugin in your puppet master it will read the
configuration from this place. The hash has to resemble the hierarchy you have defined in your hiera config.

## Steps

Steps describes one or multiple sets of steps to execute in order. Each step defines a set of nodes where it will run
on and you can limit on how many nodes it will run in parallel. Each step can also have multiple commands which it will
run in order.

There are different plugins for the most commands needed in bootstraping a node, but you can also just run a custom
command over ssh, winrm or mcollective.
