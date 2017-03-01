# DOP Future Roadmap Proposal

## Workflow Use Cases

### Initial Deployment

* Add the plan to the plan store (Make node names and hiera config known to the puppet master)
* Execute the plan (Deploy, Run, Setup[Deploy + Run])

I think this use case is completely covered and I don't see any changes needed to it in the
near future.

### Decomission Deployment

* Execute the plan (Undeploy)
* Remove the plan from the plan store (remove node info and hieradata from the puppet master)

Simple use case completely covered. No changes needed.

### Maintenance Run

* Reset plan if the stepset was already run
* Execute the plan (run --stepset foo)

There are some improvements we could do here regarding the state reset:

* Add more granular control to reset behaviour (see reset improvements section)
* Add "auto_reset: true" feature to stepset so it resets to ready automatically
  if the whole stepset is ran successfully.

### Update Deployment

Current situation:

* Update the plan (Make updated nodes and hieradata available to the puppet master)
  * (automatic) Dopi updates state and removes old nodes/steps, adds new nodes/steps 
* Run the plan (Deploy, Run, Setup[Deploy + Run])

The are several problems here.

#### Undeploy old nodes

DOPv should take care of the automatic undeployment of the old nodes. There is however
the danger that this may undeploy nodes which where unintentialy removed from the plan.

The information about what nodes where once managed exist in two places. Since the
plan store saves all plan iterations and the last deployed version is known we can
construct a hash-diff and act on those changes. Also the nodes should have an entry
in the DOPv state file.

#### Change existing nodes

DOPv should take care or changes to the node where possible, or if not redeploy the node.

The information about the changes can once again come from two places. we have the hash diff
from the update or we can try to read the current state of the deployment and act up on that.

#### Proposal

Enhancements to the DOPv refresh command and an additional command for DOPv to sync state of
the nodes (see Update/Regenerate Enhancement)

### Monitor/Regenerate Deployment

This is a new workflow which should monitor a deployment and make sure every deviation to
the plan is corrected.

* Refresh existing state of described nodes
* Regenerate desired state
  * remove old nodes present in the state file but not in the plan
  * change config to nodes not in sync or redeploy where needed

#### Proposal

This is in fact exactly the same functionality missing from the update workflow. The
implementation of those enhancements should enable this use case and fix the update
workflow. (see Update/Regenerate Enhancement)

## Update/Regenerate Enhancement

This are some initial thoughts about the implementation of this new feature

### State Update

We only just started to store the state of the remote resources. At the moment there
is a refresh command which collects ip information for the nodes which are then
used by DOPi to connect. We should store a lot more. In fact we should try to mirror
as much of the nodes hash in the plan with a hash of the actual state of the node.

This would allow us to make decisions about what to do to regenerate the state.

### Visibility

There should be "noop" capability to all the DOPv commands (deploy, undeploy, "regenerate")
to make it possible to see what will change before we actually trigger the change. This is
very important to make an informed decision about the state.

### Apply The State

A new command should be added "regenerate/sync" to implement the changes. This command should
have the usual node filters so we can limit the sync to a specific set of nodes, roles, etc.

There may be several settings for different nodes or providers which can be changed without
node redeployment. However those have to be implemented one by one. If the ability to change
it is not implemented or not possible, the default is to redeploy to reach the state.

The command should have a --destructive flag which will enable the undeployment of old
nodes and the redeployment of changed nodes which require redeployment.

The command should have a --interruptive flag which will enable changes which will
have to reboot the node and interrupt operation.

### Open Questions

How should we inform DOPi if a node is redeployed and we run from DOPv? Should
we implement this command in Dopc since it can orchestrate both tools for this kind of things?

## Reset Improvements

So far the reset was actually not part of the normal workflow (deploy, run, undeploy), but
regarded more as a tool to reset the whole thing if stuff did not work as expected. To make
it consistent to the other commands and to enable the user more granulary reset the state of
a node we should introduce the normal node selection switches to this command and also make
it possible to limit it to a stepset or even a single step.

Examples:
reset --nodes foo01.example.com,foo02.example.com
reset --stepset maint
reset --stepset maint --nodes foo01.example.com,foo02.example.com
reset --stepset maint --step 4 --nodes foo01.example.com,foo02.example.com
reset --stepset maint --step "step name" --nodes foo01.example.com,foo02.example.com

## DOP Visibility Endpoints

There are two very distinct types of information about a plan:

* Logfile/stream of an ongoing operation.
* State of a plan (nodes/steps)

There should be endpoints in DOPc to get this information with some amount of controll
of the granularity.

### Log files

Those are tied to executions and are therefor available over this endpoint. The logfiles
should be created by the tools DOPv and DOPi and DOPc should only have to load them from
the disk from the right place.

/var/log/dop/<plan_name>/<timestamp|exec_id>/all.log
/var/log/dop/<plan_name>/<timestamp|exec_id>/foo01.example.com

We should be able to change the verbosity level lower than the stored logs to filter out
noise automatically. (?? client or server side ??)

/executions/<id>/log
/executions/<id>/log?node=foo01.example.com

#### Open Questions

Can we implement this with websockets to get a stream of data maybe if we add the param 'stream'?

### State

Currently we only have the state of DOPi. But since we now start to implement more state
info for DOPv we should be able to create some form of state endpoint which delivers
information from both tools.

/executions/plan/state

Maybe add more granular access in the future if needed. But since the state is a hash
this is not really needed initially

#### Open Questions

We should be able to subscribe a client for changes with a websocket.

## DOPc-client Library / CLI

Currently DOPc is a POC CLI client for 


## DOPi enhancements

### Exec on other node

### SSH Proxy

### Enhance puppet plugin
